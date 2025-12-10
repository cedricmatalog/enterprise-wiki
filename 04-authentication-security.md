# Part 4: Authentication & Security
## Understanding Auth Concepts and Building Secure Applications

> **Goal:** Implement secure authentication and authorization  
> **Time:** 5-7 hours  
> **Prerequisites:** Completed Parts 1-3

[← Back to Part 3](./03-database-and-data.md) | [Back to Index](./README.md) | [Next: Part 5 →](./05-caching-and-performance.md)

---

## What We'll Learn

By the end of this part, you'll understand:

✅ **Authentication fundamentals** - How modern auth works  
✅ **Authentication vs Authorization** - Know the difference  
✅ **Security concepts** - Tokens, sessions, cookies  
✅ **Stack Auth** - Drop-in authentication solution  
✅ **Protected routes** - Client and server protection  
✅ **Server actions security** - Securing API operations  
✅ **Common vulnerabilities** - XSS, CSRF, and prevention  

---

## The Problem: Identifying Users

### Why Do We Need Authentication? 🔴

**Scenario:** WikiApp without authentication

```
Anyone can:
- Create articles ✅
- Edit ANY article ❌ (Should only edit their own!)
- Delete ANY article ❌ (Should only delete their own!)
- See who created what ❌ (No user association!)
```

**Problems:**
1. No way to know who created content
2. Anyone can modify/delete anything
3. No personalization
4. No accountability
5. No private content

### Real-World Analogy

Think of a building:

**Without Authentication:**
```
- No doors, anyone walks in
- No offices, everyone in one room
- No lockers, shared stuff
- No names, anonymous chaos
```

**With Authentication:**
```
- Front desk (login)
- ID badges (tokens)
- Personal offices (user data)
- Name tags (identity)
- Access cards (permissions)
```

---

## Authentication vs Authorization

### Two Different Concepts

Many people confuse these. Let's clarify:

**Authentication** = **WHO are you?**
```
"Prove you are John Doe"
├─> Username + Password
├─> Google OAuth
├─> Fingerprint
└─> Face ID

Result: Identity confirmed ✅
```

**Authorization** = **WHAT can you do?**
```
"You're John Doe, but can you delete this article?"
├─> Are you the author? (ownership)
├─> Are you an admin? (role)
├─> Is it published? (status)
└─> Check permissions

Result: Action allowed/denied ✅/❌
```

### Hotel Analogy

**Authentication** (Check-in):
```
Front desk: "Show me your ID"
You: "Here's my driver's license"
Result: "Yes, you're John Doe" ✅
```

**Authorization** (Room access):
```
You: [Try to enter Room 301]
Room card: Checks if you're assigned to 301
├─> Room 301: Access ✅ (your room)
├─> Room 205: Access ❌ (not your room)
└─> Pool: Access ✅ (public area)
```

### In WikiApp

```
Authentication:
├─> Sign up / Sign in
├─> Stack Auth handles this
└─> User gets a token

Authorization:
├─> Can edit article?
│   └─> Check: article.authorId === user.id
├─> Can delete article?
│   └─> Check: article.authorId === user.id
└─> Can view draft?
    └─> Check: article.authorId === user.id || article.published
```

---

## How Modern Auth Works

### The Flow

```
1. User Signs Up
   ├─> Enters email + password
   ├─> Stack Auth creates account
   └─> Password hashed (never stored plaintext!)

2. User Signs In
   ├─> Enters credentials
   ├─> Stack Auth verifies
   ├─> Creates token (JWT)
   └─> Token stored in cookie

3. User Makes Requests
   ├─> Browser sends cookie automatically
   ├─> Server reads token from cookie
   ├─> Verifies token (is it valid?)
   └─> Extracts user info

4. User Signs Out
   ├─> Token deleted from cookie
   └─> User is anonymous again
```

### Understanding Tokens (JWT)

**JWT = JSON Web Token**

