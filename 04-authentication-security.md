# Part 4: Authentication & Security
## Understanding Auth Concepts and Building Secure Applications

> **Goal:** Implement secure authentication and authorization  
> **Time:** 5-7 hours  
> **Prerequisites:** Completed Parts 1-3

[← Back to Part 3](./03-database-and-data.md) | [Back to Index](./README.md) | [Next: Part 5 →](./05-caching-and-performance.md)

---

> **📚 TL;DR - What You'll Build**
>
> By the end, you'll have:
> - ✅ Secure login/signup with Stack Auth
> - ✅ Protected routes (both client and server)
> - ✅ User sessions and tokens
> - ✅ Authorization checks (who can edit/delete what)
> - ✅ Understanding of common security vulnerabilities
>
> **This is critical - security done wrong = disaster!**

---

## 📍 Progress: Part 4 of 7

```
[████████████████░░░░░░░░] 57% Complete
Part 1 ✅ | Part 2 ✅ | Part 3 ✅ | Part 4 📍 YOU ARE HERE
```

---

## ⏱️ Time Breakdown

- **Understanding Auth Concepts:** 1-2 hours (theory)
- **Stack Auth Setup:** 1 hour (implementation)
- **Protected Routes:** 1-2 hours (client + server)
- **Security Best Practices:** 1-2 hours (vulnerabilities)
- **Testing & Exercises:** 1 hour (practice)

**Total:** 5-7 hours (Security is worth the time!)

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

> **💡 Key Question**
>
> **How do you know who created what content?**  
> Without authentication, your app is a free-for-all!

---

### Why Do We Need Authentication?

**Scenario:** WikiApp without authentication

```
Anyone can:
- Create articles ✅ (Good!)
- Edit ANY article ❌ (Bad! Should only edit their own!)
- Delete ANY article ❌ (Disaster! No protection!)
- See who created what ❌ (No attribution!)
- Impersonate others ❌ (Total chaos!)
```

**Problems:**
1. ❌ **No ownership** - Can't tell who created content
2. ❌ **No protection** - Anyone can modify/delete anything
3. ❌ **No personalization** - Can't customize per user
4. ❌ **No accountability** - No way to track bad actors
5. ❌ **No privacy** - Everything is public
6. ❌ **No trust** - Users won't share valuable content

> **⚠️ Real-World Impact**
>
> **Without authentication:**
> - Wikipedia without it = instant vandalism
> - Medium without it = no author credibility
> - GitHub without it = code chaos
> - Gmail without it = everyone reads everything
>
> Authentication isn't optional - it's fundamental!

---

### Building Analogy

> **🏢 Think of Authentication Like a Building**

**Without Authentication:**
```
🚪 No doors - anyone walks in
👥 No offices - everyone in one big room
🎒 No lockers - all belongings shared
😶 No names - complete anonymity
📝 No assignments - who did what?

Result: CHAOS! Nothing is safe or organized.
```

**With Authentication:**
```
🎫 Front desk - check-in process (login)
🪪 ID badges - proof of identity (tokens)
🚪 Personal offices - private spaces (user data)
👤 Name tags - clear identity (user profiles)
🔑 Access cards - permissions (authorization)
📋 Sign-in logs - accountability (audit trails)

Result: ORDER! Everyone has their space and responsibilities.
```

---

## Authentication vs Authorization

> **📚 TL;DR - The Critical Difference**
>
> **Authentication (AuthN)** = WHO are you?  
> **Authorization (AuthZ)** = WHAT can you do?
>
> **Think of it like:**
> - Authentication = Showing your ID at airport security
> - Authorization = Your ticket determines which flights you can board
>
> **You need BOTH for security!**

---

### Two Different Concepts

> **⚡ Key Insight**
>
> **Most security bugs come from confusing these two!**
>
> - Authentication answers: "Are you really John?"
> - Authorization answers: "Can John do this specific thing?"
>
> Getting authentication right but authorization wrong = still insecure!

**Authentication (WHO are you?):**
```
"Prove your identity"

Methods:
├─> Username + Password (traditional)
├─> Google OAuth (social login)
├─> Magic links (email verification)
├─> Biometrics (fingerprint, Face ID)
├─> 2FA tokens (extra security)
└─> Passkeys (modern, phishing-resistant)

Result: Identity confirmed ✅
You are now "logged in"
```

