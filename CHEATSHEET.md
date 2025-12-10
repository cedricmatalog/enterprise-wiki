# Quick Reference Cheat Sheet
## Essential Commands & Patterns for WikiApp

> **Quick access to all commands, patterns, and configurations**  
> Print this out or keep it handy while developing!

---

## Table of Contents

1. [NPM Commands](#npm-commands)
2. [Git Commands](#git-commands)
3. [Database Queries](#database-queries)
4. [Authentication Patterns](#authentication-patterns)
5. [Caching Patterns](#caching-patterns)
6. [File Upload](#file-upload)
7. [Email Sending](#email-sending)
8. [Common Components](#common-components)
9. [Environment Variables](#environment-variables)
10. [Deployment](#deployment)

---

## NPM Commands

```bash
# Development
npm run dev                 # Start dev server (http://localhost:3000)
npm run build              # Build for production
npm run start              # Start production server
npm run lint               # Run ESLint
npm run format             # Format with Prettier

# Database
npm run db:generate        # Generate migration from schema
npm run db:migrate         # Run migrations
npm run db:push            # Push schema changes (dev only)
npm run db:seed            # Seed database with test data
npm run db:studio          # Open Drizzle Studio

# Testing (if configured)
npm test                   # Run tests
npm run test:watch         # Watch mode

# Deployment
npm run predeploy          # Pre-deployment checks
```

---

## Git Commands

```bash
# Initial setup
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/USERNAME/REPO.git
git push -u origin main

# Daily workflow
git status                 # Check changes
git add .                  # Stage all changes
git commit -m "message"    # Commit changes
git push                   # Push to GitHub

# Branching
git checkout -b feature    # Create & switch to branch
git checkout main          # Switch to main
git merge feature          # Merge branch into current

# Undo changes
git restore file.ts        # Discard file changes
git reset --hard HEAD      # Discard all changes
git revert HEAD            # Undo last commit (safe)
```

---

## Database Queries

### SELECT

```typescript
// Get all
const articles = await db.select().from(articlesTable);

// Get with filter
const article = await db
  .select()
  .from(articlesTable)
  .where(eq(articlesTable.id, id));

// Query API (recommended)
const articles = await db.query.articles.findMany({
  where: eq(articles.authorId, userId),
  with: { author: true },
  orderBy: [desc(articles.createdAt)],
  limit: 10,
});
```

### INSERT

```typescript
// Insert one
const [article] = await db.insert(articles)
  .values({
    title: 'Title',
    content: 'Content',
    authorId: userId,
  })
  .returning();

// Insert multiple
const newArticles = await db.insert(articles)
  .values([
    { title: 'A', content: '...', authorId: userId },
    { title: 'B', content: '...', authorId: userId },
  ])
  .returning();
```

### UPDATE

```typescript
// Update with returning
const [updated] = await db.update(articles)
  .set({
    title: 'New Title',
    updatedAt: new Date(),
  })
  .where(eq(articles.id, id))
  .returning();

// Increment counter
await db.update(articles)
  .set({ views: sql`${articles.views} + 1` })
  .where(eq(articles.id, id));
```

### DELETE

```typescript
// Delete with condition
await db.delete(articles)
  .where(
    and(
      eq(articles.id, id),
      eq(articles.authorId, userId)
    )
  );
```

### Common Operators

```typescript
import { eq, ne, gt, gte, lt, lte, like, and, or, isNull, isNotNull } from 'drizzle-orm';

eq(column, value)          // =
ne(column, value)          // !=
gt(column, value)          // >
gte(column, value)         // >=
lt(column, value)          // <
lte(column, value)         // <=
like(column, pattern)      // LIKE
ilike(column, pattern)     // ILIKE (case-insensitive)
and(cond1, cond2)          // AND
or(cond1, cond2)           // OR
isNull(column)             // IS NULL
isNotNull(column)          // IS NOT NULL
```

---

## Authentication Patterns

### Server Component

```typescript
// Get user (optional)
const user = await stackServerApp.getUser();

// Require auth (throws if not logged in)
const user = await requireAuth();

// With redirect
if (!user) {
  redirect('/handler/sign-in');
}
```

### Client Component

```typescript
'use client';
import { useUser } from '@stackframe/stack';

export function Component() {
  const user = useUser();
  
  if (!user) {
    return <div>Not logged in</div>;
  }
  
  return <div>Hello {user.displayName}</div>;
}
```

### Server Action

```typescript
'use server';

export async function myAction(formData: FormData) {
  // Always check auth first!
  const user = await requireAuth();
  
  // Extract data
  const title = formData.get('title') as string;
  
  // Validate
  if (!title) {
    throw new Error('Title required');
  }
  
  // Process with user.id
  await db.insert(articles).values({
    title,
    authorId: user.id,  // Use authenticated user!
  });
  
  revalidatePath('/articles');
  redirect('/articles');
}
```

### Authorization Check

```typescript
// Check ownership
const article = await db.query.articles.findFirst({
  where: eq(articles.id, id),
});

if (article.authorId !== user.id) {
  throw new Error('Unauthorized');
}
```

---

## Caching Patterns

### Cache-Aside Pattern

```typescript
import { redis, CACHE_KEYS, CACHE_TTL } from '@/lib/redis';

export async function getCachedData(id: string) {
  const cacheKey = CACHE_KEYS.article(id);
  
  // 1. Try cache
  try {
    const cached = await redis.get(cacheKey);
    if (cached) return JSON.parse(cached as string);
  } catch (error) {
    console.error('Cache error:', error);
  }
  
  // 2. Query database
  const data = await db.query.articles.findFirst({
    where: eq(articles.id, id),
  });
  
  // 3. Store in cache
  try {
    await redis.setex(
      cacheKey,
      CACHE_TTL.article,
      JSON.stringify(data)
    );
  } catch (error) {
    console.error('Failed to cache:', error);
  }
  
  // 4. Return data
  return data;
}
```

### Cache Invalidation

```typescript
// After update
await db.update(articles).set(data).where(eq(articles.id, id));

// Invalidate caches
await Promise.all([
  redis.del(CACHE_KEYS.article(id)),
  redis.del(CACHE_KEYS.articles),
]);
```

### View Counter

```typescript
// Increment
const views = await redis.incr(`views:${articleId}`);

// Get
const views = await redis.get(`views:${articleId}`);
```

### Rate Limiting

```typescript
import { Ratelimit } from '@upstash/ratelimit';

const ratelimit = new Ratelimit({
  redis,
  limiter: Ratelimit.slidingWindow(10, '10 s'),
});

// In route/action
const { success } = await ratelimit.limit(userId);
if (!success) {
  throw new Error('Rate limited');
}
```

---

## File Upload

### Upload to Cloudinary

```typescript
import { cloudinary } from '@/lib/cloudinary';

export async function uploadImage(file: File): Promise<string> {
  // Convert to base64
  const bytes = await file.arrayBuffer();
  const buffer = Buffer.from(bytes);
  const base64 = buffer.toString('base64');
  const dataURI = `data:${file.type};base64,${base64}`;
  
  // Upload
  const result = await cloudinary.uploader.upload(dataURI, {
    folder: 'wikiapp/articles',
    transformation: [
      { width: 1200, crop: 'limit' },
      { quality: 'auto:good' },
      { fetch_format: 'auto' },
    ],
  });
  
  return result.secure_url;
}
```

### Form with File

```typescript
'use client';

export function UploadForm() {
  async function handleSubmit(formData: FormData) {
    await uploadAction(formData);
  }
  
  return (
    <form action={handleSubmit}>
      <input
        type="file"
        name="image"
        accept="image/*"
        required
      />
      <button type="submit">Upload</button>
    </form>
  );
}
```

---

## Email Sending

### React Email Template

```typescript
import { Html, Body, Container, Heading, Text, Button } from '@react-email/components';

interface EmailProps {
  userName: string;
  articleTitle: string;
  articleUrl: string;
}

export function MyEmail({ userName, articleTitle, articleUrl }: EmailProps) {
  return (
    <Html>
      <Body>
        <Container>
          <Heading>Hello {userName}!</Heading>
          <Text>Check out this article: {articleTitle}</Text>
          <Button href={articleUrl}>Read Article</Button>
        </Container>
      </Body>
    </Html>
  );
}
```

### Send Email

```typescript
import { resend, EMAIL_FROM } from '@/lib/email';
import { MyEmail } from '@/emails/my-email';

await resend.emails.send({
  from: EMAIL_FROM,
  to: user.email,
  subject: 'Subject Line',
  react: MyEmail({
    userName: user.displayName,
    articleTitle: article.title,
    articleUrl: `${process.env.NEXT_PUBLIC_APP_URL}/articles/${article.id}`,
  }),
});
```

---

## Common Components

### Button

```typescript
import { Button } from '@/components/ui/button';

// Variants
<Button>Default</Button>
<Button variant="destructive">Delete</Button>
<Button variant="outline">Cancel</Button>
<Button variant="ghost">Ghost</Button>

// Sizes
<Button size="sm">Small</Button>
<Button size="default">Default</Button>
<Button size="lg">Large</Button>

// As link
<Button asChild>
  <Link href="/page">Go</Link>
</Button>
```

### Card

```typescript
import { Card, CardHeader, CardTitle, CardDescription, CardContent, CardFooter } from '@/components/ui/card';

<Card>
  <CardHeader>
    <CardTitle>Title</CardTitle>
    <CardDescription>Description</CardDescription>
  </CardHeader>
  <CardContent>
    Content goes here
  </CardContent>
  <CardFooter>
    <Button>Action</Button>
  </CardFooter>
</Card>
```

### Form

```typescript
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Textarea } from '@/components/ui/textarea';

<form action={handleSubmit}>
  <div>
    <Label htmlFor="title">Title</Label>
    <Input
      id="title"
      name="title"
      placeholder="Enter title"
      required
    />
  </div>
  
  <div>
    <Label htmlFor="content">Content</Label>
    <Textarea
      id="content"
      name="content"
      placeholder="Write content"
      required
    />
  </div>
  
  <Button type="submit">Submit</Button>
</form>
```

---

## Environment Variables

### Required Variables

```bash
# Database
DATABASE_URL="postgresql://user:pass@host/db?sslmode=require"

# Auth
NEXT_PUBLIC_STACK_PROJECT_ID="proj_..."
NEXT_PUBLIC_STACK_PUBLISHABLE_CLIENT_KEY="stack_pub_..."
STACK_SECRET_SERVER_KEY="stack_secret_..."

# Redis
UPSTASH_REDIS_REST_URL="https://..."
UPSTASH_REDIS_REST_TOKEN="..."

# Cloudinary
CLOUDINARY_CLOUD_NAME="..."
CLOUDINARY_API_KEY="..."
CLOUDINARY_API_SECRET="..."

# Resend
RESEND_API_KEY="re_..."

# OpenRouter
OPENROUTER_API_KEY="sk-or-..."

# Cron
CRON_SECRET="random_string"

# App
NEXT_PUBLIC_APP_URL="http://localhost:3000"
```

### Access in Code

```typescript
// Client-side (NEXT_PUBLIC_ prefix)
const projectId = process.env.NEXT_PUBLIC_STACK_PROJECT_ID;

// Server-side only
const dbUrl = process.env.DATABASE_URL;

// With fallback
const appUrl = process.env.NEXT_PUBLIC_APP_URL || 'http://localhost:3000';
```

---

## Deployment

### Vercel CLI Commands

```bash
# Install Vercel CLI
npm i -g vercel

# Login
vercel login

# Deploy preview
vercel

# Deploy production
vercel --prod

# View logs
vercel logs
```

### Git Deployment (Automatic)

```bash
# Push to GitHub
git add .
git commit -m "Deploy"
git push

# Vercel automatically:
# 1. Detects push
# 2. Runs build
# 3. Deploys
# 4. Comments on PR with preview URL
```

### Environment Variables in Vercel

```bash
# Via dashboard:
Project Settings → Environment Variables

# Via CLI:
vercel env add DATABASE_URL production
vercel env pull .env.local  # Download to local
```

---

## Useful Tailwind Classes

```css
/* Layout */
flex flex-col items-center justify-between gap-4
grid grid-cols-3 gap-6
container mx-auto px-4 py-8

/* Sizing */
w-full h-screen max-w-4xl min-h-[400px]

/* Spacing */
p-4 px-6 py-3 m-auto mt-8 space-y-4

/* Typography */
text-lg text-center font-bold text-gray-600
line-clamp-3 truncate

/* Colors */
bg-blue-500 text-white border-gray-200
hover:bg-blue-600 focus:ring-2

/* Responsive */
md:flex-row lg:grid-cols-3 xl:max-w-6xl
hidden md:block

/* States */
hover:scale-105 active:scale-95
disabled:opacity-50 focus:outline-none
```

---

## Common Patterns

### Loading State

```typescript
'use client';

export function Component() {
  const [loading, setLoading] = useState(false);
  
  async function handleAction() {
    setLoading(true);
    try {
      await serverAction();
    } finally {
      setLoading(false);
    }
  }
  
  return (
    <Button onClick={handleAction} disabled={loading}>
      {loading ? 'Loading...' : 'Submit'}
    </Button>
  );
}
```

### Error Handling

```typescript
'use server';

export async function myAction(formData: FormData) {
  try {
    const user = await requireAuth();
    // ... action logic
    return { success: true };
  } catch (error) {
    console.error('Action failed:', error);
    return { 
      success: false, 
      error: error instanceof Error ? error.message : 'Unknown error'
    };
  }
}
```

### Pagination

```typescript
const page = 1;
const pageSize = 10;

const articles = await db.query.articles.findMany({
  limit: pageSize,
  offset: (page - 1) * pageSize,
  orderBy: [desc(articles.createdAt)],
});
```

---

## Debug Commands

```bash
# Check Node version
node --version

# Check npm version
npm --version

# Clear cache
npm cache clean --force

# Remove node_modules
rm -rf node_modules package-lock.json
npm install

# Clear Next.js cache
rm -rf .next

# Check TypeScript
npx tsc --noEmit

# Check environment variables
printenv | grep NEXT_PUBLIC
```

---

## Keyboard Shortcuts (VS Code)

```
Cmd/Ctrl + P        Quick file open
Cmd/Ctrl + Shift+P  Command palette
Cmd/Ctrl + `        Toggle terminal
Cmd/Ctrl + B        Toggle sidebar
Cmd/Ctrl + /        Comment line
Cmd/Ctrl + Shift+K  Delete line
Cmd/Ctrl + D        Select next occurrence
Alt + ↑/↓           Move line up/down
```

---

**Last Updated:** December 10, 2025  
**Tutorial Version:** 1.0  
**Quick Access:** Keep this cheat sheet handy while coding!

**Pro Tip:** Print this out or keep it on a second monitor for quick reference!