A JWT looks like:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiJ1c2VyXzEyMyIsImVtYWlsIjoiam9obkBleGFtcGxlLmNvbSJ9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

**It has 3 parts:**

```
[HEADER].[PAYLOAD].[SIGNATURE]

HEADER:
{
  "alg": "HS256",  // Algorithm
  "typ": "JWT"     // Type
}

PAYLOAD:
{
  "userId": "user_123",
  "email": "john@example.com",
  "exp": 1735689600  // Expiration timestamp
}

SIGNATURE:
// Cryptographic signature
// Proves the token hasn't been tampered with
```

**Why JWT?**

✅ **Stateless** - Server doesn't store sessions  
✅ **Self-contained** - All info in token  
✅ **Secure** - Signature prevents tampering  
✅ **Scalable** - Works with multiple servers  

**How it's secure:**

```
Hacker tries to change token:
{
  "userId": "user_123",  ← Changed to "user_admin"
  "email": "john@example.com"
}

Server checks signature:
❌ Signature invalid!
   Token was tampered with
   Request rejected
```

### Sessions vs Tokens

**Session-Based (Traditional):**
```
1. User logs in
2. Server creates session (stores in database)
3. Sends session ID in cookie
4. Every request: Look up session in DB
5. Logout: Delete session from DB

Pros: Easy to revoke
Cons: Database lookup on every request
```

**Token-Based (Modern):**
```
1. User logs in
2. Server creates JWT (all info in token)
3. Sends JWT in cookie
4. Every request: Verify signature (no DB lookup!)
5. Logout: Delete cookie (token expires naturally)

Pros: Faster (no DB lookup)
Cons: Harder to revoke (must wait for expiration)
```

**For WikiApp:** We use tokens (JWT) via Stack Auth

---

## Setting Up Stack Auth

### Why Stack Auth?

**Options compared:**

| Solution | Setup Time | Features | Free Tier | Verdict |
|----------|------------|----------|-----------|---------|
| **DIY** | 2 weeks | Basic | Free | ❌ Too risky |
| **NextAuth** | 2 days | Good | Free | ✅ But complex |
| **Clerk** | 1 hour | Great | 10k MAU | ❌ Needs card |
| **Supabase** | 1 hour | Good | 50k MAU | ✅ Good option |
| **Stack Auth** | 30 min | Great | 1k MAU | ✅ **Best!** |

**Stack Auth wins:**
- ✅ No credit card
- ✅ Drop-in solution
- ✅ Handles everything
- ✅ 1,000 users free

### Step 1: Create Account

