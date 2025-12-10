# Part 6: Services & Integrations
## File Uploads, Emails, and AI Integration

> **Goal:** Integrate external services for complete functionality  
> **Time:** 6-8 hours  
> **Prerequisites:** Completed Parts 1-5

[← Back to Part 5](./05-caching-and-performance.md) | [Back to Index](./README.md) | [Next: Part 7 →](./07-production-and-deployment.md)

---

## What We'll Learn

✅ **Object storage concepts** - Why separate storage?  
✅ **Cloudinary** - Free image uploads (25GB!)  
✅ **Image optimization** - Automatic processing  
✅ **Email systems** - Transactional emails explained  
✅ **Resend** - Modern email API (100/day free)  
✅ **React Email** - Beautiful email templates  
✅ **AI integration** - OpenRouter (free models)  
✅ **Background jobs** - Cron jobs and scheduling  

---

## The Problem: File Storage

### Why Not Store Files in Database? 🔴

**Scenario:** Users upload article images

**Option 1: Store in Database (Bad Idea)**
```sql
CREATE TABLE articles (
  id UUID,
  title TEXT,
  content TEXT,
  image BYTEA  -- ❌ Binary data in database
);
```

**Problems:**
1. **Database bloat** - Images are huge (1MB+)
2. **Slow queries** - Fetching large BLOBs is slow
3. **Expensive** - Database storage costs more
4. **No CDN** - Can't cache images at edge
5. **Backup overhead** - Backups become massive

**Option 2: Store on Server Filesystem (Also Bad)**
```typescript
// ❌ Don't do this
fs.writeFileSync(`/uploads/${filename}`, file);
```

**Problems:**
1. **Serverless incompatible** - Filesystem resets
2. **No persistence** - Files disappear on redeploy
3. **No scaling** - Single server storage
4. **No CDN** - Direct server requests

**Option 3: Object Storage (✅ The Right Way)**
```
User uploads image
└─> Send to Cloudinary
    ├─> Stores in cloud storage
    ├─> Optimizes (compress, resize)
    ├─> Serves via CDN
    └─> Returns URL

Database stores URL only:
{
  imageUrl: "https://res.cloudinary.com/..."
}
```

**Benefits:**
- ✅ Unlimited storage (scales automatically)
- ✅ Global CDN (fast worldwide)
- ✅ Automatic optimization
- ✅ Image transformations
- ✅ Backup and redundancy
- ✅ Free tier (25GB!)

---

## Understanding Object Storage

### The Mental Model

Think of it like **Amazon S3 for images**:

```
Traditional File Server:
├─> You manage server
├─> Limited storage
├─> Single location
└─> Slow for global users

Object Storage (Cloudinary):
├─> They manage infrastructure
├─> Unlimited storage (pay for what you use)
├─> Global CDN (20+ locations)
└─> Fast everywhere
```

### How It Works

```
1. User selects image
   └─> File in browser

2. Frontend uploads to Cloudinary
   └─> POST to cloudinary.com/upload
   └─> Returns: { url, public_id }

3. Save URL to database
   └─> article.imageUrl = url

4. Display image
   └─> <img src={article.imageUrl} />
   └─> Served from CDN ⚡
```

---

## Setting Up Cloudinary

### Why Cloudinary?

**Comparison:**

| Service | Free Tier | Features | No Card |
|---------|-----------|----------|---------|
| AWS S3 | 5GB | Basic | ❌ |
| Vercel Blob | 1GB | Good | ❌ |
| ImageKit | 20GB | Great | ✅ |
| **Cloudinary** | **25GB** | **Amazing** | **✅** |

**Cloudinary wins:**
- ✅ 25GB storage free
- ✅ 25GB bandwidth/month
- ✅ Automatic optimization
- ✅ Image transformations
- ✅ Video support
- ✅ No credit card required

### Step 1: Create Account

