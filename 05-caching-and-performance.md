# Part 5: Caching & Performance  
## Making Your App Lightning Fast

> **Goal:** Master caching strategies and Redis implementation  
> **Time:** 5-7 hours  
> **Prerequisites:** Completed Parts 1-4

[← Back to Part 4](./04-authentication-security.md) | [Back to Index](./README.md) | [Next: Part 6 →](./06-services-and-integrations.md)

---

> **📚 TL;DR - What You'll Build**
>
> By the end, you'll have:
> - ✅ Understanding of caching fundamentals
> - ✅ Redis setup with Upstash (serverless, FREE)
> - ✅ Real-time view counter (50x faster!)
> - ✅ Query caching (instant reads)
> - ✅ Cache invalidation strategies
>
> **Result:** Your app becomes 50-100x faster! ⚡

---

## 📍 Progress: Part 5 of 7

```
[████████████████████░░░] 71% Complete (after this part!)
Part 1 ✅ | Part 2 ✅ | Part 3 ✅ | Part 4 ✅ | Part 5 📍 YOU ARE HERE
```

**The home stretch!** Only 2 parts left after this! 🎉

---

## ⏱️ Time Breakdown

- **Understanding Caching:** 1-2 hours (concepts)
- **Redis & Upstash Setup:** 1 hour (implementation)
- **View Counter:** 1-2 hours (real-time feature)
- **Query Caching:** 1-2 hours (optimization)
- **Testing & Exercises:** 1 hour (practice)

**Total:** 5-7 hours (Performance optimization is worth it!)

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

> **💡 The Performance Problem**
>
> **Scenario:** Your app goes viral overnight!
> 
> **Without caching:**
> - Same query runs 10,000 times
> - Database CPU: 95% (near crash!)
> - Response time: 100-500ms (slow!)
> 
> **With caching:**
> - Query runs once, cached 9,999 times
> - Database CPU: 1% (relaxed!)
> - Response time: 2ms (lightning!)
>
> **Impact:** 50-100x performance improvement! ⚡

---

### Without Caching 🐌

**Scenario:** Your WikiApp becomes popular! 10,000 concurrent users.

```
10,000 users visit homepage
│
└─> Each requests "latest articles"
    └─> 10,000 identical database queries
        └─> Same query, same result! (wasteful!)
        
Database struggles:
├─> 100ms per query (network + query time)
├─> 10,000 queries = 1,000,000ms (16+ minutes!)
├─> CPU usage: 95% (danger zone!)
├─> Connection pool exhausted
├─> Queue building up...
└─> App: Slow, may crash 🔴
```

**Problems:**
1. ❌ **Database overload** - 95%+ CPU usage
2. ❌ **Slow response** - Users wait 100-500ms
3. ❌ **Expensive costs** - Database compute bills
4. ❌ **Poor UX** - Frustrated users leave
5. ❌ **May crash** - Too many connections
6. ❌ **Wasteful** - Same query, same result, 10,000 times!

> **⚠️ Real-World Impact**
>
> **Without caching, popular apps can:**
> - Cost $1,000+/month in database fees
> - Crash under viral traffic (Reddit hug of death)
> - Lose users due to slow experience
> - Fail to scale beyond 1,000 users
>
> **Twitter, Reddit, Facebook all use aggressive caching!**

---

### With Caching ⚡

```
10,000 users visit homepage
│
├─> User #1: Query database (100ms)
│   └─> Store result in Redis cache
│   └─> Return to user (100ms total)
│
└─> Users #2-10,000: Read from Redis (2ms each!)
    └─> No database hit!
    └─> Return cached result (2ms total)
    
Redis (in-memory):
├─> 2ms per read (in RAM, super fast!)
├─> 10,000 reads = 20,000ms (20 seconds total)
├─> Database queries: 1 (just the first!)
├─> Database CPU: 1% (chill mode)
└─> App: Lightning fast! ⚡
```

**Results:**
- ✅ **50x faster** - 100ms → 2ms per request
- ✅ **99% less DB load** - 1 query instead of 10,000
- ✅ **Better UX** - Instant responses
- ✅ **Lower costs** - $50/month instead of $1,000/month
- ✅ **Scales infinitely** - Redis handles millions of reads

> **⚡ Key Insight - The Power of Caching**
>
> **Caching is like having a cheat sheet:**
> - First student solves problem (100ms) ✏️
> - Writes answer on board (cached) 📝
> - Other 9,999 students copy answer (2ms) 📋
>
> **Everyone gets the answer 50x faster!**

---

## Understanding Caching

> **📚 TL;DR - What Is Caching?**
>
> **Caching = Saving results to avoid repeating work**
>
> **Perfect analogy:**
> - Restaurant menu (cache) vs asking chef every time (database)
> - Phone contacts (cache) vs phone book every time (database)
> - Brain memory (cache) vs re-reading book (database)
>
> **Key principle:** Do expensive work once, reuse the result!

---

### The Restaurant Menu Analogy

> **🍕 Think of Caching Like a Restaurant Menu**

**Without cache (no menu):**
```
Customer: "What do you have?"
Waiter: "Let me ask the chef..." 
  └─> Goes to kitchen (slow!)
  └─> Chef lists all 50 dishes (expensive!)
  └─> Waiter returns (5 minutes!)
Customer: "What about prices?"
Waiter: "Let me ask again..."
  └─> Another trip to kitchen! (wasteful!)

Result: Everyone asks, everyone waits. Chef is exhausted!
```

**With cache (menu):**
```
Customer: "What do you have?"
Waiter: "Here's the menu!" (instant!)
  └─> No kitchen trip needed
  └─> Chef can focus on cooking
  └─> Customer gets info immediately

Result: Instant answers, happy chef, happy customers!
```

**The menu IS a cache!**
- Created once (expensive)
- Read many times (cheap)
- Updated when dishes change (invalidation)

---

### Your Brain's Memory = A Cache

Think of reading a book:

**Without "cache" (no memory):**
```
Need info from page 50
├─> Find the book on shelf (slow!)
├─> Open to page 50 (time consuming)
├─> Read the information
└─> Close book

Need SAME info again (5 minutes later)
├─> Find the book AGAIN! (why?!)
├─> Open to page 50 AGAIN! (wasteful!)
├─> Read AGAIN! (redundant!)
└─> Time wasted! ⏰
```

**With "cache" (memory):**
```
Need info from page 50
├─> Find book
├─> Read once
└─> Remember it! 🧠 (cached in brain!)

Need SAME info again
└─> Recall from memory! ⚡ (instant!)
    No book needed!
    No time wasted!
```

> **💡 Pro Tip - Caching in Everyday Life**
>
> **You already use caching daily:**
> - **Phone contacts** = cache (vs looking up in phone book)
> - **Bookmarks** = cache (vs searching every time)
> - **Shortcuts** = cache (vs typing full path)
> - **Templates** = cache (vs creating from scratch)
> - **Recipes you memorize** = cache (vs reading cookbook)
>
> **Caching is natural and everywhere!**

---

### Caching Layers (The Speed Hierarchy)

Modern apps have multiple cache layers, each faster than the last:

```
User Request (New York)
│
├─> Layer 1: Browser Cache (Your Computer)
│   └─> Static files: images, CSS, JS
│   └─> Speed: 0ms (instant!)
│   └─> Scope: Just you
│
├─> Layer 2: CDN Cache (Edge - Nearby Server)
│   └─> Pages, API responses
│   └─> Speed: 10-50ms (very fast)
│   └─> Scope: Everyone in your region
│
├─> Layer 3: Application Cache (Redis)
│   └─> Database queries, computations
│   └─> Speed: 2-10ms (fast)
│   └─> Scope: All users
│
└─> Layer 4: Database (Postgres)
    └─> Query results, indexes
    └─> Speed: 50-200ms (slowest)
    └─> Scope: Source of truth
```

**Each layer makes things faster!**

> **⚡ Speed Comparison**
>
> **Typical latencies:**
> - Browser cache: 0ms (instant!)
> - CDN edge: 10-50ms (regional)
> - Redis: 2-10ms (in-memory)
> - Database: 50-200ms (disk + network)
>
> **The closer to the user, the faster!**

---

### Cache Hit vs Cache Miss

**Important concepts:**

```
Cache Hit (Found in cache) ✅
User requests article #123
├─> Check Redis first
├─> Found! Return immediately (2ms)
└─> Database not touched

Result: Fast response! ⚡
```

```
Cache Miss (Not in cache) 🔄
User requests article #456
├─> Check Redis first
├─> Not found! (cache miss)
├─> Query database (100ms)
├─> Store in Redis (for next time)
└─> Return to user

Result: Slow this time, fast next time!
```

> **📊 Hit Rate = Performance**
>
> **Hit rate** = % of requests served from cache
>
> - 90% hit rate = 90% of requests fast! (great!)
> - 50% hit rate = 50% of requests fast (okay)
> - 10% hit rate = 10% of requests fast (poor)
>
> **Goal:** Maximize hit rate by caching the right things!

---

---

### ☕ Quick Break (5 minutes)

**You've learned the caching fundamentals!**

**Covered so far:**
- ✅ Why caching matters (50x faster!)
- ✅ Perfect analogies (menu, brain memory)
- ✅ Cache layers (browser → CDN → Redis → DB)
- ✅ Cache hit vs miss

**Take 5 minutes:**
1. Stand up and stretch
2. Explain caching using the restaurant analogy
3. Think about caching in apps you use

**Coming up next:** When to cache (decision framework)

---

## When to Cache?

> **📚 TL;DR - The Caching Decision**
>
> **Simple rule: Cache if read:write ratio is 10:1 or higher**
>
> **Perfect for caching:**
> - Article lists (read 1000x, write 1x)
> - User profiles (read 100x, write 1x)
> - Product catalogs (read 10,000x, write 1x)
>
> **Never cache:**
> - Authentication state (security!)
> - Payment transactions (accuracy!)
> - Real-time data (must be fresh!)
>
> **When in doubt:** Cache it! Just set short TTL (60 seconds).

---

### The Decision Framework

> **⚡ Quick Decision Guide**
>
> **Ask these 3 questions:**
> 1. **Is it read more than written?** (10:1+ ratio)
> 2. **Is it okay if slightly stale?** (seconds-minutes old)
> 3. **Does everyone see same data?** (or can key by user)
>
> **All YES → Cache it!**  
> **Any NO → Think carefully or don't cache**

**✅ Cache when:**
```
High read:write ratio
├─> Article lists (read 1000x, write 1x)
├─> User profiles (read 100x, write 1x)
└─> Product catalogs (read 10,000x, write 1x)

Data doesn't change often
├─> Static content (hours/days old okay)
├─> Reference data (countries, categories)
└─> Computed results (trending, popular)

Computation is expensive
├─> Complex database queries (JOINs)
├─> Aggregations (COUNT, SUM, AVG)
└─> AI/ML results (recommendations)

Same data requested repeatedly
├─> Homepage data (everyone sees same)
├─> Popular articles (high traffic)
└─> Public APIs (shared responses)
```

**❌ Don't cache when:**
```
Data changes constantly
├─> Stock prices (second-by-second)
├─> Live scores (real-time)
└─> Chat messages (instant delivery)

Must be absolutely real-time
├─> Bank balances (accuracy critical!)
├─> Inventory counts (avoid overselling)
└─> Authentication tokens (security!)

Privacy/security sensitive
├─> User authentication state
├─> Personal financial data
├─> Medical records
└─> Private messages

Cache overhead > benefit
├─> Already fast queries (<5ms)
├─> Rarely accessed data
└─> Unique per request (no reuse)
```

> **💡 Pro Tip - Time-To-Live (TTL)**
>
> **Use TTL to control staleness:**
> ```typescript
> // Very dynamic (short TTL)
> redis.set('trending', data, { ex: 60 });  // 60 seconds
>
> // Moderately dynamic
> redis.set('articles', data, { ex: 300 }); // 5 minutes
>
> // Mostly static
> redis.set('categories', data, { ex: 3600 }); // 1 hour
>
> // Very static
> redis.set('settings', data, { ex: 86400 }); // 24 hours
> ```
>
> **Shorter TTL = Fresher data but more DB queries**  
> **Longer TTL = Staler data but fewer DB queries**