1. Go to [stack-auth.com](https://stack-auth.com)
2. Sign up (free, no credit card!)
3. Create new project: `wikiapp`

### Step 2: Get Credentials

After creating project, you'll see:

```
Project ID: proj_abc123...
Publishable Key: stack_pub_xyz456...
Secret Key: stack_secret_789...
```

**Copy these!**

### Step 3: Add to Environment

```bash
# .env.local
NEXT_PUBLIC_STACK_PROJECT_ID="proj_abc123..."
NEXT_PUBLIC_STACK_PUBLISHABLE_CLIENT_KEY="stack_pub_xyz456..."
STACK_SECRET_SERVER_KEY="stack_secret_789..."
```

**Understanding the keys:**

```
NEXT_PUBLIC_STACK_PROJECT_ID
└─> NEXT_PUBLIC = Available in browser
    Used for: Identifying your project

NEXT_PUBLIC_STACK_PUBLISHABLE_CLIENT_KEY
└─> NEXT_PUBLIC = Available in browser
    Used for: Client-side auth operations

STACK_SECRET_SERVER_KEY
└─> NO NEXT_PUBLIC = Server-only!
    Used for: Verifying tokens
    ⚠️ Never expose this to browser!
```

### Step 4: Initialize Stack Auth

Create `src/lib/auth.ts`:

```typescript
import { StackServerApp } from '@stackframe/stack';

// Initialize Stack Auth
export const stackServerApp = new StackServerApp({
  tokenStore: 'nextjs-cookie',  // Store token in cookies
});
```

**What is `tokenStore: 'nextjs-cookie'`?**

```
Options:
1. 'nextjs-cookie' → Stores JWT in HTTP-only cookie
   ✅ Secure (JavaScript can't access)
   ✅ Sent automatically
   ✅ Works with Server Components

2. 'localStorage' → Stores in browser localStorage
   ❌ Insecure (JavaScript can access)
   ❌ Vulnerable to XSS
   ❌ Don't use this!
```

### Step 5: Wrap App with Provider

Update `src/app/layout.tsx`:

```typescript
import { StackProvider, StackTheme } from '@stackframe/stack';
import { stackServerApp } from '@/lib/auth';

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>
        <StackProvider app={stackServerApp}>
          <StackTheme>
            {children}
          </StackTheme>
        </StackProvider>
      </body>
    </html>
  );
}
```

**What does this do?**

```
StackProvider:
├─> Makes auth available throughout app
├─> Handles token refresh
└─> Manages auth state

StackTheme:
├─> Provides UI components
├─> Sign in/up forms
└─> Styled with Tailwind
```

---

## Using Auth in Components

### Client Component: useUser Hook

```typescript
'use client';

import { useUser } from '@stackframe/stack';

export function ProfileButton() {
  const user = useUser();
  
  if (!user) {
    return <a href="/handler/sign-in">Sign In</a>;
  }
  
  return (
    <div>
      <img src={user.profileImageUrl} />
      <span>{user.displayName}</span>
    </div>
  );
}
```

**What's in `user`?**

```typescript
user = {
  id: "user_abc123",
  email: "john@example.com",
  displayName: "John Doe",
  profileImageUrl: "https://...",
  primaryEmail: "john@example.com",
  // ... more fields
} || null  // null if not signed in
```

### Server Component: Get User

```typescript
// src/app/profile/page.tsx
import { stackServerApp } from '@/lib/auth';

export default async function ProfilePage() {
  const user = await stackServerApp.getUser();
  
  if (!user) {
    redirect('/handler/sign-in');
  }
  
  return (
    <div>
      <h1>Welcome, {user.displayName}</h1>
      <p>Email: {user.email}</p>
    </div>
  );
}
```

### Helper Function: Require Auth

```typescript
// src/lib/auth.ts
import { redirect } from 'next/navigation';

export async function requireAuth() {
  const user = await stackServerApp.getUser();
  
  if (!user) {
    redirect('/handler/sign-in');
  }
  
  return user;  // TypeScript knows user is not null
}
```

**Use it in pages:**

```typescript
export default async function NewArticlePage() {
  const user = await requireAuth();  // Throws if not logged in
  
  // If we reach here, user is authenticated!
  return <ArticleForm userId={user.id} />;
}
```

---

## Protected Routes

### Client-Side Protection

**Use for UI only** (not security!):

```typescript
'use client';

import { useUser } from '@stackframe/stack';
import { useRouter } from 'next/navigation';
import { useEffect } from 'react';

export default function ProtectedPage() {
  const user = useUser();
  const router = useRouter();
  
  useEffect(() => {
    if (!user) {
      router.push('/handler/sign-in');
    }
  }, [user, router]);
  
  if (!user) {
    return <div>Loading...</div>;
  }
  
  return <div>Protected content</div>;
}
```

**Problem with client-side only:**
- User can disable JavaScript
- Content briefly visible before redirect
- Not real security!

**Use for:** Improving UX, hiding UI elements

### Server-Side Protection

**Real security** - Use this!

```typescript
// src/app/articles/new/page.tsx
import { requireAuth } from '@/lib/auth';

export default async function NewArticlePage() {
  const user = await requireAuth();
  // If user not logged in, they never reach here
  
  return <ArticleForm userId={user.id} />;
}
```

**Why this is secure:**
- ✅ Runs on server (user can't bypass)
- ✅ No content sent to browser
- ✅ Redirect happens before rendering

### Protecting Server Actions

**Server actions also need protection!**

```typescript
// src/lib/actions/articles.ts
'use server';

import { requireAuth } from '@/lib/auth';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';

export async function createArticle(formData: FormData) {
  // ALWAYS check auth first!
  const user = await requireAuth();
  
  const title = formData.get('title') as string;
  const content = formData.get('content') as string;
  
  const [article] = await db.insert(articles)
    .values({
      title,
      content,
      authorId: user.id,  // Use authenticated user's ID
    })
    .returning();
  
  return article;
}
```

**Why this matters:**

```typescript
// ❌ Without auth check:
export async function createArticle(formData: FormData) {
  const authorId = formData.get('authorId');  // From form
  // Hacker can set authorId to anyone!
  
  await db.insert(articles).values({
    authorId,  // ❌ Trusting client data!
  });
}

// ✅ With auth check:
export async function createArticle(formData: FormData) {
  const user = await requireAuth();
  
  await db.insert(articles).values({
    authorId: user.id,  // ✅ From verified token!
  });
}
```

---

## Authorization: Checking Permissions

### Ownership Check

```typescript
'use server';

import { eq, and } from 'drizzle-orm';

export async function deleteArticle(articleId: string) {
  const user = await requireAuth();
  
  // Get article
  const [article] = await db
    .select()
    .from(articles)
    .where(eq(articles.id, articleId));
  
  if (!article) {
    throw new Error('Article not found');
  }
  
  // Check ownership
  if (article.authorId !== user.id) {
    throw new Error('Unauthorized: You can only delete your own articles');
  }
  
  // User owns it, allow deletion
  await db.delete(articles).where(eq(articles.id, articleId));
}
```

**Better: Check in single query**

```typescript
export async function deleteArticle(articleId: string) {
  const user = await requireAuth();
  
  const [deleted] = await db
    .delete(articles)
    .where(
      and(
        eq(articles.id, articleId),
        eq(articles.authorId, user.id),  // Only if user owns it
      )
    )
    .returning();
  
  if (!deleted) {
    throw new Error('Article not found or unauthorized');
  }
  
  return deleted;
}
```

### Helper Function: Check Ownership

```typescript
// src/lib/auth.ts

export async function canEditArticle(articleId: string): Promise<boolean> {
  const user = await stackServerApp.getUser();
  
  if (!user) {
    return false;
  }
  
  const article = await db.query.articles.findFirst({
    where: eq(articles.id, articleId),
  });
  
  return article?.authorId === user.id;
}
```

**Use it:**

```typescript
export default async function EditArticlePage({ 
  params 
}: { 
  params: { id: string } 
}) {
  const user = await requireAuth();
  const canEdit = await canEditArticle(params.id);
  
  if (!canEdit) {
    return <div>You don't have permission to edit this article</div>;
  }
  
  return <ArticleEditForm articleId={params.id} />;
}
```

---

## Security Best Practices

### 1. Never Trust Client Input

```typescript
// ❌ DANGEROUS
export async function updateArticle(data: {
  id: string;
  userId: string;  // From client!
  title: string;
}) {
  await db.update(articles)
    .set({ title: data.title })
    .where(
      and(
        eq(articles.id, data.id),
        eq(articles.authorId, data.userId),  // ❌ Trusting client!
      )
    );
}

// ✅ SAFE
export async function updateArticle(articleId: string, title: string) {
  const user = await requireAuth();  // Get from token!
  
  await db.update(articles)
    .set({ title })
    .where(
      and(
        eq(articles.id, articleId),
        eq(articles.authorId, user.id),  // ✅ From verified token!
      )
    );
}
```

### 2. Validate Input

```typescript
import { z } from 'zod';

const articleSchema = z.object({
  title: z.string().min(1).max(200),
  content: z.string().min(10).max(50000),
});

export async function createArticle(formData: FormData) {
  const user = await requireAuth();
  
  // Validate input
  const validated = articleSchema.parse({
    title: formData.get('title'),
    content: formData.get('content'),
  });
  
  // Now safe to use
  await db.insert(articles).values({
    ...validated,
    authorId: user.id,
  });
}
```

### 3. Rate Limiting

```typescript
// Prevent abuse (we'll implement with Redis in Part 5)
import { ratelimit } from '@/lib/redis';

export async function createArticle(formData: FormData) {
  const user = await requireAuth();
  
  // Check rate limit
  const { success } = await ratelimit.limit(user.id);
  
  if (!success) {
    throw new Error('Rate limit exceeded. Please try again later.');
  }
  
  // Proceed with creation
}
```

### 4. SQL Injection Prevention

**Drizzle prevents this automatically!**

```typescript
// ❌ DANGEROUS (Raw SQL)
const userInput = formData.get('search');
const results = await pool.query(
  `SELECT * FROM articles WHERE title LIKE '%${userInput}%'`
  // If userInput = "'; DROP TABLE articles; --"
  // SQL: SELECT * FROM articles WHERE title LIKE '%'; DROP TABLE articles; --%'
  // ☠️ Your database is GONE!
);

// ✅ SAFE (Drizzle)
const userInput = formData.get('search') as string;
const results = await db.query.articles.findMany({
  where: like(articles.title, `%${userInput}%`),
});
// Drizzle escapes input automatically
// Can't inject SQL!
```

### 5. XSS Prevention

**Next.js prevents this automatically!**

```typescript
// React/Next.js escapes by default
const userInput = '<script>alert("XSS")</script>';

// ✅ SAFE (Rendered as text)
<div>{userInput}</div>
// Displays: <script>alert("XSS")</script> (as text)

// ❌ DANGEROUS (Don't do this!)
<div dangerouslySetInnerHTML={{ __html: userInput }} />
// Executes: alert("XSS") 
```

### 6. CSRF Prevention

**Next.js + Stack Auth handle this!**

```
CSRF Attack:
1. You're logged into wikiapp.com
2. You visit evil.com
3. evil.com has form:
   <form action="wikiapp.com/api/delete" method="POST">
4. Form auto-submits using YOUR cookies
5. Article deleted!

Prevention:
- Stack Auth uses HTTP-only cookies
- Next.js validates origin
- Server actions have CSRF protection built-in
```

---

## Common Vulnerabilities

### 1. Insecure Direct Object References (IDOR)

```typescript
// ❌ VULNERABLE
// URL: /api/articles/123/delete
export async function DELETE(
  request: Request,
  { params }: { params: { id: string } }
) {
  // No auth check!
  await db.delete(articles).where(eq(articles.id, params.id));
  return Response.json({ success: true });
}
// Anyone can delete any article by guessing IDs!

// ✅ SECURE
export async function DELETE(
  request: Request,
  { params }: { params: { id: string } }
) {
  const user = await requireAuth();
  
  const [deleted] = await db
    .delete(articles)
    .where(
      and(
        eq(articles.id, params.id),
        eq(articles.authorId, user.id),  // Check ownership!
      )
    )
    .returning();
  
  if (!deleted) {
    throw new Error('Unauthorized');
  }
  
  return Response.json({ success: true });
}
```

### 2. Mass Assignment

```typescript
// ❌ VULNERABLE
export async function updateProfile(formData: FormData) {
  const user = await requireAuth();
  
  // Blindly update all fields
  const updates = Object.fromEntries(formData);
  // What if formData includes: { role: "admin" }?
  
  await db.update(users)
    .set(updates)  // ❌ User can set ANY field!
    .where(eq(users.id, user.id));
}

// ✅ SECURE
export async function updateProfile(formData: FormData) {
  const user = await requireAuth();
  
  // Only allow specific fields
  const updates = {
    displayName: formData.get('displayName'),
    bio: formData.get('bio'),
    // role is NOT included!
  };
  
  await db.update(users)
    .set(updates)
    .where(eq(users.id, user.id));
}
```

### 3. Broken Access Control

```typescript
// ❌ VULNERABLE
export default async function AdminPage() {
  const user = await requireAuth();
  
  // Only check if logged in, not if admin!
  return <AdminPanel />;
}

// ✅ SECURE
export default async function AdminPage() {
  const user = await requireAuth();
  
  if (user.role !== 'admin') {
    throw new Error('Unauthorized');
  }
  
  return <AdminPanel />;
}
```

---

## 🧠 CHECKPOINT: Understanding Auth

Before moving on, explain:

1. **What's the difference between authentication and authorization?**
2. **How does JWT work?**
3. **Why do we check auth in server actions?**
4. **What is IDOR and how do we prevent it?**
5. **Why should we never trust client input?**

Take time to explain each clearly.

---

## 🧪 EXERCISES: Practice Security

### Exercise 1: Protected Page

Create a protected user profile page that:
- Requires authentication
- Shows user's articles
- Allows editing own profile only

<details>
<summary>Solution</summary>

```typescript
// src/app/profile/page.tsx
import { requireAuth } from '@/lib/auth';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { eq, desc } from 'drizzle-orm';

export default async function ProfilePage() {
  const user = await requireAuth();
  
  const userArticles = await db.query.articles.findMany({
    where: eq(articles.authorId, user.id),
    orderBy: [desc(articles.createdAt)],
  });
  
  return (
    <div>
      <h1>{user.displayName}'s Profile</h1>
      <p>Email: {user.email}</p>
      
      <h2>My Articles ({userArticles.length})</h2>
      {userArticles.map((article) => (
        <div key={article.id}>
          <h3>{article.title}</h3>
          <p>{article.views} views</p>
        </div>
      ))}
    </div>
  );
}
```
</details>

### Exercise 2: Secure Edit Action

Create a server action that updates an article, but only if:
- User is authenticated
- User owns the article
- Input is validated

<details>
<summary>Solution</summary>

```typescript
'use server';

import { requireAuth } from '@/lib/auth';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { eq, and } from 'drizzle-orm';
import { z } from 'zod';

const updateSchema = z.object({
  title: z.string().min(1).max(200),
  content: z.string().min(10).max(50000),
});

export async function updateArticle(
  articleId: string,
  data: { title: string; content: string }
) {
  // 1. Check authentication
  const user = await requireAuth();
  
  // 2. Validate input
  const validated = updateSchema.parse(data);
  
  // 3. Update with ownership check
  const [updated] = await db
    .update(articles)
    .set({
      ...validated,
      updatedAt: new Date(),
    })
    .where(
      and(
        eq(articles.id, articleId),
        eq(articles.authorId, user.id),
      )
    )
    .returning();
  
  if (!updated) {
    throw new Error('Article not found or unauthorized');
  }
  
  return updated;
}
```
</details>

---

## Summary

You now understand:

✅ **Authentication vs Authorization** - Identity vs permissions  
✅ **How modern auth works** - Tokens, cookies, flow  
✅ **Stack Auth** - Drop-in authentication  
✅ **Protected routes** - Client and server protection  
✅ **Server action security** - Verifying every action  
✅ **Common vulnerabilities** - And how to prevent them  

### Key Takeaways

1. **Authentication** = Who you are (login)
2. **Authorization** = What you can do (permissions)
3. **Never trust client input** - Always verify on server
4. **Check auth in EVERY server action** - No exceptions
5. **Use ownership checks** - Verify user owns resource
6. **Validate all input** - Use Zod or similar
7. **HTTP-only cookies** - Secure token storage

### What's Next?

In Part 5, we'll optimize performance:
- Understanding caching
- Redis implementation
- View counters
- Query optimization
- Edge caching strategies

[→ Continue to Part 5: Caching & Performance](./05-caching-and-performance.md)

---

**Excellent work on Part 4!** Security is critical - you now know how to build secure apps. 🔒