**Authorization (WHAT can you do?):**
```
"You're verified, but are you allowed to do this?"

Checks:
├─> Ownership: "Is this YOUR article?"
├─> Roles: "Are you an admin?"
├─> Status: "Is this published or draft?"
├─> Permissions: "Do you have 'delete' permission?"
└─> Context: "Is this action allowed right now?"

Result: Action allowed ✅ or denied ❌
```

---

### Hotel Analogy (Perfect Example)

> **🏨 Hotel Check-In: The Perfect Auth Analogy**

**Authentication (Front Desk Check-in):**
```
Receptionist: "Show me your ID and reservation"
You: "Here's my driver's license and booking confirmation"
Receptionist: [Verifies ID matches booking]
Result: "Yes, you're John Doe" ✅
Action: Gives you a room key card
```

**Authorization (Using the Key Card):**
```
You: [Walk around hotel with key card]

Room 301: 🟢 BEEP ✅ (YOUR room - access granted)
Room 205: 🔴 BEEP ❌ (NOT your room - access denied)
Gym: 🟢 BEEP ✅ (Public amenity - all guests)
Pool: 🟢 BEEP ✅ (Public amenity - all guests)
Penthouse: 🔴 BEEP ❌ (Suite level - upgrade required)
Staff Room: 🔴 BEEP ❌ (Employees only)
```

**The key card knows:**
- ✅ WHO you are (authentication - John Doe)
- ✅ WHAT you can access (authorization - Room 301, public areas)
- ❌ WHAT you can't access (other rooms, staff areas)

---

### In WikiApp

**Authentication (WHO):**
```typescript
// User proves identity
const user = await stackAuth.getUser();

// Now we know:
user = {
  id: "user_123",
  email: "john@example.com",
  name: "John Doe"
}

// Stack Auth handled all the hard parts:
// - Password hashing
// - Token generation
// - Session management
// - Security best practices
```

**Authorization (WHAT):**
```typescript
// Now check if user CAN do something
async function deleteArticle(articleId: string, userId: string) {
  // Get the article
  const article = await db.query.articles.findFirst({
    where: eq(articles.id, articleId)
  });
  
  // Authorization check!
  if (article.authorId !== userId) {
    throw new Error("Unauthorized: You can only delete your own articles");
  }
  
  // If we got here, authorization passed ✅
  await db.delete(articles).where(eq(articles.id, articleId));
}
```

> **💡 Pro Tip**
>
> **The pattern:**
> ```typescript
> // 1. Authenticate (WHO)
> const user = await getUser();
> if (!user) throw new Error("Not authenticated");
>
> // 2. Authorize (WHAT)
> if (!canUserDoThis(user, resource)) {
>   throw new Error("Not authorized");
> }
>
> // 3. Do the thing
> await doTheSecureThing();
> ```
>
> **Always do BOTH checks!**

---

---

### ☕ Quick Break (5 minutes)

**You've learned the auth fundamentals!**

**Covered so far:**
- ✅ Why authentication matters (ownership, protection)
- ✅ Authentication vs Authorization (WHO vs WHAT)
- ✅ Perfect analogies (hotel, building)

**Take 5 minutes:**
1. Stand up and stretch
2. Explain auth vs authz out loud (Feynman it!)
3. Think of examples in apps you use

**Coming up next:** How modern auth actually works (tokens, JWTs)

---

## How Modern Auth Works

> **📚 TL;DR - The Modern Auth Flow**
>
> **4 simple steps:**
> 1. **Sign up** - Create account (password hashed)
> 2. **Sign in** - Verify identity (get token)
> 3. **Make requests** - Browser sends token automatically
> 4. **Sign out** - Delete token
>
> **Key concept:** Tokens (JWTs) contain user info + signature  
> **Why secure:** Signature prevents tampering
>
> **Stack Auth handles all of this for us!**

---

### The Complete Flow

```
1. User Signs Up 📝
   ├─> User enters email + password
   ├─> Stack Auth creates account
   ├─> Password hashed with bcrypt (NEVER stored plaintext!)
   └─> Account created ✅

2. User Signs In 🔑
   ├─> User enters credentials
   ├─> Stack Auth verifies against hash
   ├─> Creates JWT token with user info
   ├─> Token stored in httpOnly cookie (secure!)
   └─> User is "logged in" ✅

3. User Makes Requests 🌐
   ├─> Browser sends cookie automatically (every request)
   ├─> Server reads token from cookie
   ├─> Verifies token signature (is it valid? not tampered?)
   ├─> Extracts user info (id, email, etc.)
   └─> Request processed with user context ✅

4. User Signs Out 🚪
   ├─> Token deleted from cookie
   ├─> User is anonymous again
   └─> Must sign in to access protected resources ✅
```