---

### For WikiApp: What Should We Cache?

**✅ Should cache (high read:write ratio):**

| Data | Read:Write | Why Cache | TTL |
|------|-----------|-----------|-----|
| **Article list** | 1000:1 | Everyone sees same | 5 min |
| **Individual articles** | 100:1 | Popular ones accessed often | 10 min |
| **View counts** | 10000:1 | Aggregated, not critical | 1 min |
| **User profiles** | 50:1 | Change rarely | 10 min |
| **Trending articles** | 100:1 | Computed expensive | 5 min |

**❌ Should NOT cache (low read:write or sensitive):**

| Data | Why NOT Cache |
|------|---------------|
| **Auth state** | Security sensitive, changes often |
| **Form submissions** | Unique per user, must be real-time |
| **Draft articles** | Private, change constantly |
| **Login attempts** | Security tracking |
| **Password resets** | Security critical |

> **⚠️ The Two Hard Problems in Computer Science**
>
> Famous quote:
> 
> *"There are only two hard things in Computer Science:*  
> *cache invalidation and naming things."*  
> *- Phil Karlton*
>
> **Cache invalidation = Knowing when to remove stale data**
>
> We'll cover this soon!

---

## Setting Up Redis

> **📚 TL;DR - Why Redis?**
>
> **Redis = Ultra-fast in-memory database**
>
> **Why it's fast:**
> - Stores data in RAM (not disk)
> - 50-100x faster than Postgres
> - 1-5ms response time
>
> **Why Upstash:**
> - Serverless (no server management)
> - FREE tier (10,000 commands/day)
> - No credit card required
> - Setup: 5 minutes
>
> **Perfect for caching!**

---

### What is Redis?

**Redis = Remote Dictionary Server**

> **💡 Simple Analogy**
>
> **Redis is like a whiteboard in your office:**
> - Write things down (super fast!)
> - Anyone can see instantly
> - Easy to erase and update
> - But... wipe clean when you leave (volatile!)
>
> **Postgres is like a filing cabinet:**
> - Write things down (slower)
> - Must open drawer to see
> - Permanent storage
> - Survives power outage

**The key difference:**

```
Traditional Database (Postgres):
├─> Stores data on DISK (HDD/SSD)
├─> Reads from disk (mechanical delay)
├─> Write-through caching
├─> Persistent (survives restart)
└─> Speed: 50-200ms per query

Redis:
├─> Stores data in RAM (memory!)
├─> Reads from memory (instant!)
├─> Optional persistence
├─> May lose data on crash
└─> Speed: 1-5ms per query

Result: 50-100x faster! ⚡
```

**Use cases:**
- ✅ **Caching** (our focus!) - Store query results
- ✅ **Session storage** - User sessions
- ✅ **Real-time analytics** - View counters
- ✅ **Rate limiting** - API throttling
- ✅ **Pub/sub messaging** - Real-time notifications
- ✅ **Leaderboards** - Sorted sets for rankings

> **⚠️ Important: Redis is NOT a Primary Database**
>
> **Redis:**
> - Fast but volatile (can lose data)
> - Best for temporary data
> - Cache = safe to lose
>
> **Postgres:**
> - Slower but persistent (never loses data)
> - Source of truth
> - Actual data storage
>
> **Use BOTH:** Postgres stores, Redis caches!

---

### Why Upstash?

**Traditional Redis hosting (e.g., AWS ElastiCache):**
```
You manage:
├─> Server provisioning
├─> Security patches
├─> Scaling (add/remove nodes)
├─> Backups
├─> Monitoring
├─> High availability
└─> Cost: $50-500+/month (even when idle!)

Time investment: 5-10 hours/month
```

**Upstash (Serverless Redis):**
```
Upstash manages everything:
├─> Auto-scaling
├─> Security
├─> Backups
├─> Monitoring
├─> High availability
└─> Cost: FREE (up to 10k commands/day)
    Then: $0.20 per 100k commands

Time investment: 0 hours/month
```

> **⚡ Why Serverless?**
>
> **Traditional:** Pay for server 24/7 (even at 3am)  
> **Serverless:** Pay only for usage (sleep = $0!)
>
> **Perfect for:**
> - Side projects (mostly idle)
> - Growing apps (scales automatically)
> - Development (FREE tier generous)

---

### Step-by-Step Setup (5 minutes)

### Step 1: Create Upstash Account

