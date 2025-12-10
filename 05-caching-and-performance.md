# Part 5: Caching & Performance  
## Making Your App Lightning Fast

> **Goal:** Master caching strategies and Redis implementation  
> **Time:** 5-7 hours  
> **Prerequisites:** Completed Parts 1-4

[← Back to Part 4](./04-authentication-security.md) | [Back to Index](./README.md) | [Next: Part 6 →](./06-services-and-integrations.md)

---

## What We'll Learn

✅ **Why caching matters** - Performance impact  
✅ **Caching strategies** - When and what to cache  
✅ **Redis fundamentals** - In-memory data store  
✅ **Upstash setup** - Serverless Redis (FREE!)  
✅ **View counter** - Real-time tracking  
✅ **Query caching** - Speed up database reads  
✅ **Cache invalidation** - The hard problem  

---

## The Problem: Slow Performance

### Without Caching 🔴

**Scenario:** Your WikiApp becomes popular!

```
10,000 users visit homepage
└─> Each requests "latest articles"
    └─> 10,000 database queries
        └─> Same query, same result!
        
Database:
├─> 100ms per query
├─> 10,000 queries = 1,000,000ms (16 minutes!)
├─> Database CPU: 95%
└─> App: Slow 🐌
```

**Problems:**
1. Database overloaded
2. Slow response times
3. Expensive compute costs
4. Poor user experience
5. May crash under load

### With Caching ✅

```
10,000 users visit homepage
└─> First request: Query database (100ms)
    └─> Cache result in Redis
    └─> Next 9,999 requests: Read from Redis (2ms)
    
Redis:
├─> 2ms per query  
├─> 10,000 queries = 20,000ms (20 seconds)
├─> Database CPU: 1%
└─> App: Fast! ⚡
```

**Results:**
- ✅ 50x faster (100ms → 2ms)
- ✅ 99% less database load
- ✅ Better user experience
- ✅ Lower costs
- ✅ Scales to millions

---

## Understanding Caching

### The Mental Model

Think of your brain reading a book:

**Without "cache" (memory):**
```
Need info from page 50
├─> Find book
├─> Open to page 50
├─> Read
└─> Close book

Need same info again
├─> Find book (again!)
├─> Open to page 50 (again!)
├─> Read (again!)
└─> Time wasted!
```

**With "cache" (memory):**
```
Need info from page 50
├─> Find book
├─> Read
└─> Remember it! 🧠

Need same info again
└─> Recall from memory! ⚡
    (No book needed)
```

### Caching Layers

Modern apps have multiple cache layers:

```
User Request
│
├─> Browser Cache (Static files)
│   └─> Images, CSS, JS
│
├─> CDN Cache (Edge)
│   └─> Pages, API responses
│
├─> Application Cache (Redis)
│   └─> Database queries, computations
│
└─> Database Cache (Query cache)
    └─> Query results
```

**Each layer makes things faster!**

---

## When to Cache?

### The Decision Framework

✅ **Cache when:**
- Read much more than written (10:1 ratio+)
- Data doesn't change often
- Computation is expensive
- Database queries are slow
- Same data requested repeatedly

❌ **Don't cache when:**
- Data changes constantly
- Must be real-time
- Unique per user (unless user-keyed)
- Privacy/security sensitive
- Cache overhead > computation cost

### For WikiApp

**Should cache:**
- ✅ Article list (changes rarely)
- ✅ Individual articles (change occasionally)
- ✅ View counts (aggregate, not critical)
- ✅ User profiles (change rarely)

**Should NOT cache:**
- ❌ User authentication state
- ❌ Form submissions
- ❌ Payment processing
- ❌ Real-time chat

---

## Setting Up Redis

### What is Redis?

**Redis = Remote Dictionary Server**

It's an in-memory data store:

```
Traditional Database (Postgres):
├─> Stores data on disk
├─> Reads from disk (slow)
└─> 50-200ms per query

Redis:
├─> Stores data in RAM
├─> Reads from memory (fast!)
└─> 1-5ms per query
```

**Use cases:**
- Caching
- Session storage
- Real-time analytics
- Rate limiting
- Pub/sub messaging

### Why Upstash?

**Traditional Redis:**
```
You manage:
├─> Server setup
├─> Scaling
├─> Backups
├─> Security
└─> Cost: $50+/month
```