> **⚠️ Critical Security Detail**
>
> **Passwords are NEVER stored plaintext!**
>
> ```
> ❌ BAD (NEVER do this):
> Database: { password: "mysecretpass" }
> Hacker gets database → All passwords exposed!
>
> ✅ GOOD (Always do this):
> Database: { password: "$2b$10$N9qo8..." } ← Hashed!
> Hacker gets database → Useless! Can't reverse the hash!
> ```
>
> **Stack Auth does this automatically.** Never roll your own!

---

### Understanding Tokens (JWT)

> **💡 What is a JWT?**
>
> **JWT = JSON Web Token**
>
> Think of it like a driver's license:
> - Has your info (name, photo, DOB)
> - Has security features (hologram, signature)
> - Can't be faked (tamper-evident)
> - Proves who you are
>
> JWTs are digital driver's licenses!

**A real JWT looks like:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiJ1c2VyXzEyMyIsImVtYWlsIjoiam9obkBleGFtcGxlLmNvbSJ9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

**Looks like gibberish? It has 3 parts:**

```
[HEADER].[PAYLOAD].[SIGNATURE]
   ↓        ↓         ↓
Base64   Base64   Crypto hash
```

**Decoded:**

```json
// HEADER (metadata)
{
  "alg": "HS256",  // Algorithm used
  "typ": "JWT"     // Type of token
}

// PAYLOAD (actual data)
{
  "userId": "user_123",
  "email": "john@example.com",
  "name": "John Doe",
  "iat": 1735689600,  // Issued at (timestamp)
  "exp": 1735776000   // Expires at (timestamp)
}

// SIGNATURE (security)
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret_key
)
```

**Why JWT is secure:**

> **⚡ Key Insight - The Signature**
>
> ```
> Hacker tries to change token:
> 1. Decodes JWT
> 2. Changes userId to "admin"  
> 3. Re-encodes JWT
>
> Server receives tampered token:
> 1. Decodes JWT
> 2. Recalculates signature
> 3. Signature doesn't match!
> 4. ❌ Request rejected
>
> Why? Hacker doesn't have the secret_key!
> ```
>
> **Without the secret key, tokens can't be faked!**

**JWT Benefits:**

✅ **Stateless** - Server doesn't need to store sessions in database  
✅ **Self-contained** - All info is in the token itself  
✅ **Secure** - Signature prevents tampering  
✅ **Scalable** - Works across multiple servers (no shared session store)  
✅ **Standard** - Widely supported, many libraries  

---

### Sessions vs Tokens: The Evolution

> **📋 Quick Comparison**

**Session-Based (Old Way):**
```
1. User logs in
2. Server creates session → Stores in database
   └─> sessions table: { sessionId, userId, expiresAt }
3. Server sends sessionId in cookie → "abc123"
4. Every request: 
   └─> Server looks up sessionId in database
   └─> Gets userId from database
   └─> Database query on EVERY request! ❌
5. Logout: Delete session from database

Pros: 
✅ Easy to revoke (just delete from DB)
✅ Server has full control

Cons:
❌ Database lookup on every request (slow!)
❌ Doesn't scale well (shared session store needed)
❌ Hard to use with microservices
```

**Token-Based (Modern Way):**
```
1. User logs in
2. Server creates JWT → All info in token
   └─> No database storage needed!
3. Server sends JWT in cookie
4. Every request:
   └─> Server verifies signature (fast!)
   └─> Extracts userId from token
   └─> No database lookup! ✅
5. Logout: Delete cookie (token expires naturally)

Pros:
✅ Fast (no database lookup)
✅ Scales infinitely (stateless)
✅ Works with microservices
✅ Works with mobile apps

Cons:
❌ Harder to revoke (must wait for expiration)
❌ Tokens can get large
❌ More complex to implement correctly
```

**For WikiApp:** We use tokens (JWT) via Stack Auth

**Why?**
- ⚡ Faster (no DB lookups)
- 📈 Scales better (stateless)
- 🎯 Simpler with serverless
- ✅ Stack Auth handles complexity

---

## Setting Up Stack Auth