1. Go to [upstash.com](https://upstash.com)
2. Click "Get Started Free"
3. Sign up with GitHub/Google (or email)
4. **No credit card required!** ✅

### Step 2: Create Redis Database

1. Click "Create Database"
2. Enter name: `wikiapp-cache`
3. Select region: **Choose closest to your users**
   - US users → `us-east-1` (Virginia)
   - EU users → `eu-west-1` (Ireland)
   - Asia users → `ap-southeast-1` (Singapore)
4. Type: **Regional** (FREE tier)
5. Click "Create"

> **💡 Pro Tip - Region Selection**
>
> **Pick region closest to your app server:**
> - App in Vercel US → Redis in US
> - App in Vercel EU → Redis in EU
>
> **Why:** Lower latency (faster cache access!)
>
> **Network speed:**
> - Same region: 1-2ms
> - Different region: 50-100ms
> - Different continent: 200-300ms

### Step 3: Get Credentials

After creation, you'll see your dashboard:

```
Database: wikiapp-cache
Status: Active ✅

REST URL: https://us1-living-dog-12345.upstash.io
REST Token: AYABcd...xyz123

Redis URL: redis://default:AYABcd...@us1-living-dog-12345.upstash.io:6379
```

**Copy both REST URL and REST Token** (we'll use REST API, not Redis protocol)

### Step 4: Add to Environment Variables

```bash
# .env.local (create if doesn't exist)
UPSTASH_REDIS_REST_URL="https://us1-living-dog-12345.upstash.io"
UPSTASH_REDIS_REST_TOKEN="AYABcd...xyz123"
```

> **⚠️ Security Note**
>
> **These are secrets!**
> - Never commit to Git
> - Add `.env.local` to `.gitignore`
> - Use Vercel env vars for production
>
> **Check your `.gitignore`:**
> ```
> .env.local
> .env*.local
> ```

### Step 5: Install Redis Client

```bash
npm install @upstash/redis
```

### Step 6: Initialize Redis Client

Create `src/lib/redis.ts`:

```typescript
import { Redis } from '@upstash/redis';

// Initialize Redis client
export const redis = new Redis({
  url: process.env.UPSTASH_REDIS_REST_URL!,
  token: process.env.UPSTASH_REDIS_REST_TOKEN!,
});

// Cache key helpers (namespaced to avoid collisions)
export const CACHE_KEYS = {
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

> **📚 TL;DR - The Two Hard Problems**
>
> **Famous quote:**
> > "There are only two hard things in Computer Science:  
> > cache invalidation and naming things."  
> > — Phil Karlton
>
> **Why is cache invalidation hard?**
> - Old data in cache (stale)
> - New data in database (fresh)
> - Users see wrong information!
>
> **Solution:** Invalidate cache when data changes  
> **Challenge:** Remember to do it everywhere!

---

### The Hard Problem Explained

> **⚠️ Why Cache Invalidation is "Hard"**
>
> **The scenario:**
> ```
> 1. User reads article → Cached (100 views)
> 2. Author updates article
> 3. Database updated ✅ (Article changed!)
> 4. Cache unchanged ❌ (Still has old version!)
> 5. Users see old content for 5 minutes! 🔴
> ```
>
> **The problem:** Two sources of truth!
> - Database has fresh data
> - Cache has stale data
> - Which one is "correct"?

**Why it matters:**

```
Real-world consequences:
├─> User edits profile → Sees old name for 5 minutes
├─> Price changes → Customers see wrong price
├─> Article deleted → Still appears in search
├─> User banned → Can still post comments
└─> Security issue → Old permissions still active

Impact: User confusion, bugs, security issues!
```

> **💡 Key Insight - The Fundamental Trade-off**
>
> **Caching creates a trade-off:**
> - ⚡ **Performance** (fast reads from cache)
> - 🎯 **Consistency** (fresh data from database)
>
> **You can't have both perfectly!**
>
> **Pick your poison:**
> - Short TTL = More consistent, more DB queries
> - Long TTL = Faster, more stale data
> - Manual invalidation = Perfect consistency, more complex

---

### Invalidation Strategies

> **⚡ Quick Strategy Guide**
>
> **Simple rule:**
> - **Time-based (TTL):** For data that changes rarely (products, articles)
> - **Manual invalidation:** For data that must be fresh (user profile, settings)
> - **Cache tags:** For complex relationships (articles + comments + author)
>
> **Most apps use a combination!**

---

#### Strategy 1: Time-Based Expiration (TTL)

> **🕐 TTL = Time To Live**
>
> **Like milk in your fridge:**
> - Buy milk (cache data)
> - Drink from fridge for 7 days (use cache)
> - After 7 days, throw away (TTL expired)
> - Buy fresh milk (query database)

**How it works:**

```typescript
// Set data with 5-minute expiration
await redis.setex(key, 300, data);  // 300 seconds = 5 minutes

Timeline:
├─> 0:00 - Data cached ✅
├─> 2:00 - Read from cache (fast!) ⚡
├─> 4:59 - Still cached (fast!) ⚡
├─> 5:00 - Expired! ❌
└─> 5:01 - Next read queries DB, recaches ♻️
```

**WikiApp example:**

```typescript
import { redis, CACHE_KEYS, CACHE_TTL } from '@/lib/redis';

export async function getArticles() {
  const cacheKey = CACHE_KEYS.articles;
  
  // Check cache
  const cached = await redis.get(cacheKey);
  if (cached) return JSON.parse(cached);
  
  // Query database
  const articles = await db.query.articles.findMany();
  
  // Cache with 5-minute TTL
  await redis.setex(
    cacheKey,
    CACHE_TTL.MEDIUM,  // 300 seconds
    JSON.stringify(articles)
  );
  
  return articles;
}

// Article list might be 5 minutes stale
// That's okay for most use cases!
```

**Pros & Cons:**

```
✅ Pros:
├─> Simple (just set TTL!)
├─> Automatic (no manual work)
├─> Safe (expires eventually)
└─> Good for most use cases

❌ Cons:
├─> Data can be stale (up to TTL duration)
├─> Users see old data temporarily
├─> No control over when it refreshes
└─> Not suitable for critical data
```

**TTL Guidelines:**

| Data Type | Suggested TTL | Reasoning |
|-----------|---------------|-----------|
| Static content | 1 hour+ | Rarely changes |
| Article lists | 5-10 minutes | Changes occasionally |
| User profiles | 5 minutes | Changes sometimes |
| Trending/popular | 1-2 minutes | Updates frequently |
| Real-time data | 10-30 seconds | Must be fresh |

---

#### Strategy 2: Manual Invalidation (Immediate)

> **🔨 Manual Invalidation = Delete on Update**
>
> **Like updating a whiteboard:**
> - Write answer on board (cache)
> - When answer changes, erase board (invalidate)
> - Next student asks, write new answer (recache)

**How it works:**

```typescript
// When data changes, delete cache
export async function updateArticle(id: string, data: any) {
  // 1. Update database
  await db.update(articles)
    .set(data)
    .where(eq(articles.id, id));
  
  // 2. Immediately invalidate caches
  await redis.del(CACHE_KEYS.article(id));      // This article
  await redis.del(CACHE_KEYS.articles);         // Article list
  await redis.del(CACHE_KEYS.user(user.id));    // User profile
  
  // 3. Next request will get fresh data!
}

Timeline:
├─> User edits article
├─> Database updated ✅
├─> Caches deleted ✅
└─> Next read queries DB (fresh data!) ✅
```

**WikiApp implementation:**

```typescript
'use server';

import { redis, CACHE_KEYS } from '@/lib/redis';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { eq, and } from 'drizzle-orm';

export async function updateArticle(
  id: string, 
  data: { title: string; content: string }
) {
  const user = await requireAuth();
  
  // 1. Update database
  const [updated] = await db.update(articles)
    .set({ 
      ...data, 
      updatedAt: new Date() 
    })
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
  
  // 2. Invalidate all related caches
  // Use Promise.all to delete in parallel (faster!)
  await Promise.all([
    redis.del(CACHE_KEYS.article(id)),           // This article cache
    redis.del(CACHE_KEYS.articles),              // Article list cache
    redis.del(CACHE_KEYS.userArticles(user.id)), // User's articles
    redis.del(CACHE_KEYS.trending),              // Trending articles
  ]);
  
  console.log('✅ Caches invalidated for article:', id);
  
  return updated;
}
```

**Pros & Cons:**

```
✅ Pros:
├─> Immediate consistency (no stale data!)
├─> Users always see fresh data
├─> Full control over invalidation
└─> Perfect for critical data

❌ Cons:
├─> Must remember to invalidate (easy to forget!)
├─> Need to invalidate in every update function
├─> More complex code
└─> Can miss edge cases (delete, bulk updates, etc.)
```

> **⚠️ Common Mistakes with Manual Invalidation**
>
> **Mistake #1: Forget to invalidate**
> ```typescript
> // ❌ BAD
> export async function deleteArticle(id: string) {
>   await db.delete(articles).where(eq(articles.id, id));
>   // Forgot to invalidate! Cache still has deleted article!
> }
> ```
>
> **Mistake #2: Invalidate only partial keys**
> ```typescript
> // ❌ BAD
> export async function updateArticle(id: string, data: any) {
>   await db.update(articles).set(data)...;
>   await redis.del(`article:${id}`);  // Deleted this article
>   // But forgot articles list, user profile, trending, etc.!
> }
> ```
>
> **Mistake #3: Forget related caches**
> ```typescript
> // ❌ BAD - User updates profile
> await redis.del(`user:${userId}`);
> // But forgot to invalidate:
> // - User's articles cache
> // - User's comments cache
> // - Search results cache
> ```

---

#### Strategy 3: Cache Tags (Advanced)

> **🏷️ Cache Tags = Group Related Caches**
>
> **Like organizing files with labels:**
> - Tag files with "Work", "Personal", "Urgent"
> - Delete all "Work" files at once
> - No need to list every file manually!

**How it works:**

```typescript
// Store with tags
await redis.set('article:123', articleData, {
  tags: ['articles', 'user:456', 'category:tech'],
});

// Invalidate all caches with a tag
await redis.invalidateTag('articles');
// Clears: article:123, article:124, article:125, etc.
// All in one command!
```

**WikiApp example (using Upstash Redis):**

```typescript
import { Redis } from '@upstash/redis';

const redis = new Redis({
  url: process.env.UPSTASH_REDIS_REST_URL!,
  token: process.env.UPSTASH_REDIS_REST_TOKEN!,
});

// Cache with tags
export async function cacheArticle(article: Article) {
  await redis.set(
    `article:${article.id}`,
    JSON.stringify(article),
    {
      ex: 3600,  // 1 hour TTL
      tags: [
        'articles',                    // All articles tag
        `user:${article.authorId}`,    // User's articles tag
        `category:${article.category}`, // Category tag
      ],
    }
  );
}

// Update article and invalidate by tag
export async function updateArticle(id: string, data: any) {
  await db.update(articles).set(data)...;
  
  // Clear all articles (including lists, search results, etc.)
  await redis.invalidateTag('articles');
  
  // All related caches cleared automatically! ✨
}
```

**Pros & Cons:**

```
✅ Pros:
├─> Flexible (group related caches)
├─> Powerful (invalidate many at once)
├─> No missed caches (tag handles relationships)
└─> Clean code (one line to invalidate)

❌ Cons:
├─> More complex setup
├─> Requires Redis support (not all clients)
├─> Harder to debug (less explicit)
└─> Potential over-invalidation (clears too much)
```

---

### Choosing the Right Strategy

> **💡 Decision Framework**
>
> **Use TTL when:**
> - ✅ Data doesn't change often (products, articles)
> - ✅ Slight staleness is acceptable (seconds-minutes)
> - ✅ Want simplicity (no invalidation logic)
>
> **Use Manual Invalidation when:**
> - ✅ Data must be immediately fresh (user settings, prices)
> - ✅ You have few update paths (easy to track)
> - ✅ Willing to maintain invalidation logic
>
> **Use Cache Tags when:**
> - ✅ Complex relationships (articles + comments + users)
> - ✅ Many related caches to invalidate
> - ✅ Using Redis that supports tags (Upstash, etc.)

**WikiApp strategy (hybrid approach):**

```typescript
// Different strategies for different data:

// 1. Article lists: TTL (5 minutes)
// Changes occasionally, staleness okay
await redis.setex('articles:latest', 300, data);

// 2. User profile: Manual invalidation
// Must be fresh when user updates
await redis.del(`user:${userId}`);

// 3. Complex queries: Cache tags
// Many related caches to clear
await redis.invalidateTag('articles');
```

> **⚡ Key Takeaway - Hybrid Approach**
>
> **Best practice: Combine strategies!**
>
> ```typescript
> // TTL as backup (safety net)
> await redis.setex(key, 3600, data);  // 1 hour max
>
> // Manual invalidation for immediate updates
> await redis.del(key);  // Clear on change
>
> // Result: Fast, fresh, safe! ✅
> ```
>
> **TTL prevents stale data from living forever**  
> **Manual invalidation keeps data fresh**  
> **Perfect balance!**

---

### ☕ Take a Break (5-10 minutes)

**You've mastered the hard problem in computer science!**

**Covered so far:**
- ✅ Caching fundamentals (50x faster!)
- ✅ When to cache (decision frameworks)
- ✅ Cache-aside pattern (check, miss, store)
- ✅ **Cache invalidation strategies** (the hard one!)
  - TTL (time-based, simple)
  - Manual (immediate, complex)
  - Cache tags (flexible, powerful)

**Take 5-10 minutes:**
1. Stand and stretch (you've earned it!)
2. Explain cache invalidation strategies out loud
3. Think about where you'd use each strategy
4. Draw a diagram of TTL + manual invalidation

**Coming up next:** Rate limiting and best practices

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

### ☕ Take a Break (5 minutes)

**You've learned caching implementation!**

**Covered so far:**
- ✅ Caching fundamentals (why it matters)
- ✅ Redis setup (Upstash serverless)
- ✅ View counters (real-time tracking)
- ✅ Query caching (cache-aside pattern)
- ✅ Cache invalidation (keeping data fresh)

**Take 5 minutes:**
1. Stand and stretch
2. Test your cache in Upstash dashboard
3. Think about what to cache in your apps

**Coming up next:** Exercises to practice everything!

---

## 🧠 CHECKPOINT: Understanding Caching

> **💡 Self-Check Questions**
>
> **Before moving on, explain these concepts out loud:**
>
> (Use the Feynman Technique - explain like teaching someone!)

### 1. Why Do We Need Caching?

**Your explanation should include:**
- Database queries are slow (50-200ms)
- Redis is fast (1-5ms) because it's in RAM
- Same data requested repeatedly
- Reduces database load by 90%+
- 50-100x performance improvement

**Test yourself:**
```
Without cache: 10,000 users = 10,000 DB queries
With cache: 10,000 users = 1 DB query + 9,999 Redis reads

Time saved: ?
```

---

### 2. What Is Cache-Aside Pattern?

**Your explanation should include:**
- Check cache first (fast path)
- If miss, query database (slow path)
- Store in cache for next time
- Return data to user

**Draw the flow:**
```
Request → Cache? → Hit ✅ → Return (2ms)
              ↓
            Miss ❌
              ↓
         Database → Store in cache → Return (100ms)
              ↓
         Next request → Hit ✅ → Return (2ms)
```

---

### 3. What Is Cache Invalidation?

**Your explanation should include:**
- Removing stale data from cache
- Happens when data changes (update/delete)
- The "hard problem" in computer science
- Methods: TTL (time-based), manual (event-based), tags

**Why it's hard:**
```
Update article → Which caches become stale?
├─> Article cache ✅
├─> Article list cache ✅  
├─> Author profile ✅
├─> Trending list ✅
└─> Must invalidate ALL related caches!
```

---

### 4. When Should You Cache?

**Your explanation should cover:**

**✅ Cache when:**
- Read-heavy (10:1 ratio or higher)
- Data doesn't change often
- Slow queries (complex JOINs, aggregations)
- Frequently accessed (popular content)

**❌ Don't cache when:**
- Real-time required (stock prices, live scores)
- Constantly changing (every second)
- Security sensitive (auth tokens, payments)
- Unique per request (no reuse benefit)

**Examples:**
- Article list: CACHE ✅ (read 1000x, write 1x)
- Bank balance: DON'T CACHE ❌ (must be real-time!)

---

### 5. What Is TTL and Why Does It Matter?

**Your explanation should include:**
- TTL = Time To Live
- How long cache stays valid (in seconds)
- After TTL expires, cache auto-deletes
- Forces fresh data fetch
- Shorter TTL = fresher data, more DB queries
- Longer TTL = staler data, fewer DB queries

**TTL strategy:**
```typescript
// Very dynamic
redis.setex('trending', 60, data);        // 1 minute

// Moderately dynamic  
redis.setex('articles', 300, data);       // 5 minutes

// Mostly static
redis.setex('categories', 3600, data);    // 1 hour

// Very static
redis.setex('site-config', 86400, data);  // 24 hours
```

---

> **✅ Checkpoint Passed?**
>
> **If you can explain all 5 concepts clearly, you're ready for exercises!**
>
> **If any are fuzzy:**
> 1. Re-read that section
> 2. Draw diagrams
> 3. Explain to someone (or rubber duck!)
> 4. Write code examples
>
> **Taking time here = Saves time later!**

---

---

## 🧪 EXERCISES: Practice Caching

> **📚 Caching Practice**
>
> **Time needed:** 45-60 minutes total  
> **Difficulty:** Intermediate
>
> **Before you start:**
> - ✅ Redis client initialized
> - ✅ Upstash connected
> - ✅ You understand cache-aside pattern
> - ✅ You know TTL concept
>
> **If you get stuck:**
> 1. Review the cache-aside pattern
> 2. Check Redis connection in Upstash dashboard
> 3. Use console.log to debug cache hits/misses
> 4. Look at the solution (it's a learning tool!)

---

### Exercise 1: Cache User Profile

**📝 Task:** Implement caching for user profile queries

**Requirements:**
- Check cache first (try-catch for safety)
- If cache miss, query database
- Store result in cache with 5-minute TTL
- Return user data with their recent articles

**⏱️ Time:** 10-15 minutes

<details>
<summary>💡 Hint</summary>

You'll need:
- `redis.get()` to check cache
- `redis.setex()` to store with TTL (300 seconds = 5 min)
- `JSON.parse()` and `JSON.stringify()` for data conversion
- Try-catch around cache operations (cache failures shouldn't break app)
- Use `CACHE_KEYS.user(userId)` for consistent naming

Pattern:
```typescript
try {
  const cached = await redis.get(key);
  if (cached) return JSON.parse(cached);
} catch {}

const data = await db.query...();
await redis.setex(key, 300, JSON.stringify(data));
return data;
```

</details>

<details>
<summary>✅ Solution</summary>

```typescript
import { redis, CACHE_KEYS, CACHE_TTL } from '@/lib/redis';
import { db } from '@/lib/db';
import { users, articles } from '@/lib/db/schema';
import { eq, desc } from 'drizzle-orm';

export async function getUserProfile(userId: string) {
  const cacheKey = CACHE_KEYS.user(userId);
  
  // 1. Try cache first (cache-aside pattern)
  try {
    const cached = await redis.get(cacheKey);
    if (cached) {
      console.log('✅ Cache hit! Served from Redis');
      return JSON.parse(cached as string);
    }
  } catch (error) {
    console.error('Cache error:', error);
    // Continue to database (cache failure shouldn't break app)
  }
  
  console.log('❌ Cache miss, querying database...');
  
  // 2. Query database (expensive!)
  const user = await db.query.users.findFirst({
    where: eq(users.id, userId),
    with: {
      articles: {
        orderBy: [desc(articles.createdAt)],
        limit: 5,  // Recent articles only
      },
    },
  });
  
  if (!user) {
    throw new Error('User not found');
  }
  
  // 3. Store in cache for next time (5 min TTL)
  try {
    await redis.setex(
      cacheKey,
      CACHE_TTL.MEDIUM,  // 300 seconds
      JSON.stringify(user)
    );
    console.log('✅ Stored in cache');
  } catch (error) {
    console.error('Failed to cache:', error);
    // Not critical, still return user
  }
  
  // 4. Return data
  return user;
}
```

**What this does:**
- **Line by line:**
  1. Create consistent cache key
  2. Try cache first (fast path!)
  3. On miss, query database (slow path)
  4. Store result for future requests
  5. Return data to user

**Performance improvement:**
- First request: 100ms (database)
- Subsequent requests: 2ms (cache)
- 50x faster! ⚡

</details>

---

### Exercise 2: Implement View Counter

**📝 Task:** Create a real-time view counter using Redis

**Requirements:**
- Increment view count in Redis (instant!)
- Batch-update database every 100 views
- Don't block user request (use async)
- Handle concurrent requests safely

**⏱️ Time:** 15-20 minutes  
**Difficulty:** 🔴 Intermediate

<details>
<summary>💡 Hint</summary>

You'll need:
- `redis.incr()` to atomically increment (thread-safe!)
- `redis.get()` to read current count
- Check if count % 100 === 0 to trigger batch update
- Use separate async function for database update
- Don't await database update (let it run in background)

Pattern:
```typescript
// Increment in Redis (fast!)
const views = await redis.incr(`views:${id}`);

// Batch update DB every 100 views (background)
if (views % 100 === 0) {
  updateDatabaseAsync(id, views);  // Don't await!
}
```

</details>

<details>
<summary>✅ Solution</summary>

```typescript
'use server';

import { redis } from '@/lib/redis';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { eq } from 'drizzle-orm';

export async function incrementArticleViews(articleId: string) {
  const viewKey = `views:${articleId}`;
  
  // 1. Increment in Redis (atomic, thread-safe)
  const newViewCount = await redis.incr(viewKey);
  
  console.log(`Article ${articleId} views: ${newViewCount}`);
  
  // 2. Batch update database every 100 views
  if (newViewCount % 100 === 0) {
    // Run in background (don't block user request!)
    updateDatabaseViews(articleId, newViewCount).catch(error => {
      console.error('Failed to update database views:', error);
    });
  }
  
  return newViewCount;
}

// Background function to update database
async function updateDatabaseViews(articleId: string, views: number) {
  await db
    .update(articles)
    .set({ views })
    .where(eq(articles.id, articleId));
  
  console.log(`✅ Updated database: article ${articleId} = ${views} views`);
}

// Get current view count
export async function getArticleViews(articleId: string): Promise<number> {
  const viewKey = `views:${articleId}`;
  
  const views = await redis.get(viewKey);
  return views ? parseInt(views as string, 10) : 0;
}
```

**What this does:**
- **redis.incr()** - Atomically increments (safe for concurrent requests!)
- **Batch update** - Database updated every 100 views (reduces load)
- **Non-blocking** - User gets response immediately
- **Error handling** - DB failure doesn't affect Redis counter

**Why this pattern:**
```
10,000 views without caching:
├─> 10,000 database writes
├─> Slow, expensive
└─> May overwhelm database

10,000 views with Redis batching:
├─> 10,000 Redis writes (instant!)
├─> 100 database writes (batched!)
└─> 100x less database load!
```

**Testing it:**
```typescript
// Simulate 250 views
for (let i = 0; i < 250; i++) {
  await incrementArticleViews('article_123');
}

// Database updated at: 100, 200
// Redis shows: 250
```

</details>

---

### Exercise 3: Cache Invalidation Strategy

**📝 Task:** Implement cache invalidation when article is updated

**Requirements:**
- Invalidate article cache
- Invalidate article list cache
- Invalidate author profile cache
- Use Promise.all for parallel deletion

**⏱️ Time:** 10-15 minutes

<details>
<summary>💡 Hint</summary>

When article updates, these caches become stale:
1. Individual article cache
2. Article list cache (contains this article)
3. Author profile cache (lists their articles)
4. View count cache (might be inconsistent)

Use:
- `redis.del()` to delete keys
- `Promise.all()` to delete in parallel (faster!)
- Consider all related caches

</details>

<details>
<summary>✅ Solution</summary>

```typescript
'use server';

import { requireAuth } from '@/lib/auth';
import { redis, CACHE_KEYS } from '@/lib/redis';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { eq, and } from 'drizzle-orm';

export async function updateArticle(
  id: string,
  data: { title?: string; content?: string }
) {
  // 1. Update database (with authorization)
  const user = await requireAuth();
  
  const [updated] = await db
    .update(articles)
    .set({
      ...data,
      updatedAt: new Date(),
    })
    .where(
      and(
        eq(articles.id, id),
        eq(articles.authorId, user.id)
      )
    )
    .returning();
  
  if (!updated) {
    throw new Error('Not found or unauthorized');
  }
  
  // 2. Invalidate ALL related caches (parallel for speed!)
  await Promise.all([
    // Individual article cache
    redis.del(CACHE_KEYS.article(id)),
    
    // Article list cache (contains this article)
    redis.del(CACHE_KEYS.articles),
    
    // Author profile cache (lists their articles)
    redis.del(CACHE_KEYS.user(user.id)),
    
    // View count cache (might be stale)
    redis.del(`views:${id}`),
    
    // Trending cache (if article was trending)
    redis.del('articles:trending'),
    
    // Popular cache (if article was popular)
    redis.del('articles:popular'),
  ]);
  
  console.log('✅ Invalidated all related caches');
  
  return updated;
}
```

**What this does:**
- **Cascade invalidation** - Remove all affected caches
- **Promise.all** - Delete in parallel (faster than sequential)
- **Comprehensive** - Considers all related caches

**Why invalidate multiple caches:**
```
Article updated:
├─> Article cache stale ❌
├─> Article list includes old version ❌
├─> Author profile shows old article ❌
├─> Trending list might include old data ❌
└─> Must invalidate ALL! ✅
```

**Alternative: Tag-based invalidation**
```typescript
// More advanced: Use cache tags
await redis.del(
  ...(await redis.keys(`*:article:${id}:*`))
);
// Deletes all keys containing this article ID
```

</details>

---

### Exercise 4: Debug Cache Issues

**📝 Task:** Debug stale data problem

**Scenario:** Users report seeing old article titles even after updates. What could be wrong?

**⏱️ Time:** 10-15 minutes  
**Difficulty:** 🔴 Advanced

**Think through:**
1. Is the cache key correct?
2. Is the TTL appropriate?
3. Are we invalidating on updates?
4. Could it be a race condition?
5. Are there multiple cache layers?

Write down your debugging approach before looking at the solution.

<details>
<summary>💡 Hint</summary>

**Common cache bugs:**
1. **Typo in cache key** - get/set use different keys
2. **No invalidation** - Forgot to delete on update
3. **TTL too long** - Old data persists for hours
4. **Race condition** - Read old, update DB, read cached old
5. **Multiple environments** - Dev cache, prod cache mixed

**Debugging tools:**
```typescript
// Check if key exists
const exists = await redis.exists(key);

// Check TTL
const ttl = await redis.ttl(key);

// See all keys
const keys = await redis.keys('article:*');

// Delete all caches
await redis.flushall();
```

</details>

<details>
<summary>✅ Debugging Approach & Solution</summary>

**Step 1: Verify Cache Keys**
```typescript
// Add logging to both get and set
console.log('Getting cache key:', CACHE_KEYS.article(id));
console.log('Setting cache key:', CACHE_KEYS.article(id));

// Make sure they match exactly!
// Common bug: Typo or different function
```

**Step 2: Check TTL**
```typescript
const ttl = await redis.ttl(CACHE_KEYS.article(id));
console.log('TTL remaining:', ttl, 'seconds');

// If TTL is 3600 (1 hour), old data persists!
// Solution: Reduce TTL to 300 (5 minutes)
```

**Step 3: Verify Invalidation Happens**
```typescript
// Add logging to update function
export async function updateArticle(id: string, data: any) {
  console.log('Updating article:', id);
  
  const updated = await db.update...
  
  console.log('Invalidating cache for article:', id);
  await redis.del(CACHE_KEYS.article(id));
  console.log('Cache deleted successfully');
  
  return updated;
}
```

**Step 4: Test the Full Flow**
```typescript
async function testCacheFlow() {
  const testId = 'test-article';
  const key = CACHE_KEYS.article(testId);
  
  // 1. Set initial cache
  await redis.set(key, JSON.stringify({ title: 'Old Title' }));
  console.log('Set cache:', await redis.get(key));
  
  // 2. Update article (should invalidate)
  await updateArticle(testId, { title: 'New Title' });
  
  // 3. Check cache (should be null)
  const afterUpdate = await redis.get(key);
  console.log('After update:', afterUpdate); // Should be null!
  
  if (afterUpdate !== null) {
    console.error('❌ BUG: Cache not invalidated!');
  } else {
    console.log('✅ Cache properly invalidated');
  }
}
```

**Step 5: Check for Race Conditions**
```typescript
// Race condition example:
// Thread 1: Read from cache (old data)
// Thread 2: Update DB + invalidate cache
// Thread 1: Still has old data in memory!

// Solution: Use cache versioning
const cacheKey = `article:${id}:v2`; // Change version to bust all caches
```

**Common Issues & Solutions:**

| Issue | Symptom | Solution |
|-------|---------|----------|
| **Typo in key** | Cache never hits | Use constants, add tests |
| **No invalidation** | Stale data persists | Add del() to update |
| **TTL too long** | Old data for hours | Reduce TTL to minutes |
| **Race condition** | Occasional stale | Use versioning |
| **Multiple layers** | CDN + Redis stale | Invalidate both |

**Pro debugging technique:**
```typescript
// Create a cache monitor
export async function debugCache(id: string) {
  const key = CACHE_KEYS.article(id);
  
  console.log('=== Cache Debug ===');
  console.log('Key:', key);
  console.log('Exists:', await redis.exists(key));
  console.log('TTL:', await redis.ttl(key));
  console.log('Value:', await redis.get(key));
  console.log('==================');
}

// Use it:
await debugCache('article_123');
```

</details>

---

### 🎯 Exercise Summary

**If you completed:**
- ✅ Exercise 1: You can implement cache-aside pattern!
- ✅ Exercise 2: You understand real-time counters!
- ✅ Exercise 3: You know cache invalidation strategies!
- ✅ Exercise 4: You can debug cache issues!

**Caching patterns you've mastered:**

```typescript
// 1. Cache-aside (read-through)
async function getData(id) {
  const cached = await cache.get(id);
  if (cached) return cached;
  
  const data = await db.query(id);
  await cache.set(id, data, TTL);
  return data;
}

// 2. Write-through (update cache on write)
async function updateData(id, updates) {
  await db.update(id, updates);
  await cache.set(id, updates, TTL);  // Update cache
}

// 3. Write-behind (batch writes)
async function incrementCounter(id) {
  const count = await cache.incr(id);
  if (count % 100 === 0) {
    db.update(id, count);  // Background batch
  }
}

// 4. Cache invalidation
async function deleteData(id) {
  await db.delete(id);
  await cache.del(id);  // Invalidate
}
```

**Performance gains you achieved:**
- ⚡ 50-100x faster reads
- 📉 99% less database load
- 💰 Lower infrastructure costs
- 😊 Better user experience

**Next steps:**
- Implement caching in your app
- Monitor cache hit rates
- Adjust TTLs based on usage
- Consider CDN caching for static content

---
  
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