**Upstash (Serverless Redis):**
```
Upstash manages everything
Cost: FREE (10k commands/day)
```

### Step 1: Create Upstash Account

1. Go to [upstash.com](https://upstash.com)
2. Sign up (no credit card!)
3. Create database: `wikiapp-cache`
4. Select region (closest to you)

### Step 2: Get Credentials

You'll see:

```
REST URL: https://us1-living-dog-12345.upstash.io
REST Token: AYABcd...xyz123
```

### Step 3: Add to Environment

```bash
# .env.local
UPSTASH_REDIS_REST_URL="https://..."
UPSTASH_REDIS_REST_TOKEN="AYABcd..."
```

### Step 4: Initialize Redis Client

Create `src/lib/redis.ts`:

```typescript
import { Redis } from '@upstash/redis';

export const redis = new Redis({
  url: process.env.UPSTASH_REDIS_REST_URL!,
  token: process.env.UPSTASH_REDIS_REST_TOKEN!,
});

// Cache keys (namespaced)
export const CACHE_KEYS = {
  article: (id: string) => `article:${id}`,
  articles: 'articles:all',
  views: (id: string) => `views:${id}`,
  user: (id: string) => `user:${id}`,
};

// Cache TTL (time-to-live in seconds)
export const CACHE_TTL = {
  article: 60 * 5,      // 5 minutes
  articles: 60,         // 1 minute
  user: 60 * 10,        // 10 minutes
};
```

**Understanding TTL:**

```
TTL = Time To Live

Set cache with TTL: 60 seconds
├─> 0-59 seconds: Read from cache ⚡
└─> 60 seconds: Cache expires, query DB again
```

---

## Redis Basic Operations

### SET: Store Data

```typescript
// Set simple value
await redis.set('key', 'value');

// Set with expiration (60 seconds)
await redis.setex('key', 60, 'value');

// Set JSON data
await redis.set('user:123', JSON.stringify({
  name: 'John',
  email: 'john@example.com'
}));
```

### GET: Retrieve Data

```typescript
// Get value
const value = await redis.get('key');
// Returns: 'value' or null

// Get and parse JSON
const userStr = await redis.get('user:123');
const user = userStr ? JSON.parse(userStr) : null;
```

### DEL: Delete Data

```typescript
// Delete single key
await redis.del('key');

// Delete multiple keys
await redis.del('key1', 'key2', 'key3');
```

### INCR: Increment Counter

```typescript
// Increment by 1
const count = await redis.incr('views:article_123');
// First call: returns 1
// Second call: returns 2
// Third call: returns 3

// Increment by amount
const views = await redis.incrby('views:article_123', 5);
```

### EXPIRE: Set Expiration

```typescript
// Set expiration on existing key
await redis.expire('key', 60);  // Expires in 60 seconds
```

---

## Implementing View Counter

### Why Redis for View Counters?

**Requirements:**
- ✅ Increment quickly
- ✅ Handle many requests
- ✅ Don't overload database
- ✅ Eventually consistent (OK if slightly off)

**Redis is perfect!**

```
Old way (database):
User views article
└─> UPDATE articles SET views = views + 1
    └─> Write to disk (slow!)
    └─> 1000 views = 1000 disk writes 😰

New way (Redis):
User views article  
└─> INCR views:article_123
    └─> Update in memory (fast!)
    └─> 1000 views = 1000 memory ops ⚡
    └─> Periodically sync to database
```

### Create View Actions

Create `src/lib/actions/views.ts`:

```typescript
'use server';

import { redis } from '@/lib/redis';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { eq } from 'drizzle-orm';

export async function incrementViews(articleId: string) {
  try {
    // Increment in Redis
    const views = await redis.incr(`views:${articleId}`);
    
    // Sync to database every 10 views
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

export async function getViews(articleId: string) {
  try {
    const views = await redis.get<number>(`views:${articleId}`);
    return views || 0;
  } catch (error) {
    console.error('Failed to get views:', error);
    return 0;
  }
}
```

**Why sync every 10 views?**

```
Database writes are expensive:
├─> Disk I/O
├─> Index updates
└─> Transaction overhead

By batching:
├─> 100 views = 10 DB writes (instead of 100)
└─> 90% less database load!
```

### Create View Counter Component

Create `src/components/view-counter.tsx`:

```typescript
'use client';

import { useEffect, useState } from 'react';
import { incrementViews } from '@/lib/actions/views';
import { Eye } from 'lucide-react';

export function ViewCounter({ articleId }: { articleId: string }) {
  const [views, setViews] = useState(0);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    async function updateViews() {
      try {
        const newViews = await incrementViews(articleId);
        setViews(newViews);
      } catch (error) {
        console.error('Error:', error);
      } finally {
        setLoading(false);
      }
    }
    
    updateViews();
  }, [articleId]);
  
  return (
    <div className="flex items-center gap-2 text-muted-foreground">
      <Eye className="h-4 w-4" />
      <span>
        {loading ? '...' : views.toLocaleString()} views
      </span>
    </div>
  );
}
```

### Use in Article Page

```typescript
// src/app/articles/[id]/page.tsx
import { ViewCounter } from '@/components/view-counter';

export default async function ArticlePage({ 
  params 
}: { 
  params: { id: string } 
}) {
  const article = await getArticle(params.id);
  
  return (
    <article>
      <h1>{article.title}</h1>
      <ViewCounter articleId={article.id} />
      <div>{article.content}</div>
    </article>
  );
}
```

---

## Caching Database Queries

### Pattern: Cache-Aside

```
1. Check cache
   ├─> Hit? Return cached data ⚡
   └─> Miss? Continue...

2. Query database
   └─> Get fresh data

3. Store in cache
   └─> For next time

4. Return data
```

### Implementation

```typescript
// src/lib/db/queries.ts
import { redis, CACHE_KEYS, CACHE_TTL } from '@/lib/redis';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { desc } from 'drizzle-orm';

export async function getAllArticles() {
  // 1. Try cache first
  const cached = await redis.get(CACHE_KEYS.articles);
  
  if (cached) {
    console.log('✅ Cache hit!');
    return JSON.parse(cached as string);
  }
  
  console.log('❌ Cache miss, querying database...');
  
  // 2. Query database
  const allArticles = await db.query.articles.findMany({
    with: { author: true },
    orderBy: [desc(articles.createdAt)],
  });
  
  // 3. Store in cache
  await redis.setex(
    CACHE_KEYS.articles,
    CACHE_TTL.articles,
    JSON.stringify(allArticles)
  );
  
  // 4. Return data
  return allArticles;
}
```

**Performance impact:**

```
First request:
├─> Cache miss
├─> Query database: 100ms
├─> Store in cache: 2ms
└─> Total: 102ms

Next 1,000 requests (within 60 seconds):
├─> Cache hit
├─> Read from Redis: 2ms
└─> Total: 2ms each

Result: 50x faster! ⚡
```

### Cache Single Article

```typescript
export async function getArticleById(id: string) {
  const cacheKey = CACHE_KEYS.article(id);
  
  // Try cache
  const cached = await redis.get(cacheKey);
  if (cached) {
    return JSON.parse(cached as string);
  }
  
  // Query database
  const article = await db.query.articles.findFirst({
    where: eq(articles.id, id),
    with: { author: true },
  });
  
  if (!article) {
    throw new Error('Article not found');
  }
  
  // Cache for 5 minutes
  await redis.setex(
    cacheKey,
    CACHE_TTL.article,
    JSON.stringify(article)
  );
  
  return article;
}
```

---

## Cache Invalidation

### The Hard Problem

> "There are only two hard things in Computer Science: cache invalidation and naming things."  
> — Phil Karlton

**Why is it hard?**

```
User edits article
├─> Database updated ✅
├─> Cache still has old data ❌
└─> Users see stale content!

Solution: Invalidate cache when data changes
```

### Strategies

#### 1. Time-Based Expiration (TTL)

```typescript
// Cache expires after 5 minutes
await redis.setex(key, 300, data);

// Pros: Simple, automatic
// Cons: Stale data for up to 5 minutes
```

#### 2. Manual Invalidation

```typescript
export async function updateArticle(id: string, data: any) {
  // Update database
  await db.update(articles).set(data).where(eq(articles.id, id));
  
  // Invalidate caches
  await redis.del(CACHE_KEYS.article(id));
  await redis.del(CACHE_KEYS.articles);
  
  // Next request will get fresh data
}

// Pros: Immediate consistency
// Cons: Must remember to invalidate
```

#### 3. Cache Tags

```typescript
// Tag related caches
await redis.set('article:123', data, {
  tags: ['articles', 'user:456'],
});

// Invalidate by tag
await redis.invalidateTag('articles');
// All articles caches cleared!

// Pros: Flexible, powerful
// Cons: More complex
```

### WikiApp Implementation

```typescript
// src/lib/actions/articles.ts
'use server';

import { redis, CACHE_KEYS } from '@/lib/redis';

export async function updateArticle(
  id: string, 
  data: { title: string; content: string }
) {
  const user = await requireAuth();
  
  // Update database
  const [updated] = await db.update(articles)
    .set({ ...data, updatedAt: new Date() })
    .where(
      and(
        eq(articles.id, id),
        eq(articles.authorId, user.id),
      )
    )
    .returning();
  
  if (!updated) {
    throw new Error('Unauthorized or not found');
  }
  
  // Invalidate caches
  await Promise.all([
    redis.del(CACHE_KEYS.article(id)),      // This article
    redis.del(CACHE_KEYS.articles),         // Article list
    redis.del(CACHE_KEYS.user(user.id)),    // User profile
  ]);
  
  return updated;
}
```

---

## Rate Limiting with Redis

Prevent abuse with rate limiting:

```typescript
// src/lib/redis.ts
import { Ratelimit } from '@upstash/ratelimit';

export const ratelimit = new Ratelimit({
  redis,
  limiter: Ratelimit.slidingWindow(10, '10 s'),
  analytics: true,
});
```

**Use in actions:**

```typescript
export async function createArticle(formData: FormData) {
  const user = await requireAuth();
  
  // Check rate limit: 10 articles per 10 seconds
  const { success } = await ratelimit.limit(user.id);
  
  if (!success) {
    throw new Error('Rate limit exceeded. Slow down!');
  }
  
  // Proceed with creation
  await db.insert(articles).values({...});
}
```

---

## Best Practices

### 1. Cache Keys Should Be Descriptive

```typescript
// ❌ Bad
await redis.set('a1', data);

// ✅ Good
await redis.set('article:123', data);
await redis.set('user:456:profile', data);
```

### 2. Always Set TTL

```typescript
// ❌ Bad: Cache forever
await redis.set('key', data);

// ✅ Good: Cache with expiration
await redis.setex('key', 300, data);
```

### 3. Handle Cache Failures Gracefully

```typescript
// ✅ Always wrap in try-catch
try {
  const cached = await redis.get(key);
  if (cached) return cached;
} catch (error) {
  console.error('Cache error:', error);
  // Fall through to database
}

// Query database as fallback
return await db.query.articles.findMany();
```

### 4. Cache High-Traffic Endpoints

```typescript
// Cache expensive queries
export async function getPopularArticles() {
  const cacheKey = 'articles:popular';
  
  try {
    const cached = await redis.get(cacheKey);
    if (cached) return JSON.parse(cached);
  } catch {}
  
  // Complex query with joins and aggregations
  const popular = await db.query.articles.findMany({
    with: { author: true, comments: true },
    where: gt(articles.views, 1000),
    orderBy: [desc(articles.views)],
    limit: 10,
  });
  
  await redis.setex(cacheKey, 300, JSON.stringify(popular));
  return popular;
}
```

---

## 🧠 CHECKPOINT: Understanding Caching

Before moving on, make sure you can explain:

1. **Why do we need caching?**
   - Database queries are slow (50-200ms)
   - Redis is fast (1-5ms)
   - Same data requested repeatedly
   - Reduces database load by 90%+

2. **What is cache-aside pattern?**
   - Check cache first
   - If miss, query database
   - Store in cache for next time
   - Return data

3. **What is cache invalidation?**
   - Removing stale data from cache
   - Happens when data changes
   - The "hard problem" in CS
   - Options: TTL, manual, tags

4. **When should you cache?**
   - ✅ Read-heavy data (10:1 ratio)
   - ✅ Slow queries
   - ✅ Frequently accessed
   - ❌ Real-time required
   - ❌ Constantly changing

5. **What is TTL?**
   - Time To Live
   - How long cache stays valid
   - After TTL expires, cache clears
   - Forces fresh data fetch

**Take a moment to explain each concept out loud.**

---

## 🧪 EXERCISES: Practice Caching

### Exercise 1: Cache User Profile

Implement caching for user profile queries:

```typescript
export async function getUserProfile(userId: string) {
  // Your code here:
  // 1. Check cache
  // 2. If miss, query database
  // 3. Store in cache (5 min TTL)
  // 4. Return user
}
```

<details>
<summary>💡 Solution</summary>

```typescript
import { redis, CACHE_KEYS, CACHE_TTL } from '@/lib/redis';
import { db } from '@/lib/db';
import { users } from '@/lib/db/schema';
import { eq } from 'drizzle-orm';

export async function getUserProfile(userId: string) {
  const cacheKey = CACHE_KEYS.user(userId);
  
  // 1. Try cache first
  try {
    const cached = await redis.get(cacheKey);
    if (cached) {
      console.log('✅ Cache hit!');
      return JSON.parse(cached as string);
    }
  } catch (error) {
    console.error('Cache error:', error);
  }
  
  console.log('❌ Cache miss, querying database...');
  
  // 2. Query database
  const user = await db.query.users.findFirst({
    where: eq(users.id, userId),
    with: {
      articles: {
        orderBy: [desc(articles.createdAt)],
        limit: 5,
      },
    },
  });
  
  if (!user) {
    throw new Error('User not found');
  }
  
  // 3. Store in cache (5 minutes)
  try {
    await redis.setex(
      cacheKey,
      CACHE_TTL.user,
      JSON.stringify(user)
    );
  } catch (error) {
    console.error('Failed to cache:', error);
  }
  
  // 4. Return user
  return user;
}
```
</details>

### Exercise 2: Invalidate on Update

Add cache invalidation to the update profile action:

```typescript
export async function updateProfile(userId: string, data: any) {
  // Update database
  await db.update(users).set(data).where(eq(users.id, userId));
  
  // Your code here:
  // Invalidate the user's cache
}
```

<details>
<summary>💡 Solution</summary>

```typescript
export async function updateProfile(
  userId: string, 
  data: { displayName?: string; bio?: string }
) {
  // Update database
  const [updated] = await db.update(users)
    .set(data)
    .where(eq(users.id, userId))
    .returning();
  
  // Invalidate cache
  await redis.del(CACHE_KEYS.user(userId));
  
  // Also invalidate articles list if user is an author
  await redis.del(CACHE_KEYS.articles);
  
  return updated;
}
```

**Why invalidate both?**
- User profile cache has stale name
- Article list shows old author name
- Both need fresh data!
</details>

### Exercise 3: Popular Articles with Cache

Implement a cached query for popular articles:

```typescript
export async function getPopularArticles() {
  // Your code here:
  // 1. Cache key: 'articles:popular'
  // 2. TTL: 5 minutes
  // 3. Query: articles with views > 100
  // 4. Include author
  // 5. Limit: 10
}
```

<details>
<summary>💡 Solution</summary>

```typescript
export async function getPopularArticles() {
  const cacheKey = 'articles:popular';
  
  // Try cache
  try {
    const cached = await redis.get(cacheKey);
    if (cached) return JSON.parse(cached as string);
  } catch (error) {
    console.error('Cache error:', error);
  }
  
  // Query database
  const popular = await db.query.articles.findMany({
    where: gt(articles.views, 100),
    with: { author: true },
    orderBy: [desc(articles.views)],
    limit: 10,
  });
  
  // Cache for 5 minutes
  try {
    await redis.setex(cacheKey, 300, JSON.stringify(popular));
  } catch (error) {
    console.error('Failed to cache:', error);
  }
  
  return popular;
}
```
</details>

### Exercise 4: Rate Limit API Route

Implement rate limiting for an API endpoint:

```typescript
// src/app/api/articles/route.ts
export async function POST(request: Request) {
  // Your code here:
  // 1. Get user ID from auth
  // 2. Check rate limit (5 requests per minute)
  // 3. If exceeded, return 429
  // 4. Otherwise, create article
}
```

<details>
<summary>💡 Solution</summary>

```typescript
import { Ratelimit } from '@upstash/ratelimit';
import { redis } from '@/lib/redis';
import { requireAuth } from '@/lib/auth';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';

// Create rate limiter: 5 requests per 60 seconds
const ratelimit = new Ratelimit({
  redis,
  limiter: Ratelimit.slidingWindow(5, '60 s'),
});

export async function POST(request: Request) {
  // 1. Get user from auth
  const user = await requireAuth();
  
  // 2. Check rate limit
  const { success, limit, reset, remaining } = await ratelimit.limit(user.id);
  
  // 3. If exceeded, return 429
  if (!success) {
    return Response.json(
      { 
        error: 'Rate limit exceeded',
        limit,
        remaining,
        reset: new Date(reset),
      },
      { 
        status: 429,
        headers: {
          'X-RateLimit-Limit': limit.toString(),
          'X-RateLimit-Remaining': remaining.toString(),
          'X-RateLimit-Reset': reset.toString(),
        },
      }
    );
  }
  
  // 4. Create article
  const body = await request.json();
  
  const [article] = await db.insert(articles)
    .values({
      ...body,
      authorId: user.id,
    })
    .returning();
  
  return Response.json(article, { status: 201 });
}
```
</details>

---

## 🧠 CHECKPOINT: Understanding Caching

Before moving on, make sure you can explain:

1. **What is caching and why does it matter?**
   - What problem does it solve?
   - What's the performance difference?

2. **When should you cache data?**
   - What's the 10:1 ratio rule?
   - When should you NOT cache?

3. **How does Redis differ from a database?**
   - Memory vs disk storage
   - Speed difference
   - Use cases for each

4. **What is cache invalidation?**
   - Why is it called "the hard problem"?
   - What are the main strategies?

5. **How does the view counter work?**
   - Why Redis instead of direct database?
   - Why sync every 10 views?

**Explain each concept out loud using the Feynman Technique.**

---

## 🧪 EXERCISES: Practice Caching

### Exercise 1: Cache User Profiles

Implement caching for user profiles:

```typescript
// Your task: Add caching to this function
export async function getUserProfile(userId: string) {
  // TODO: Check cache first
  // TODO: If miss, query database
  // TODO: Store in cache
  // TODO: Return user
}
```

**Hints:**
- Cache key: `user:${userId}`
- TTL: 10 minutes
- Use JSON.stringify/parse

<details>
<summary>Solution</summary>

```typescript
import { redis, CACHE_KEYS, CACHE_TTL } from '@/lib/redis';
import { db } from '@/lib/db';
import { users } from '@/lib/db/schema';
import { eq } from 'drizzle-orm';

export async function getUserProfile(userId: string) {
  const cacheKey = CACHE_KEYS.user(userId);
  
  // 1. Try cache
  try {
    const cached = await redis.get(cacheKey);
    if (cached) {
      console.log('✅ Cache hit: user profile');
      return JSON.parse(cached as string);
    }
  } catch (error) {
    console.error('Cache error:', error);
  }
  
  console.log('❌ Cache miss: querying database');
  
  // 2. Query database
  const user = await db.query.users.findFirst({
    where: eq(users.id, userId),
  });
  
  if (!user) {
    throw new Error('User not found');
  }
  
  // 3. Store in cache
  try {
    await redis.setex(
      cacheKey,
      CACHE_TTL.user,
      JSON.stringify(user)
    );
  } catch (error) {
    console.error('Cache set error:', error);
  }
  
  // 4. Return user
  return user;
}
```
</details>

### Exercise 2: Implement Rate Limiting

Create a rate limiter for article creation:

```typescript
// Allow 5 articles per hour per user
export async function checkArticleRateLimit(userId: string): Promise<boolean> {
  // TODO: Implement using Redis
  // TODO: Return true if allowed, false if rate limited
}
```

**Hints:**
- Key: `ratelimit:articles:${userId}`
- Use INCR command
- Set expiry to 1 hour

<details>
<summary>Solution</summary>

```typescript
import { redis } from '@/lib/redis';

export async function checkArticleRateLimit(
  userId: string
): Promise<boolean> {
  const key = `ratelimit:articles:${userId}`;
  const limit = 5;
  const window = 60 * 60; // 1 hour in seconds
  
  try {
    // Get current count
    const current = await redis.get(key);
    
    if (!current) {
      // First request in window
      await redis.setex(key, window, '1');
      return true;
    }
    
    const count = parseInt(current as string);
    
    if (count >= limit) {
      return false; // Rate limited
    }
    
    // Increment counter
    await redis.incr(key);
    return true;
  } catch (error) {
    console.error('Rate limit check failed:', error);
    return true; // Fail open (allow on error)
  }
}

// Use in article creation:
export async function createArticle(formData: FormData) {
  const user = await requireAuth();
  
  const allowed = await checkArticleRateLimit(user.id);
  if (!allowed) {
    throw new Error('Rate limit: Max 5 articles per hour');
  }
  
  // Create article...
}
```
</details>

### Exercise 3: Cache Invalidation Strategy

You need to invalidate related caches when data changes. Complete this function:

```typescript
export async function updateArticle(id: string, data: any) {
  // 1. Update database
  const updated = await db.update(articles)...
  
  // 2. TODO: Invalidate all related caches
  // - The article itself
  // - Article list
  // - Author's profile
  
  return updated;
}
```

<details>
<summary>Solution</summary>

```typescript
export async function updateArticle(
  id: string,
  data: { title: string; content: string }
) {
  const user = await requireAuth();
  
  // 1. Update database
  const [updated] = await db.update(articles)
    .set({
      ...data,
      updatedAt: new Date(),
    })
    .where(
      and(
        eq(articles.id, id),
        eq(articles.authorId, user.id),
      )
    )
    .returning();
  
  if (!updated) {
    throw new Error('Not found or unauthorized');
  }
  
  // 2. Invalidate related caches
  await Promise.all([
    // Article cache
    redis.del(CACHE_KEYS.article(id)),
    
    // Article list cache
    redis.del(CACHE_KEYS.articles),
    
    // Author profile cache (has article list)
    redis.del(CACHE_KEYS.user(user.id)),
    
    // View count cache (might be stale)
    redis.del(`views:${id}`),
  ]);
  
  console.log('✅ Invalidated related caches');
  
  return updated;
}
```
</details>

### Exercise 4: Debug Cache Issues

**Scenario:** Users report seeing stale article data. What could be wrong?

Think through these questions:
1. Is the cache key correct?
2. Is the TTL appropriate?
3. Are we invalidating on updates?
4. Could it be a race condition?

Write down your debugging steps.

<details>
<summary>Debugging Approach</summary>

**Step 1: Verify Cache Keys**
```typescript
console.log('Cache key:', CACHE_KEYS.article(id));
// Make sure it matches across get/set/delete
```

**Step 2: Check TTL**
```typescript
const ttl = await redis.ttl(CACHE_KEYS.article(id));
console.log('TTL remaining:', ttl, 'seconds');
// If TTL is too long, old data persists
```

**Step 3: Verify Invalidation**
```typescript
// Add logging to update function
console.log('Invalidating cache for article:', id);
await redis.del(CACHE_KEYS.article(id));
console.log('Cache deleted');
```

**Step 4: Test Cache Flow**
```typescript
// Manual test
const key = CACHE_KEYS.article('test');

// Set cache
await redis.set(key, JSON.stringify({ title: 'Old' }));

// Update article
await updateArticle('test', { title: 'New' });

// Check cache (should be empty or new data)
const cached = await redis.get(key);
console.log('After update:', cached); // Should be null
```

**Common Issues:**
1. ❌ Typo in cache key
2. ❌ Forgetting to invalidate
3. ❌ TTL too long (hours instead of minutes)
4. ❌ Multiple cache layers not synced
5. ❌ Race condition (read old, write new, read cached old)
</details>

---

## Summary

You now understand:

✅ **Why caching matters** - 50x faster responses  
✅ **When to cache** - Decision frameworks  
✅ **Redis basics** - In-memory data store  
✅ **View counters** - Real-time tracking  
✅ **Query caching** - Cache-aside pattern  
✅ **Cache invalidation** - Keeping data fresh  
✅ **Rate limiting** - Preventing abuse  

### Key Takeaways

1. **Caching dramatically improves** performance
2. **Redis is perfect** for caching (fast!)
3. **Cache frequently accessed data** that doesn't change often
4. **Always set TTL** to prevent stale data
5. **Invalidate cache** when data changes
6. **Handle failures gracefully** - cache is optional
7. **Monitor cache hit rates** - adjust strategies

[→ Continue to Part 6: Services & Integrations](./06-services-and-integrations.md)

---

**Excellent work on Part 5!** Your app is now blazing fast! ⚡