1. Go to [cloudinary.com](https://cloudinary.com)
2. Sign up (no credit card!)
3. Verify email

### Step 2: Get Credentials

Dashboard shows:

```
Cloud Name: your-cloud-name
API Key: 123456789012345
API Secret: abcdefghijklmnopqrstuvwxyz
```

### Step 3: Add to Environment

```bash
# .env.local
CLOUDINARY_CLOUD_NAME="your-cloud-name"
CLOUDINARY_API_KEY="123456789012345"
CLOUDINARY_API_SECRET="abcdefghijklmnopqrstuvwxyz"
```

### Step 4: Initialize Cloudinary

Create `src/lib/cloudinary.ts`:

```typescript
import { v2 as cloudinary } from 'cloudinary';

// Configure Cloudinary
cloudinary.config({
  cloud_name: process.env.CLOUDINARY_CLOUD_NAME,
  api_key: process.env.CLOUDINARY_API_KEY,
  api_secret: process.env.CLOUDINARY_API_SECRET,
});

export { cloudinary };
```

---

## Implementing Image Upload

### Upload Flow

```
Client Component (Browser)
├─> User selects file
├─> Preview image
└─> Submit form
    ↓
Server Action
├─> Receive file
├─> Upload to Cloudinary
├─> Get URL back
├─> Save to database
└─> Return article
```

### Create Upload Function

Update `src/lib/cloudinary.ts`:

```typescript
export async function uploadImage(file: File): Promise<string> {
  try {
    // Convert File to base64
    const bytes = await file.arrayBuffer();
    const buffer = Buffer.from(bytes);
    const base64 = buffer.toString('base64');
    const dataURI = `data:${file.type};base64,${base64}`;
    
    // Upload to Cloudinary
    const result = await cloudinary.uploader.upload(dataURI, {
      folder: 'wikiapp/articles',  // Organize in folders
      resource_type: 'auto',       // Auto-detect type
      transformation: [
        { width: 1200, crop: 'limit' },  // Max width
        { quality: 'auto:good' },        // Auto quality
        { fetch_format: 'auto' },        // Auto format (WebP)
      ],
    });
    
    return result.secure_url;
  } catch (error) {
    console.error('Cloudinary upload failed:', error);
    throw new Error('Failed to upload image');
  }
}
```

**Understanding transformations:**

```typescript
transformation: [
  { width: 1200, crop: 'limit' },
  // ↑ If image > 1200px wide, resize to 1200px
  // If smaller, leave as-is
  
  { quality: 'auto:good' },
  // ↑ Cloudinary picks optimal quality
  // Balances size vs quality
  
  { fetch_format: 'auto' },
  // ↑ Serve WebP to modern browsers
  // JPEG to old browsers
]
```

**Result:**
- Original: 5MB JPEG
- Optimized: 200KB WebP
- 96% smaller! ⚡

### Update Article Actions

```typescript
// src/lib/actions/articles.ts
'use server';

import { requireAuth } from '@/lib/auth';
import { uploadImage } from '@/lib/cloudinary';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';

export async function createArticle(formData: FormData) {
  const user = await requireAuth();
  
  const title = formData.get('title') as string;
  const content = formData.get('content') as string;
  const imageFile = formData.get('image') as File;
  
  // Validate
  if (!title || !content) {
    throw new Error('Title and content required');
  }
  
  // Upload image if provided
  let imageUrl: string | null = null;
  if (imageFile && imageFile.size > 0) {
    // Check file size (max 5MB)
    if (imageFile.size > 5 * 1024 * 1024) {
      throw new Error('Image must be less than 5MB');
    }
    
    // Check file type
    if (!imageFile.type.startsWith('image/')) {
      throw new Error('File must be an image');
    }
    
    imageUrl = await uploadImage(imageFile);
  }
  
  // Create article
  const [article] = await db.insert(articles)
    .values({
      title,
      content,
      imageUrl,
      authorId: user.id,
    })
    .returning();
  
  revalidatePath('/articles');
  return article;
}
```

### Create Form Component

Create `src/components/article-form.tsx`:

```typescript
'use client';

import { useState } from 'react';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Textarea } from '@/components/ui/textarea';
import { Label } from '@/components/ui/label';
import { Alert, AlertDescription } from '@/components/ui/alert';
import { createArticle } from '@/lib/actions/articles';
import { Upload, X } from 'lucide-react';

export function ArticleForm() {
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [imagePreview, setImagePreview] = useState<string | null>(null);
  const [error, setError] = useState<string | null>(null);
  
  function handleImageChange(e: React.ChangeEvent<HTMLInputElement>) {
    const file = e.target.files?.[0];
    if (!file) return;
    
    // Validate size
    if (file.size > 5 * 1024 * 1024) {
      setError('Image must be less than 5MB');
      return;
    }
    
    // Validate type
    if (!file.type.startsWith('image/')) {
      setError('File must be an image');
      return;
    }
    
    // Create preview
    const reader = new FileReader();
    reader.onloadend = () => {
      setImagePreview(reader.result as string);
      setError(null);
    };
    reader.readAsDataURL(file);
  }
  
  function clearImage() {
    setImagePreview(null);
    const input = document.getElementById('image') as HTMLInputElement;
    if (input) input.value = '';
  }
  
  async function handleSubmit(formData: FormData) {
    setIsSubmitting(true);
    setError(null);
    
    try {
      await createArticle(formData);
      // Redirect handled by server action
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Failed to create');
    } finally {
      setIsSubmitting(false);
    }
  }
  
  return (
    <form action={handleSubmit} className="space-y-6">
      {error && (
        <Alert variant="destructive">
          <AlertDescription>{error}</AlertDescription>
        </Alert>
      )}
      
      <div>
        <Label htmlFor="title">Title</Label>
        <Input
          id="title"
          name="title"
          placeholder="Enter article title"
          required
          disabled={isSubmitting}
        />
      </div>
      
      <div>
        <Label htmlFor="image">Featured Image (optional, max 5MB)</Label>
        {imagePreview ? (
          <div className="relative mt-2">
            <img
              src={imagePreview}
              alt="Preview"
              className="max-w-md rounded-lg"
            />
            <Button
              type="button"
              variant="destructive"
              size="sm"
              className="absolute top-2 right-2"
              onClick={clearImage}
            >
              <X className="h-4 w-4" />
            </Button>
          </div>
        ) : (
          <div className="border-2 border-dashed rounded-lg p-8 text-center mt-2">
            <Upload className="h-8 w-8 mx-auto mb-2 text-muted-foreground" />
            <Input
              id="image"
              name="image"
              type="file"
              accept="image/*"
              onChange={handleImageChange}
              disabled={isSubmitting}
              className="max-w-xs mx-auto"
            />
            <p className="text-sm text-muted-foreground mt-2">
              PNG, JPG, GIF up to 5MB
            </p>
          </div>
        )}
      </div>
      
      <div>
        <Label htmlFor="content">Content</Label>
        <Textarea
          id="content"
          name="content"
          placeholder="Write your article..."
          className="min-h-[400px] font-mono"
          required
          disabled={isSubmitting}
        />
      </div>
      
      <Button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Creating...' : 'Create Article'}
      </Button>
    </form>
  );
}
```

---

## Email System with Resend

### Why Send Emails?

**Use cases in WikiApp:**
- Welcome emails (new users)
- Milestone celebrations (1000 views!)
- Password resets
- Notifications (new comments)
- Weekly digests

### Email Challenges

**Traditional Email (SMTP):**
```
Problems:
├─> Complex setup (SMTP server)
├─> Spam filters (low deliverability)
├─> No tracking (open rates, clicks)
├─> Manual HTML templates
└─> Hard to test
```

**Modern Email APIs:**
```
Solutions:
├─> Simple API (just HTTP request)
├─> High deliverability (managed IPs)
├─> Built-in tracking
├─> Template support
└─> Easy testing
```

### Why Resend?

| Service | Free Tier | Features | React Support |
|---------|-----------|----------|---------------|
| SendGrid | 100/day | Good | ❌ |
| Mailgun | 100/day | Good | ❌ |
| **Resend** | **100/day** | **Great** | **✅** |

**Resend wins:**
- ✅ 100 emails/day free
- ✅ React Email support (JSX templates!)
- ✅ Great DX
- ✅ No credit card required

### Step 1: Create Resend Account

1. Go to [resend.com](https://resend.com)
2. Sign up (no credit card!)
3. Generate API key

### Step 2: Add to Environment

```bash
# .env.local
RESEND_API_KEY="re_abc123xyz..."
```

### Step 3: Initialize Resend

Create `src/lib/email.ts`:

```typescript
import { Resend } from 'resend';

export const resend = new Resend(process.env.RESEND_API_KEY);

// For testing: can only send to your email
export const EMAIL_FROM = 'WikiApp <onboarding@resend.dev>';

// For production: add custom domain
// export const EMAIL_FROM = 'WikiApp <noreply@yourdomain.com>';
```

---

## React Email Templates

### Why React Email?

**Traditional HTML emails:**
```html
<!-- Painful! -->
<table width="600" cellpadding="0" cellspacing="0">
  <tr>
    <td style="padding: 20px; background: #f0f0f0;">
      <h1 style="color: #333; font-size: 24px;">Hello!</h1>
    </td>
  </tr>
</table>
```

**React Email:**
```tsx
// Beautiful! ✨
<Html>
  <Container>
    <Heading>Hello!</Heading>
    <Text>Your article reached 1000 views!</Text>
  </Container>
</Html>
```

### Create Email Template

Create `src/emails/milestone-email.tsx`:

```typescript
import {
  Body,
  Container,
  Head,
  Heading,
  Html,
  Link,
  Preview,
  Text,
} from '@react-email/components';

interface MilestoneEmailProps {
  authorName: string;
  articleTitle: string;
  articleUrl: string;
  views: number;
}

export function MilestoneEmail({
  authorName,
  articleTitle,
  articleUrl,
  views,
}: MilestoneEmailProps) {
  return (
    <Html>
      <Head />
      <Preview>Your article reached {views} views! 🎉</Preview>
      <Body style={main}>
        <Container style={container}>
          <Heading style={h1}>🎉 Congratulations!</Heading>
          
          <Text style={text}>
            Hi {authorName},
          </Text>
          
          <Text style={text}>
            Great news! Your article{' '}
            <strong>"{articleTitle}"</strong> has reached{' '}
            <strong>{views.toLocaleString()} views</strong>!
          </Text>
          
          <Text style={text}>
            This is an amazing milestone. Keep creating great content!
          </Text>
          
          <Link href={articleUrl} style={button}>
            View Article
          </Link>
          
          <Text style={footer}>
            WikiApp - Building Knowledge Together 📚
          </Text>
        </Container>
      </Body>
    </Html>
  );
}

// Styles
const main = {
  backgroundColor: '#f6f9fc',
  fontFamily: '-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif',
};

const container = {
  backgroundColor: '#ffffff',
  margin: '0 auto',
  padding: '20px 0 48px',
  maxWidth: '600px',
};

const h1 = {
  color: '#333',
  fontSize: '28px',
  fontWeight: 'bold',
  margin: '40px 0',
  textAlign: 'center' as const,
};

const text = {
  color: '#333',
  fontSize: '16px',
  lineHeight: '26px',
  margin: '16px 0',
};

const button = {
  backgroundColor: '#000',
  borderRadius: '5px',
  color: '#fff',
  display: 'inline-block',
  fontSize: '16px',
  fontWeight: 'bold',
  textDecoration: 'none',
  textAlign: 'center' as const,
  padding: '12px 24px',
  margin: '24px 0',
};

const footer = {
  color: '#8898aa',
  fontSize: '12px',
  textAlign: 'center' as const,
  marginTop: '32px',
};
```

### Send Milestone Emails

Update `src/lib/actions/views.ts`:

```typescript
'use server';

import { redis } from '@/lib/redis';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { eq } from 'drizzle-orm';
import { resend, EMAIL_FROM } from '@/lib/email';
import { MilestoneEmail } from '@/emails/milestone-email';

const MILESTONES = [100, 500, 1000, 5000, 10000];

export async function incrementViews(articleId: string) {
  try {
    const views = await redis.incr(`views:${articleId}`);
    
    // Check for milestone
    if (MILESTONES.includes(views)) {
      // Send email (don't await, send in background)
      sendMilestoneEmail(articleId, views).catch(console.error);
    }
    
    // Sync to DB every 10 views
    if (views % 10 === 0) {
      await db.update(articles)
        .set({ views })
        .where(eq(articles.id, articleId));
    }
    
    return views;
  } catch (error) {
    console.error('Failed to increment views:', error);
    return 0;
  }
}

async function sendMilestoneEmail(articleId: string, views: number) {
  try {
    // Get article with author
    const article = await db.query.articles.findFirst({
      where: eq(articles.id, articleId),
      with: { author: true },
    });
    
    if (!article || !article.author?.email) {
      return;
    }
    
    const articleUrl = `${process.env.NEXT_PUBLIC_APP_URL}/articles/${articleId}`;
    
    // Send email
    await resend.emails.send({
      from: EMAIL_FROM,
      to: article.author.email,
      subject: `🎉 Your article reached ${views.toLocaleString()} views!`,
      react: MilestoneEmail({
        authorName: article.author.displayName || 'there',
        articleTitle: article.title,
        articleUrl,
        views,
      }),
    });
    
    console.log(`✅ Sent milestone email for article ${articleId}`);
  } catch (error) {
    console.error('Failed to send email:', error);
  }
}
```

---

## AI Integration with OpenRouter

### Why AI in WikiApp?

**Use case:** Auto-generate article summaries

**Benefits:**
- ✅ Better SEO (meta descriptions)
- ✅ Social media previews
- ✅ Article cards
- ✅ Search results

### Why OpenRouter?

**Free AI Options:**

| Service | Cost | Quality | Speed |
|---------|------|---------|-------|
| OpenAI | Paid | Best | Fast |
| Anthropic | Paid | Best | Fast |
| Hugging Face | Free | OK | Slow |
| **OpenRouter** | **Free models** | **Good** | **Fast** |

**OpenRouter:**
- ✅ Free models available (Gemma 2 9B)
- ✅ OpenAI-compatible API
- ✅ No credit card required
- ✅ Good for summaries

### Step 1: Create OpenRouter Account

1. Go to [openrouter.ai](https://openrouter.ai)
2. Sign up (no credit card!)
3. Generate API key

### Step 2: Add to Environment

```bash
# .env.local
OPENROUTER_API_KEY="sk-or-..."
```

### Step 3: Create AI Helper

Create `src/lib/ai.ts`:

```typescript
import OpenAI from 'openai';

const openai = new OpenAI({
  baseURL: 'https://openrouter.ai/api/v1',
  apiKey: process.env.OPENROUTER_API_KEY,
  defaultHeaders: {
    'HTTP-Referer': process.env.NEXT_PUBLIC_APP_URL,
    'X-Title': 'WikiApp',
  },
});

export async function generateSummary(content: string): Promise<string> {
  try {
    const completion = await openai.chat.completions.create({
      model: 'google/gemma-2-9b-it:free',  // FREE model!
      messages: [
        {
          role: 'user',
          content: `Create a brief summary (2-3 sentences, max 150 words) of this article. Focus on the main points:\n\n${content.substring(0, 2000)}`,
        },
      ],
      max_tokens: 150,
      temperature: 0.7,
    });
    
    return completion.choices[0]?.message?.content?.trim() || '';
  } catch (error) {
    console.error('AI generation failed:', error);
    return '';  // Fail gracefully
  }
}
```

### Update Article Creation

```typescript
// src/lib/actions/articles.ts
import { generateSummary } from '@/lib/ai';

export async function createArticle(formData: FormData) {
  const user = await requireAuth();
  
  const title = formData.get('title') as string;
  const content = formData.get('content') as string;
  const imageFile = formData.get('image') as File;
  
  // Upload image
  let imageUrl: string | null = null;
  if (imageFile && imageFile.size > 0) {
    imageUrl = await uploadImage(imageFile);
  }
  
  // Generate AI summary
  const summary = await generateSummary(content);
  
  // Create article
  const [article] = await db.insert(articles)
    .values({
      title,
      content,
      summary,  // AI-generated!
      imageUrl,
      authorId: user.id,
    })
    .returning();
  
  revalidatePath('/articles');
  return article;
}
```

---

## Background Jobs with Cron

### Why Background Jobs?

**Use cases:**
- Generate missing summaries (batch processing)
- Send email digests (weekly)
- Clean up old data (monthly)
- Update analytics (hourly)

### Create Cron API Route

Create `src/app/api/cron/summarize/route.ts`:

```typescript
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { isNull, eq } from 'drizzle-orm';
import { generateSummary } from '@/lib/ai';
import { NextResponse } from 'next/server';

export async function GET(request: Request) {
  try {
    // Verify authorization
    const authHeader = request.headers.get('authorization');
    if (authHeader !== `Bearer ${process.env.CRON_SECRET}`) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }
    
    // Find articles without summaries
    const articlesWithoutSummary = await db.query.articles.findMany({
      where: isNull(articles.summary),
      limit: 5,  // Process 5 at a time
    });
    
    console.log(`Found ${articlesWithoutSummary.length} articles to summarize`);
    
    // Generate summaries
    for (const article of articlesWithoutSummary) {
      try {
        const summary = await generateSummary(article.content);
        
        if (summary) {
          await db.update(articles)
            .set({ summary })
            .where(eq(articles.id, article.id));
          
          console.log(`✅ Summarized: ${article.id}`);
        }
      } catch (error) {
        console.error(`❌ Failed: ${article.id}`, error);
      }
    }
    
    return NextResponse.json({
      success: true,
      processed: articlesWithoutSummary.length,
    });
  } catch (error) {
    console.error('Cron job failed:', error);
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

### Add Cron Secret

```bash
# .env.local
CRON_SECRET="your_random_secret_string_here"
```

### Configure Vercel Cron

Create `vercel.json`:

```json
{
  "crons": [
    {
      "path": "/api/cron/summarize",
      "schedule": "0 */6 * * *"
    }
  ]
}
```

**Schedule format (cron):**
```
0 */6 * * *
│ │  │ │ │
│ │  │ │ └─ Day of week (0-7, 0=Sunday)
│ │  │ └─── Month (1-12)
│ │  └───── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)

0 */6 * * * = Every 6 hours
0 0 * * * = Daily at midnight
0 9 * * 1 = Monday at 9am
```

---

## 🧠 CHECKPOINT: Understanding Services

Before moving on, make sure you can explain:

1. **Why use object storage instead of database?**
   - Files are large (1MB+)
   - Databases bloat with binary data
   - No CDN for database
   - Object storage is cheaper
   - Global distribution

2. **How does image upload work?**
   - User selects file → Browser
   - Convert to base64 → String
   - Upload to Cloudinary → Returns URL
   - Save URL to database → String reference
   - Display: `<img src={url} />` → CDN serves

3. **What is React Email?**
   - Write emails in JSX (not HTML)
   - Components just like React
   - Compiles to email-safe HTML
   - Better developer experience
   - Reusable email components

4. **Why use AI for summaries?**
   - Saves author time
   - Consistent quality
   - Better SEO (meta descriptions)
   - Improves user experience
   - Social media previews

5. **What are cron jobs?**
   - Scheduled background tasks
   - Run at specific times/intervals
   - Don't block user requests
   - Examples: batch processing, cleanup, emails
   - Vercel handles execution

**Take time to explain each concept clearly.**

---

## 🧪 EXERCISES: Practice Integrations

### Exercise 1: Implement Delete Image

When deleting an article, also delete its image from Cloudinary:

```typescript
// src/lib/actions/articles.ts
export async function deleteArticle(articleId: string) {
  const user = await requireAuth();
  
  // Get article (includes imageUrl)
  const article = await db.query.articles.findFirst({
    where: eq(articles.id, articleId),
  });
  
  // Your code here:
  // 1. Check ownership
  // 2. If has image, delete from Cloudinary
  // 3. Delete from database
}
```

<details>
<summary>💡 Solution</summary>

```typescript
import { cloudinary } from '@/lib/cloudinary';

export async function deleteArticle(articleId: string) {
  const user = await requireAuth();
  
  // Get article
  const article = await db.query.articles.findFirst({
    where: eq(articles.id, articleId),
  });
  
  if (!article) {
    throw new Error('Article not found');
  }
  
  // 1. Check ownership
  if (article.authorId !== user.id) {
    throw new Error('Unauthorized');
  }
  
  // 2. Delete image from Cloudinary if exists
  if (article.imageUrl) {
    try {
      // Extract public_id from URL
      // URL format: https://res.cloudinary.com/.../wikiapp/articles/abc123.jpg
      const publicId = article.imageUrl
        .split('/')
        .slice(-2)
        .join('/')
        .replace(/\.[^/.]+$/, ''); // Remove extension
      
      await cloudinary.uploader.destroy(publicId);
      console.log('✅ Deleted image from Cloudinary');
    } catch (error) {
      console.error('Failed to delete image:', error);
      // Continue anyway - don't block deletion
    }
  }
  
  // 3. Delete from database
  await db.delete(articles).where(eq(articles.id, articleId));
  
  revalidatePath('/articles');
  redirect('/articles');
}
```
</details>

### Exercise 2: Welcome Email

Send a welcome email when users sign up:

```typescript
// src/emails/welcome-email.tsx
// Create a welcome email template
```

<details>
<summary>💡 Solution</summary>

```typescript
// src/emails/welcome-email.tsx
import {
  Body,
  Button,
  Container,
  Head,
  Heading,
  Html,
  Link,
  Preview,
  Text,
} from '@react-email/components';

interface WelcomeEmailProps {
  userName: string;
}

export function WelcomeEmail({ userName }: WelcomeEmailProps) {
  return (
    <Html>
      <Head />
      <Preview>Welcome to WikiApp! Start sharing knowledge today.</Preview>
      <Body style={main}>
        <Container style={container}>
          <Heading style={h1}>Welcome to WikiApp! 📚</Heading>
          
          <Text style={text}>
            Hi {userName},
          </Text>
          
          <Text style={text}>
            Thanks for joining WikiApp! We're excited to have you in our
            community of knowledge sharers.
          </Text>
          
          <Text style={text}>
            Here's what you can do:
          </Text>
          
          <ul style={list}>
            <li>Create and share articles</li>
            <li>Upload images</li>
            <li>Track your article views</li>
            <li>Connect with other writers</li>
          </ul>
          
          <Button
            href={`${process.env.NEXT_PUBLIC_APP_URL}/articles/new`}
            style={button}
          >
            Write Your First Article
          </Button>
          
          <Text style={footer}>
            Happy writing! 🚀
            <br />
            The WikiApp Team
          </Text>
        </Container>
      </Body>
    </Html>
  );
}

const main = {
  backgroundColor: '#f6f9fc',
  fontFamily: '-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif',
};

const container = {
  backgroundColor: '#ffffff',
  margin: '0 auto',
  padding: '20px 0 48px',
  maxWidth: '600px',
};

const h1 = {
  color: '#333',
  fontSize: '28px',
  fontWeight: 'bold',
  margin: '40px 0',
  textAlign: 'center' as const,
};

const text = {
  color: '#333',
  fontSize: '16px',
  lineHeight: '26px',
  margin: '16px 0',
};

const list = {
  color: '#333',
  fontSize: '16px',
  lineHeight: '26px',
  paddingLeft: '20px',
};

const button = {
  backgroundColor: '#000',
  borderRadius: '5px',
  color: '#fff',
  display: 'inline-block',
  fontSize: '16px',
  fontWeight: 'bold',
  textDecoration: 'none',
  textAlign: 'center' as const,
  padding: '12px 24px',
  margin: '24px 0',
};

const footer = {
  color: '#8898aa',
  fontSize: '14px',
  textAlign: 'center' as const,
  marginTop: '32px',
};
```

**Send it on signup:**

```typescript
// In your Stack Auth webhook or signup handler
import { resend, EMAIL_FROM } from '@/lib/email';
import { WelcomeEmail } from '@/emails/welcome-email';

async function handleNewUser(user: User) {
  await resend.emails.send({
    from: EMAIL_FROM,
    to: user.email,
    subject: 'Welcome to WikiApp! 📚',
    react: WelcomeEmail({
      userName: user.displayName || 'there',
    }),
  });
}
```
</details>

### Exercise 3: Add Alt Text Generator

Use AI to generate alt text for uploaded images:

```typescript
// src/lib/ai.ts
export async function generateAltText(imageUrl: string): Promise<string> {
  // Your code here:
  // Use OpenRouter to analyze image and generate alt text
}
```

<details>
<summary>💡 Solution</summary>

```typescript
import OpenAI from 'openai';

const openai = new OpenAI({
  baseURL: 'https://openrouter.ai/api/v1',
  apiKey: process.env.OPENROUTER_API_KEY,
  defaultHeaders: {
    'HTTP-Referer': process.env.NEXT_PUBLIC_APP_URL,
    'X-Title': 'WikiApp',
  },
});

export async function generateAltText(imageUrl: string): Promise<string> {
  try {
    const completion = await openai.chat.completions.create({
      model: 'google/gemini-2.0-flash-exp:free',
      messages: [
        {
          role: 'user',
          content: [
            {
              type: 'text',
              text: 'Describe this image in one concise sentence for alt text. Focus on the main subject and important details. Maximum 125 characters.',
            },
            {
              type: 'image_url',
              image_url: {
                url: imageUrl,
              },
            },
          ],
        },
      ],
      max_tokens: 50,
    });
    
    return completion.choices[0]?.message?.content?.trim() || 'Image';
  } catch (error) {
    console.error('Alt text generation failed:', error);
    return 'Article image';
  }
}
```

**Use it after upload:**

```typescript
export async function createArticle(formData: FormData) {
  // ... upload image
  
  let imageUrl: string | null = null;
  let imageAlt: string | null = null;
  
  if (imageFile && imageFile.size > 0) {
    imageUrl = await uploadImage(imageFile);
    imageAlt = await generateAltText(imageUrl);
  }
  
  await db.insert(articles).values({
    title,
    content,
    imageUrl,
    imageAlt,  // Store alt text!
    authorId: user.id,
  });
}
```
</details>

### Exercise 4: Weekly Digest Cron

Create a cron job that sends weekly digest emails:

```typescript
// src/app/api/cron/weekly-digest/route.ts
// Send top 5 articles from past week to all users
```

<details>
<summary>💡 Solution</summary>

```typescript
import { db } from '@/lib/db';
import { users, articles } from '@/lib/db/schema';
import { gte, desc } from 'drizzle-orm';
import { resend, EMAIL_FROM } from '@/lib/email';
import { NextResponse } from 'next/server';

export async function GET(request: Request) {
  try {
    // Verify authorization
    const authHeader = request.headers.get('authorization');
    if (authHeader !== `Bearer ${process.env.CRON_SECRET}`) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }
    
    // Get articles from past week
    const oneWeekAgo = new Date();
    oneWeekAgo.setDate(oneWeekAgo.getDate() - 7);
    
    const topArticles = await db.query.articles.findMany({
      where: gte(articles.createdAt, oneWeekAgo),
      with: { author: true },
      orderBy: [desc(articles.views)],
      limit: 5,
    });
    
    if (topArticles.length === 0) {
      return NextResponse.json({ message: 'No articles this week' });
    }
    
    // Get all users
    const allUsers = await db.select().from(users);
    
    // Send emails
    let sent = 0;
    for (const user of allUsers) {
      if (!user.email) continue;
      
      try {
        await resend.emails.send({
          from: EMAIL_FROM,
          to: user.email,
          subject: '📚 This Week\'s Top Articles on WikiApp',
          html: `
            <h1>Top Articles This Week</h1>
            <p>Hi ${user.displayName || 'there'},</p>
            <p>Here are the top articles from this week:</p>
            <ul>
              ${topArticles.map(a => `
                <li>
                  <strong>${a.title}</strong>
                  <br>
                  by ${a.author?.displayName || 'Anonymous'}
                  <br>
                  ${a.views} views
                  <br>
                  <a href="${process.env.NEXT_PUBLIC_APP_URL}/articles/${a.id}">
                    Read Article
                  </a>
                </li>
              `).join('')}
            </ul>
            <p>Happy reading! 📖</p>
          `,
        });
        sent++;
      } catch (error) {
        console.error(`Failed to send to ${user.email}:`, error);
      }
    }
    
    return NextResponse.json({
      success: true,
      articleCount: topArticles.length,
      emailsSent: sent,
    });
  } catch (error) {
    console.error('Cron job failed:', error);
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}
```

**Add to `vercel.json`:**

```json
{
  "crons": [
    {
      "path": "/api/cron/summarize",
      "schedule": "0 */6 * * *"
    },
    {
      "path": "/api/cron/weekly-digest",
      "schedule": "0 9 * * 1"
    }
  ]
}
```

**Schedule: Monday at 9am**
</details>

---

## 🧠 CHECKPOINT: Understanding Services

Before moving on, make sure you understand:

1. **Why use object storage instead of database?**
   - What problems does it solve?
   - Performance implications?

2. **How does Cloudinary optimize images?**
   - What transformations happen?
   - How much can files shrink?

3. **Why send emails in the background?**
   - What happens if you don't?
   - How does it affect user experience?

4. **What makes React Email better than HTML?**
   - Why JSX for emails?
   - What problems does it solve?

5. **How do cron jobs work?**
   - What's the schedule format?
   - Why use them for batch processing?

**Explain each concept using simple analogies.**

---

## 🧪 EXERCISES: Practice Integrations

### Exercise 1: Add Image Validation

Enhance the image upload with better validation:

```typescript
// Add these checks:
// - File size < 5MB
// - Only image types (jpg, png, gif, webp)
// - Minimum dimensions (300x300)
// - Maximum dimensions (4000x4000)

export async function validateAndUploadImage(file: File): Promise<string> {
  // TODO: Implement validation
  // TODO: Upload to Cloudinary
  // TODO: Return URL
}
```

<details>
<summary>Solution</summary>

```typescript
export async function validateAndUploadImage(
  file: File
): Promise<string> {
  // 1. Check file size (5MB max)
  const maxSize = 5 * 1024 * 1024;
  if (file.size > maxSize) {
    throw new Error('Image must be less than 5MB');
  }
  
  // 2. Check file type
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif', 'image/webp'];
  if (!allowedTypes.includes(file.type)) {
    throw new Error('File must be JPG, PNG, GIF, or WebP');
  }
  
  // 3. Check dimensions
  const dimensions = await getImageDimensions(file);
  
  if (dimensions.width < 300 || dimensions.height < 300) {
    throw new Error('Image must be at least 300x300 pixels');
  }
  
  if (dimensions.width > 4000 || dimensions.height > 4000) {
    throw new Error('Image must not exceed 4000x4000 pixels');
  }
  
  // 4. Upload to Cloudinary
  return await uploadImage(file);
}

async function getImageDimensions(file: File): Promise<{
  width: number;
  height: number;
}> {
  return new Promise((resolve, reject) => {
    const img = new Image();
    const url = URL.createObjectURL(file);
    
    img.onload = () => {
      URL.revokeObjectURL(url);
      resolve({
        width: img.width,
        height: img.height,
      });
    };
    
    img.onerror = () => {
      URL.revokeObjectURL(url);
      reject(new Error('Failed to load image'));
    };
    
    img.src = url;
  });
}
```
</details>

### Exercise 2: Create Welcome Email

Build a welcome email template for new users:

```typescript
// Create src/emails/welcome-email.tsx
// Include:
// - Personalized greeting
// - Quick start guide
// - Link to first article creation
// - Support contact info
```

<details>
<summary>Solution</summary>

```typescript
// src/emails/welcome-email.tsx
import {
  Body,
  Button,
  Container,
  Head,
  Heading,
  Hr,
  Html,
  Link,
  Preview,
  Section,
  Text,
} from '@react-email/components';

interface WelcomeEmailProps {
  userName: string;
  loginUrl: string;
}

export function WelcomeEmail({ userName, loginUrl }: WelcomeEmailProps) {
  return (
    <Html>
      <Head />
      <Preview>Welcome to WikiApp - Start sharing knowledge today!</Preview>
      <Body style={main}>
        <Container style={container}>
          <Heading style={h1}>Welcome to WikiApp! 👋</Heading>
          
          <Text style={text}>
            Hi {userName},
          </Text>
          
          <Text style={text}>
            We're excited to have you join our community of knowledge sharers!
            WikiApp is your platform to write, share, and discover great content.
          </Text>
          
          <Section style={section}>
            <Heading style={h2}>Quick Start Guide</Heading>
            
            <Text style={text}>
              <strong>1. Create your first article</strong><br />
              Click the "New Article" button to start writing
            </Text>
            
            <Text style={text}>
              <strong>2. Add a featured image</strong><br />
              Make your article stand out with a great cover
            </Text>
            
            <Text style={text}>
              <strong>3. Share your knowledge</strong><br />
              Publish and watch your view count grow!
            </Text>
          </Section>
          
          <Button href={`${loginUrl}/articles/new`} style={button}>
            Create Your First Article
          </Button>
          
          <Hr style={hr} />
          
          <Text style={footer}>
            Need help? Reply to this email or visit our{' '}
            <Link href={`${loginUrl}/help`}>Help Center</Link>
          </Text>
          
          <Text style={footer}>
            Happy writing! 📚<br />
            The WikiApp Team
          </Text>
        </Container>
      </Body>
    </Html>
  );
}

// Styles
const main = {
  backgroundColor: '#f6f9fc',
  fontFamily: '-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif',
};

const container = {
  backgroundColor: '#ffffff',
  margin: '0 auto',
  padding: '20px 0 48px',
  marginBottom: '64px',
  maxWidth: '600px',
};

const h1 = {
  color: '#333',
  fontSize: '32px',
  fontWeight: 'bold',
  margin: '40px 0',
  padding: '0',
  textAlign: 'center' as const,
};

const h2 = {
  color: '#333',
  fontSize: '24px',
  fontWeight: 'bold',
  margin: '30px 0 20px',
};

const text = {
  color: '#333',
  fontSize: '16px',
  lineHeight: '26px',
  margin: '16px 0',
};

const section = {
  padding: '24px',
  backgroundColor: '#f8f9fa',
  borderRadius: '8px',
  margin: '24px 0',
};

const button = {
  backgroundColor: '#000',
  borderRadius: '5px',
  color: '#fff',
  display: 'inline-block',
  fontSize: '16px',
  fontWeight: 'bold',
  textDecoration: 'none',
  textAlign: 'center' as const,
  padding: '14px 28px',
  margin: '24px 0',
};

const hr = {
  borderColor: '#e6ebf1',
  margin: '32px 0',
};

const footer = {
  color: '#8898aa',
  fontSize: '14px',
  lineHeight: '24px',
  textAlign: 'center' as const,
  marginTop: '16px',
};
```

**Send it:**

```typescript
// src/lib/actions/auth.ts
import { resend, EMAIL_FROM } from '@/lib/email';
import { WelcomeEmail } from '@/emails/welcome-email';

export async function sendWelcomeEmail(
  email: string,
  userName: string
) {
  try {
    await resend.emails.send({
      from: EMAIL_FROM,
      to: email,
      subject: 'Welcome to WikiApp! 🎉',
      react: WelcomeEmail({
        userName,
        loginUrl: process.env.NEXT_PUBLIC_APP_URL!,
      }),
    });
    
    console.log(`✅ Sent welcome email to ${email}`);
  } catch (error) {
    console.error('Failed to send welcome email:', error);
  }
}
```
</details>

### Exercise 3: AI Summary Improvement

The current AI summary is basic. Enhance it to:
- Include article tags/categories
- Extract key points
- Generate meta description

```typescript
export async function generateEnhancedSummary(content: string): Promise<{
  summary: string;
  tags: string[];
  metaDescription: string;
}> {
  // TODO: Implement enhanced AI generation
}
```

<details>
<summary>Solution</summary>

```typescript
import OpenAI from 'openai';

const openai = new OpenAI({
  baseURL: 'https://openrouter.ai/api/v1',
  apiKey: process.env.OPENROUTER_API_KEY,
});

export async function generateEnhancedSummary(
  content: string
): Promise<{
  summary: string;
  tags: string[];
  metaDescription: string;
}> {
  try {
    const completion = await openai.chat.completions.create({
      model: 'google/gemma-2-9b-it:free',
      messages: [
        {
          role: 'user',
          content: `Analyze this article and provide:
1. A 2-3 sentence summary
2. 3-5 relevant tags/categories
3. A meta description (max 155 characters)

Article:
${content.substring(0, 2000)}

Respond in JSON format:
{
  "summary": "...",
  "tags": ["tag1", "tag2"],
  "metaDescription": "..."
}`,
        },
      ],
      max_tokens: 300,
      temperature: 0.7,
    });
    
    const response = completion.choices[0]?.message?.content?.trim() || '{}';
    
    // Parse JSON response
    const cleaned = response.replace(/```json\n?/g, '').replace(/```\n?/g, '');
    const result = JSON.parse(cleaned);
    
    return {
      summary: result.summary || '',
      tags: result.tags || [],
      metaDescription: result.metaDescription || '',
    };
  } catch (error) {
    console.error('AI generation failed:', error);
    return {
      summary: '',
      tags: [],
      metaDescription: '',
    };
  }
}
```
</details>

### Exercise 4: Batch Image Optimization

Create a cron job to optimize old images that weren't processed:

```typescript
// src/app/api/cron/optimize-images/route.ts
// Find articles with unoptimized images
// Re-upload to Cloudinary with optimizations
// Update database with new URLs
```

<details>
<summary>Solution</summary>

```typescript
// src/app/api/cron/optimize-images/route.ts
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { isNotNull } from 'drizzle-orm';
import { cloudinary } from '@/lib/cloudinary';
import { NextResponse } from 'next/server';

export async function GET(request: Request) {
  try {
    // Verify authorization
    const authHeader = request.headers.get('authorization');
    if (authHeader !== `Bearer ${process.env.CRON_SECRET}`) {
      return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
    }
    
    // Find articles with images
    const articlesWithImages = await db.query.articles.findMany({
      where: isNotNull(articles.imageUrl),
      limit: 10, // Process 10 at a time
    });
    
    console.log(`Found ${articlesWithImages.length} articles with images`);
    
    let optimized = 0;
    
    for (const article of articlesWithImages) {
      try {
        // Check if already optimized (has transformation params)
        if (article.imageUrl?.includes('/w_1200,')) {
          console.log(`⏭️  Already optimized: ${article.id}`);
          continue;
        }
        
        // Extract public_id from URL
        const publicId = extractPublicId(article.imageUrl!);
        
        if (!publicId) {
          console.log(`❌ Invalid URL: ${article.id}`);
          continue;
        }
        
        // Generate optimized URL
        const optimizedUrl = cloudinary.url(publicId, {
          transformation: [
            { width: 1200, crop: 'limit' },
            { quality: 'auto:good' },
            { fetch_format: 'auto' },
          ],
        });
        
        // Update database
        await db.update(articles)
          .set({ imageUrl: optimizedUrl })
          .where(eq(articles.id, article.id));
        
        console.log(`✅ Optimized: ${article.id}`);
        optimized++;
        
      } catch (error) {
        console.error(`❌ Failed to optimize ${article.id}:`, error);
      }
    }
    
    return NextResponse.json({
      success: true,
      processed: articlesWithImages.length,
      optimized,
    });
  } catch (error) {
    console.error('Cron job failed:', error);
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    );
  }
}

function extractPublicId(url: string): string | null {
  try {
    // Example URL: https://res.cloudinary.com/demo/image/upload/v1234/sample.jpg
    const match = url.match(/\/upload\/(?:v\d+\/)?(.+?)(?:\.[^.]+)?$/);
    return match ? match[1] : null;
  } catch {
    return null;
  }
}
```

**Add to vercel.json:**
```json
{
  "crons": [
    {
      "path": "/api/cron/summarize",
      "schedule": "0 */6 * * *"
    },
    {
      "path": "/api/cron/optimize-images",
      "schedule": "0 2 * * *"
    }
  ]
}
```
</details>

---

## Summary

You now understand:

✅ **Object storage** - Why and how to use it  
✅ **Cloudinary** - Free image uploads with optimization  
✅ **Email systems** - Transactional emails with Resend  
✅ **React Email** - Beautiful templates in JSX  
✅ **AI integration** - Free models with OpenRouter  
✅ **Background jobs** - Cron jobs for batch processing  

### Key Takeaways

1. **Never store files in database** - Use object storage
2. **Cloudinary optimizes automatically** - 96% smaller images!
3. **React Email makes templates easy** - Write JSX, not HTML
4. **Send emails in background** - Don't block user requests
5. **AI can enhance UX** - Auto-summaries, etc.
6. **Cron jobs for batch tasks** - Not everything needs real-time

[→ Continue to Part 7: Production & Deployment](./07-production-and-deployment.md)

---

**Great work on Part 6!** Your app now has file uploads, emails, and AI! 🚀