> **📚 TL;DR - Why Stack Auth?**
>
> **Stack Auth handles the hard parts:**
> - ✅ Password hashing (bcrypt)
> - ✅ Token management (JWT)
> - ✅ Session handling (cookies)
> - ✅ OAuth providers (Google, GitHub, etc.)
> - ✅ Email verification
> - ✅ Password reset
>
> **You focus on:** Your app's features  
> **Stack Auth focuses on:** Security & authentication
>
> **Setup time:** ~15 minutes (vs weeks building it yourself!)

---

### Why Stack Auth?

> **⚡ Key Decision**
>
> **Never build authentication yourself!**
>
> Security is HARD. One mistake = compromised accounts.  
> Use a proven solution like Stack Auth.

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

---

### ☕ Take a Break (5 minutes)

**You've learned a lot about auth implementation!**

**Covered so far:**
- ✅ Auth fundamentals (WHO vs WHAT)
- ✅ Modern auth flow (JWT, cookies)
- ✅ Stack Auth setup
- ✅ Protected routes
- ✅ Authorization checks

**Take 5 minutes:**
1. Stand and stretch
2. Try signing in/out of your app
3. Test a protected route

**Coming up next:** Security vulnerabilities (learn what NOT to do!)

---

## Common Vulnerabilities

> **📚 TL;DR - The Top 3 Security Bugs**
>
> **1. IDOR (Insecure Direct Object References)**
> - Problem: No ownership check
> - Fix: Verify user owns the resource
>
> **2. Mass Assignment**
> - Problem: Trust all input fields
> - Fix: Whitelist allowed fields only
>
> **3. Broken Access Control**
> - Problem: Check authentication but not authorization
> - Fix: Verify BOTH who you are AND what you can do
>
> **Remember:** Never trust client input!

---

### 1. Insecure Direct Object References (IDOR)

> **⚠️ IDOR = The #1 Authorization Bug**
>
> **The problem:**
> ```
> User A can access /api/articles/123
> User A tries /api/articles/124 (User B's article)
> No check! User A can read/edit/delete User B's article!
> ```
>
> **How hackers exploit this:**
> 1. Create account
> 2. Find their own article ID (e.g., 123)
> 3. Try IDs: 122, 124, 125... (automated)
> 4. Access everyone's private data!
>
> **Real-world examples:** Facebook, Instagram have had IDOR bugs!

```typescript
// ❌ VULNERABLE - NO OWNERSHIP CHECK
// URL: /api/articles/123/delete
export async function DELETE(
  request: Request,
  { params }: { params: { id: string } }
) {
  // Only checks if user is logged in
  const user = await requireAuth();
  
  // But doesn't check if user OWNS this article!
  await db.delete(articles).where(eq(articles.id, params.id));
  
  return Response.json({ success: true });
}

// 🔴 DANGER: Anyone logged in can delete ANY article!
// Hacker can:
// 1. Sign up (get authenticated)
// 2. Try /api/articles/1/delete, /api/articles/2/delete...
// 3. Delete all articles in database!
```

```typescript
// ✅ SECURE - WITH OWNERSHIP CHECK
export async function DELETE(
  request: Request,
  { params }: { params: { id: string } }
) {
  // 1. Authenticate (WHO)
  const user = await requireAuth();
  
  // 2. Authorize (WHAT) - Check ownership!
  const [deleted] = await db
    .delete(articles)
    .where(
      and(
        eq(articles.id, params.id),
        eq(articles.authorId, user.id),  // ✅ Must be author!
      )
    )
    .returning();
  
  // 3. Handle unauthorized
  if (!deleted) {
    return Response.json(
      { error: 'Article not found or unauthorized' },
      { status: 403 }
    );
  }
  
  return Response.json({ success: true });
}

// ✅ SAFE: Users can only delete their own articles!
```

> **💡 Pro Tip - The Authorization Pattern**
>
> ```typescript
> // ALWAYS use this pattern:
> const resource = await db.query.X.findFirst({
>   where: and(
>     eq(X.id, resourceId),     // Find the resource
>     eq(X.userId, user.id)     // Check ownership!
>   )
> });
>
> if (!resource) {
>   throw new Error('Not found or unauthorized');
> }
>
> // Now safe to operate on resource
> ```

---

### 2. Mass Assignment

> **⚠️ Mass Assignment = Trusting User Input Blindly**
>
> **The problem:**
> ```
> Form has: name, email
> User modifies request to include: role: "admin"
> Server blindly updates ALL fields
> User is now admin!
> ```

```typescript
// ❌ VULNERABLE - TRUSTS ALL INPUT
export async function updateProfile(formData: FormData) {
  const user = await requireAuth();
  
  // Convert FormData to object
  const updates = Object.fromEntries(formData);
  
  // 🔴 DANGER: User can send ANY field!
  // What if request includes:
  // { 
  //   displayName: "John",
  //   role: "admin",        ← Shouldn't be changeable!
  //   isVerified: true,     ← Shouldn't be changeable!
  //   credits: 9999         ← Shouldn't be changeable!
  // }
  
  await db.update(users)
    .set(updates)  // ❌ Sets ALL fields from input!
    .where(eq(users.id, user.id));
    
  return Response.json({ success: true });
}

// 🔴 DANGER: User can escalate to admin!
// Hacker can:
// 1. Open browser DevTools
// 2. Edit form to include role="admin"
// 3. Submit form
// 4. Now they're admin!
```

```typescript
// ✅ SECURE - WHITELIST ALLOWED FIELDS
export async function updateProfile(formData: FormData) {
  const user = await requireAuth();
  
  // ✅ Explicitly define allowed fields
  const allowedFields = {
    displayName: formData.get('displayName'),
    bio: formData.get('bio'),
    avatar: formData.get('avatar'),
    // role is NOT included!
    // isVerified is NOT included!
    // credits is NOT included!
  };
  
  // Validate each field
  if (typeof allowedFields.displayName !== 'string') {
    throw new Error('Invalid displayName');
  }
  
  await db.update(users)
    .set(allowedFields)  // ✅ Only safe fields!
    .where(eq(users.id, user.id));
    
  return Response.json({ success: true });
}

// ✅ SAFE: Only allowed fields can be updated!
```

> **💡 Pro Tip - Input Validation Pattern**
>
> ```typescript
> // ALWAYS:
> // 1. Whitelist allowed fields
> const allowed = {
>   field1: input.field1,
>   field2: input.field2,
>   // DON'T use ...input or Object.assign
> };
>
> // 2. Validate types
> if (typeof allowed.field1 !== 'string') {
>   throw new Error('Invalid input');
> }
>
> // 3. Validate values
> if (allowed.field1.length > 100) {
>   throw new Error('Too long');
> }
>
> // 4. Then use
> await db.update(table).set(allowed);
> ```

---

### 3. Broken Access Control

> **⚠️ Broken Access Control = Authentication Without Authorization**
>
> **The problem:**
> ```
> Check if user is logged in ✅
> But don't check if user is ADMIN ❌
> Regular user accesses admin panel!
> ```

```typescript
// ❌ VULNERABLE - ONLY CHECKS AUTHENTICATION
export default async function AdminPage() {
  const user = await requireAuth();
  
  // 🔴 DANGER: Any logged-in user can access!
  // No check for admin role!
  
  return (
    <div>
      <h1>Admin Panel</h1>
      <button onClick={deleteAllUsers}>Delete All Users</button>
      <button onClick={viewAllPasswords}>View Passwords</button>
    </div>
  );
}

// 🔴 DANGER: Any user can access admin features!
```

```typescript
// ✅ SECURE - CHECKS AUTHORIZATION
export default async function AdminPage() {
  // 1. Authenticate (WHO)
  const user = await requireAuth();
  
  // 2. Authorize (WHAT)
  const userWithRole = await db.query.users.findFirst({
    where: eq(users.id, user.id),
  });
  
  if (userWithRole.role !== 'admin') {
    redirect('/unauthorized');
  }
  
  return (
    <div>
      <h1>Admin Panel</h1>
      <button onClick={deleteAllUsers}>Delete All Users</button>
    </div>
  );
}

// ✅ SAFE: Only admins can access!
```

> **💡 Pro Tip - Create Authorization Helpers**
>
> ```typescript
> // lib/auth/permissions.ts
> export async function requireAdmin() {
>   const user = await requireAuth();
>   
>   if (user.role !== 'admin') {
>     throw new Error('Unauthorized');
>   }
>   
>   return user;
> }
>
> export async function requireOwnership(
>   resourceId: string,
>   resourceType: 'article' | 'comment'
> ) {
>   const user = await requireAuth();
>   
>   // Check ownership based on type
>   // ...
>   
>   return { user, resource };
> }
>
> // Then use:
> const user = await requireAdmin();
> const { user, article } = await requireOwnership(id, 'article');
> ```

> **⚡ Key Takeaway - Defense in Depth**
>
> **Always check BOTH:**
> 1. ✅ Authentication (WHO are you?)
> 2. ✅ Authorization (WHAT can you do?)
>
> **And always verify:**
> - Ownership (is this YOUR resource?)
> - Permissions (do you have the ROLE?)
> - State (is this resource in correct STATE?)
> - Context (is this action ALLOWED right now?)
>
> **Never assume:** Client won't send malicious data!

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

---

## 🧪 EXERCISES: Practice Security

> **📚 Security Practice**
>
> **Time needed:** 30-45 minutes total  
> **Difficulty:** Intermediate
>
> **Before you start:**
> - ✅ Stack Auth is set up
> - ✅ You understand AuthN vs AuthZ
> - ✅ You've seen the vulnerability examples
> - ✅ Have Drizzle schema ready
>
> **If you get stuck:**
> 1. Review the vulnerability examples above
> 2. Check if you're doing BOTH auth checks
> 3. Verify ownership checks use AND logic
> 4. Look at the solution (learning tool!)

---

### Exercise 1: Protected Page

**📝 Task:** Create a protected user profile page

**Requirements:**
- Requires authentication (redirect if not logged in)
- Shows user's information
- Lists all articles by this user
- Only the user can view their own profile

**⏱️ Time:** 10-15 minutes

<details>
<summary>💡 Hint</summary>

You'll need:
- `requireAuth()` to get the user
- `db.query.articles.findMany()` to get user's articles
- Filter articles by `authorId`
- Display user info and article list

</details>

<details>
<summary>✅ Solution</summary>

```typescript
// src/app/profile/page.tsx
import { requireAuth } from '@/lib/auth';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { eq, desc } from 'drizzle-orm';

export default async function ProfilePage() {
  // 1. Require authentication
  const user = await requireAuth();
  
  // 2. Get user's articles
  const userArticles = await db.query.articles.findMany({
    where: eq(articles.authorId, user.id),
    orderBy: [desc(articles.createdAt)],
  });
  
  return (
    <div className="container mx-auto px-4 py-8">
      <h1 className="text-3xl font-bold mb-4">
        {user.displayName}'s Profile
      </h1>
      <p className="text-gray-600 mb-8">Email: {user.email}</p>
      
      <h2 className="text-2xl font-bold mb-4">
        My Articles ({userArticles.length})
      </h2>
      
      <div className="grid gap-4">
        {userArticles.map((article) => (
          <div key={article.id} className="border p-4 rounded">
            <h3 className="text-xl font-semibold">{article.title}</h3>
            <p className="text-gray-500">{article.views} views</p>
            <p className="text-sm text-gray-400">
              {new Date(article.createdAt).toLocaleDateString()}
            </p>
          </div>
        ))}
      </div>
    </div>
  );
}
```

**What this does:**
- `requireAuth()` - Redirects to login if not authenticated
- `eq(articles.authorId, user.id)` - Only get THIS user's articles
- `desc(articles.createdAt)` - Newest first
- Protected route - can't access without login

</details>

---

### Exercise 2: Secure Edit Action (With Validation)

**📝 Task:** Create a server action that updates an article safely

**Requirements:**
- User must be authenticated
- User must OWN the article
- Validate input (title 1-200 chars, content 10-50k chars)
- Return the updated article
- Handle errors properly

**⏱️ Time:** 15-20 minutes  
**Difficulty:** 🔴 Intermediate

<details>
<summary>💡 Hint</summary>

You'll need:
- `requireAuth()` to authenticate
- `z.object()` from Zod to validate input
- `and()` to combine article.id AND author.id checks
- `.returning()` to get the updated article
- Check if result exists (authorization)

</details>

<details>
<summary>✅ Solution</summary>

```typescript
'use server';

import { requireAuth } from '@/lib/auth';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { eq, and } from 'drizzle-orm';
import { z } from 'zod';

// Define validation schema
const updateSchema = z.object({
  title: z.string().min(1, 'Title required').max(200, 'Title too long'),
  content: z.string().min(10, 'Content too short').max(50000, 'Content too long'),
  summary: z.string().max(500, 'Summary too long').optional(),
});

export async function updateArticle(
  articleId: string,
  data: { title: string; content: string; summary?: string }
) {
  // 1. Authenticate (WHO)
  const user = await requireAuth();
  
  // 2. Validate input (WHAT data)
  const validated = updateSchema.parse(data);
  
  // 3. Update with authorization (WHAT can they do)
  const [updated] = await db
    .update(articles)
    .set({
      ...validated,
      updatedAt: new Date(),
    })
    .where(
      and(
        eq(articles.id, articleId),       // This article
        eq(articles.authorId, user.id),   // User owns it
      )
    )
    .returning();
  
  // 4. Handle authorization failure
  if (!updated) {
    throw new Error('Article not found or you are not authorized to edit it');
  }
  
  return updated;
}
```

**What this does:**
- **Line by line:**
  1. `requireAuth()` - Authenticate user (WHO)
  2. `updateSchema.parse()` - Validate input (throws if invalid)
  3. `and()` - BOTH conditions must be true (id AND ownership)
  4. `if (!updated)` - Authorization failed (not found or not owned)

**Security checklist:**
- ✅ Authentication check (user logged in)
- ✅ Input validation (Zod schema)
- ✅ Authorization check (ownership)
- ✅ Error handling (clear messages)

</details>

---

### Exercise 3: Prevent IDOR Attack

**📝 Task:** Fix a vulnerable delete endpoint

**Given this vulnerable code:**
```typescript
// ❌ VULNERABLE
export async function DELETE(
  req: Request,
  { params }: { params: { id: string } }
) {
  await db.delete(articles).where(eq(articles.id, params.id));
  return Response.json({ success: true });
}
```

**Requirements:**
- Add authentication check
- Add authorization check (ownership)
- Return proper error for unauthorized
- Test that other users can't delete

**⏱️ Time:** 10-15 minutes

<details>
<summary>💡 Hint</summary>

Remember the IDOR pattern:
1. Get user (authentication)
2. Use `and()` to check both id AND ownership
3. Check if deletion succeeded
4. Return 403 if unauthorized

</details>

<details>
<summary>✅ Solution</summary>

```typescript
// ✅ SECURE
export async function DELETE(
  req: Request,
  { params }: { params: { id: string } }
) {
  // 1. Authenticate
  const user = await stackServerApp.getUser();
  
  if (!user) {
    return Response.json(
      { error: 'Not authenticated' },
      { status: 401 }
    );
  }
  
  // 2. Delete with authorization check
  const [deleted] = await db
    .delete(articles)
    .where(
      and(
        eq(articles.id, params.id),
        eq(articles.authorId, user.id),  // ✅ Ownership check!
      )
    )
    .returning();
  
  // 3. Handle authorization failure
  if (!deleted) {
    return Response.json(
      { error: 'Article not found or unauthorized' },
      { status: 403 }  // Forbidden
    );
  }
  
  return Response.json({ success: true });
}
```

**What changed:**
- ❌ Before: No auth, anyone could delete anything
- ✅ After: Must be authenticated AND owner

**Test it:**
```typescript
// User A (id: user_1) tries to delete User B's article:
DELETE /api/articles/article_belonging_to_user_2

// Result: 403 Forbidden ✅
// User A can't delete User B's article!

// User A tries to delete their own article:
DELETE /api/articles/article_belonging_to_user_1

// Result: 200 Success ✅
// User A can delete their own article!
```

</details>

---

### 🎯 Exercise Summary

**If you completed:**
- ✅ Exercise 1: You can protect pages!
- ✅ Exercise 2: You understand authorization checks!
- ✅ Exercise 3: You can prevent IDOR attacks!

**Security checklist for ALL endpoints:**
```typescript
// ALWAYS do this pattern:

// 1. Authenticate
const user = await requireAuth();

// 2. Validate input
const validated = schema.parse(input);

// 3. Authorize (check ownership)
const result = await db.operation()
  .where(and(
    eq(resource.id, id),
    eq(resource.userId, user.id)  // ← Critical!
  ));

// 4. Handle unauthorized
if (!result) {
  throw new Error('Unauthorized');
}

// 5. Do the thing
return result;
```

**Common mistakes to avoid:**
- ❌ Checking authentication but not authorization
- ❌ Trusting client input without validation
- ❌ Using OR instead of AND in ownership checks
- ❌ Forgetting to check if operation succeeded
- ❌ Not returning proper error codes (401 vs 403)

**Next steps:**
- Add authorization to all your endpoints
- Write tests for auth logic
- Review each endpoint for security
- Use Zod for all input validation

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
