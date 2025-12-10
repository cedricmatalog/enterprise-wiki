# Part 3: Database & Data Layer
## Understanding Databases, Schema Design, and Drizzle ORM

> **Goal:** Master database fundamentals and build a type-safe data layer  
> **Time:** 8-12 hours (This is the most comprehensive part!)  
> **Prerequisites:** Completed Parts 1 & 2

[← Back to Part 2](./02-ui-and-components.md) | [Back to Index](./README.md) | [Next: Part 4 →](./04-authentication-security.md)

---

> **📚 TL;DR - What You'll Build**
>
> By the end of this part, you'll have:
> - ✅ A fully-functional Postgres database
> - ✅ Type-safe queries with Drizzle ORM
> - ✅ Article and user tables with relations
> - ✅ Complete CRUD operations
> - ✅ Understanding of when/why/how to use databases
>
> **This is the foundation of your app - take your time!**

---

## 📍 Progress: Part 3 of 7

```
[████████████░░░░░░░░░░░░░] 43% Complete
Part 1 ✅ | Part 2 ✅ | Part 3 📍 YOU ARE HERE
```

---

## What We'll Learn

By the end of this part, you'll understand:

✅ **What databases actually are** and why we need them  
✅ **SQL vs NoSQL** - When to use each  
✅ **Schema design** - Modeling data correctly  
✅ **Postgres fundamentals** - How it works  
✅ **Drizzle ORM** - Type-safe database queries  
✅ **Migrations** - Managing schema changes safely  
✅ **Relations** - Connecting data together  
✅ **Query patterns** - Reading and writing data  

> **⏱️ Time Breakdown:**
> - Reading & Understanding: 3-4 hours
> - Setup & Configuration: 1-2 hours  
> - Coding & Exercises: 4-6 hours
> - **Total: 8-12 hours** (worth every minute!)

---

## The Problem: Storing Data

### Why Do We Need a Database? 🔴

> **💡 Key Question:**
> You're building WikiApp with thousands of articles.
> Where do you store them?

Let's explore the options:

#### ❌ Bad Idea #1: Variables

```typescript
// Don't do this!
let articles = [
  { id: '1', title: 'My Article', content: '...' },
  { id: '2', title: 'Another Article', content: '...' },
];
```

**Problems:**
- Data disappears when server restarts 💥
- Can't share data between server instances
- No backup or recovery
- Limited by RAM
- No search capabilities

#### ❌ Bad Idea #2: Files

```typescript
// Don't do this either!
fs.writeFileSync('articles.json', JSON.stringify(articles));
```

**Problems:**
- Slow for large datasets 🐌
- No concurrent access (corruption risk)
- No relationships between data
- No indexing (slow searches)
- Manual backup management

#### ✅ Good Idea: Database

```typescript
// This is the way! ⚡
const articles = await db.select().from(articlesTable);
```

**Benefits:**
- ✅ Data persists forever
- ✅ Handles millions of records
- ✅ Concurrent access (many users at once)
- ✅ Relationships between data
- ✅ Fast searches (indexing)
- ✅ ACID guarantees (data integrity)
- ✅ Automatic backups
- ✅ Query optimization

---

### Real-World Comparison

**Without Database:**
```
User A: Reads article (loads file)
User B: Reads article (loads same file)
User A: Edits article (saves file)
User B: Edits article (saves file)
Result: User A's changes are LOST! 💥
```

**With Database:**
```
User A: Reads article (SELECT query)
User B: Reads article (SELECT query)
User A: Edits article (UPDATE query)
User B: Edits article (UPDATE query)
Result: Database handles conflicts, both saved ✅
```

> **⚡ Key Takeaway**
>
> Databases solve **3 critical problems:**
> 1. **Persistence** - Data survives restarts
> 2. **Concurrency** - Multiple users simultaneously
> 3. **Performance** - Fast queries at scale
>
> This is why every serious app uses a database!

---

## Understanding Databases

### The Mental Model: Spreadsheet

Think of a database like **Excel on steroids**:

```
Excel Spreadsheet:
- Rows = Records
- Columns = Fields
- Sheets = Tables
- Formulas = Queries

Database:
- Same structure
- But handles millions of rows
- Multiple users at once
- Complex relationships
- Lightning fast
```

### Database Terminology

```
Database
└── Tables (like Excel sheets)
    └── Rows/Records (individual items)
        └── Columns/Fields (properties)

Example:

Table: articles
┌────┬───────────────────┬─────────────┬───────────┐
│ id │ title             │ content     │ author_id │
├────┼───────────────────┼─────────────┼───────────┤
│ 1  │ "Getting Started" │ "Once..."   │ 42        │ ← Row/Record
│ 2  │ "Next Steps"      │ "After..."  │ 42        │
│ 3  │ "Advanced"        │ "Now..."    │ 17        │
└────┴───────────────────┴─────────────┴───────────┘
     ↑                                  ↑
   Column                             Column
```

### ACID Properties

Databases guarantee **ACID**:

**Atomicity** - All or nothing
```typescript
// Either BOTH succeed or BOTH fail
await db.transaction(async (tx) => {
  await tx.insert(articles).values({ title: 'New' });
  await tx.insert(users).values({ name: 'Author' });
  // If either fails, both roll back
});
```

**Consistency** - Rules are enforced
```typescript
// Can't insert invalid data
await db.insert(articles).values({
  title: 'Test',
  author_id: 999,  // ❌ Error if user 999 doesn't exist
});
```

**Isolation** - Transactions don't interfere
```typescript
// Two users updating at once don't corrupt data
// Database handles it automatically
```

**Durability** - Saved data stays saved
```typescript
// Even if server crashes after save
// Data is on disk, not lost
```

---

---

### ☕ Quick Break (5 minutes)

**You've learned a lot about databases!**

**Covered so far:**
- ✅ Why databases exist (persistence, concurrency, performance)
- ✅ What ACID means (reliability guarantees)
- ✅ How data is structured (tables, rows, columns)

**Take 5 minutes:**
1. Stand up and stretch
2. Explain ACID to yourself out loud
3. Draw a simple table structure on paper

**Coming up next:** SQL vs NoSQL - which to choose and why

---

## SQL vs NoSQL: Decision Framework

> **📚 TL;DR - SQL vs NoSQL**
>
> **SQL (Relational):**
> - Structured tables with relationships
> - Strong data integrity (ACID)
> - Complex queries (joins, aggregations)
> - **Use for:** Most traditional apps
>
> **NoSQL (Document):**
> - Flexible JSON-like documents
> - Easy horizontal scaling
> - Schema-less (rapid changes)
> - **Use for:** Logs, analytics, catalogs
>
> **For WikiApp:** We use **SQL (Postgres)** because we have clear relationships!

---

### Understanding the Difference

> **💡 Key Insight**
>
> **SQL = Spreadsheet with multiple sheets**  
> **NoSQL = Collection of JSON files**
>
> Both store data. The difference is structure!

**SQL (Relational):**
```
Structured tables with relationships

users table:
┌────┬──────┐
│ id │ name │
├────┼──────┤
│ 1  │ John │
└────┴──────┘

articles table:
┌────┬────────┬───────────┐
│ id │ title  │ author_id │ ← Links to users.id
├────┼────────┼───────────┤
│ 1  │ "Post" │ 1         │
└────┴────────┴───────────┘

Benefits:
✅ Data integrity (can't orphan articles)
✅ No duplication (user name in one place)
✅ Complex queries (join tables together)
```

**NoSQL (Document-based):**
```
Flexible documents (like JSON)

{
  id: 1,
  title: "Post",
  author: {  ← Nested, no separate table
    id: 1,
    name: "John"
  }
}

Benefits:
✅ Fast reads (everything in one document)
✅ Easy to scale horizontally
✅ Schema can change anytime
```

---

### When to Use SQL

✅ **Use SQL when:**
- Data has clear relationships
- Need complex queries (joins, aggregations)
- Data integrity is critical
- Transactions are important (money, orders)
- Schema is relatively stable
- Need strong consistency

**Real-world examples:**
- **E-commerce:** Orders link to products, customers, payments
- **Financial systems:** Transactions must be atomic
- **User management:** Users have posts, comments, likes
- **Content management:** Our WikiApp! (articles, authors, categories)

> **⚡ Key Takeaway**
>
> **If your data has relationships, use SQL!**
>
> Example: "A user writes many articles"  
> This is a relationship → Use SQL!

---

### When to Use NoSQL

✅ **Use NoSQL when:**
- Data is hierarchical/nested (trees, graphs)
- Schema changes frequently (rapid prototyping)
- Need horizontal scaling (millions of writes/sec)
- Flexible data structure (different fields per document)
- High write throughput (logging, analytics)

**Real-world examples:**
- **User activity logs:** Each event has different fields
- **Real-time analytics:** Billions of events, need to scale
- **Product catalogs:** Products have wildly different attributes
- **Social media feeds:** Denormalized for fast reads

> **⚠️ Common Mistake**
>
> **Don't choose NoSQL just because it's "newer"!**
>
> SQL databases have been optimized for 40+ years.  
> For most apps, SQL is the better choice.

---

### For WikiApp: We Choose SQL (Postgres)

**Why SQL is perfect for WikiApp:**

**1. Clear relationships:**
```
Users ──< writes >── Articles ──< has >── Comments
(One user writes many articles)
(One article has many comments)

This is classic relational data!
```

**2. Data integrity:**
```
✅ Can't delete user if they have articles (foreign key)
✅ Can't create article without valid author (constraint)
✅ Can't have orphaned comments (referential integrity)

Database enforces these rules automatically!
```

**3. Complex queries we'll need:**
```sql
-- Get all articles by author, with view counts
SELECT a.*, COUNT(v.id) as views
FROM articles a
LEFT JOIN views v ON v.article_id = a.id
WHERE a.author_id = ?
GROUP BY a.id

-- This is simple in SQL, painful in NoSQL!
```

**4. Industry standard:**
- ✅ Mature tooling (decades of optimization)
- ✅ Wide adoption (easy to hire SQL developers)
- ✅ SQL skills transferable (Postgres, MySQL, SQLite)
- ✅ Best practices well-established

> **💡 Pro Tip**
>
> **Postgres has the best of both worlds!**
>
> - SQL tables for structured data ✅
> - JSON columns for flexible data ✅
> - Full-text search built-in ✅
> - Arrays and advanced types ✅
>
> You can have your cake and eat it too!

---

## Understanding Postgres

> **📚 TL;DR - Why Postgres?**
>
> **PostgreSQL (Postgres) is:**
> - Industry-standard SQL database (30+ years old!)
> - Open-source and truly free (not Oracle-owned)
> - Supports both SQL tables AND JSON documents
> - ACID compliant (data safety guaranteed)
> - Advanced features (arrays, full-text search, extensions)
>
> **With Neon:** Serverless, auto-scaling, FREE tier!

---

### What is Postgres?

**PostgreSQL** (nicknamed "Postgres") is the world's most advanced open-source database.

**Key features:**
- ✅ **Open-source:** Free forever, community-driven
- ✅ **ACID compliant:** Your data is safe
- ✅ **SQL + JSON:** Best of both worlds
- ✅ **Advanced types:** Arrays, JSONB, full-text search
- ✅ **Battle-tested:** 30+ years of development
- ✅ **Extensible:** Add custom functions and data types

> **💡 Pro Tip**
>
> **Postgres is the "safe choice" for most applications!**
>
> Used by: Instagram, Spotify, Reddit, Twitch, Notion  
> If it's good enough for them, it's good enough for us!

---

### Why Postgres Over MySQL?

Both are popular. Here's why Postgres is better for modern apps:

| Feature | Postgres | MySQL | Winner |
|---------|----------|-------|--------|
| **Standards** | Strict SQL compliance | Looser | Postgres ✅ |
| **Data Types** | Rich (JSON, Arrays, UUID) | Basic | Postgres ✅ |
| **Concurrency** | Better (MVCC) | Good | Postgres ✅ |
| **JSON Support** | Native (JSONB) | Added later | Postgres ✅ |
| **Extensions** | Powerful (PostGIS, etc.) | Limited | Postgres ✅ |
| **Open Source** | True OSS | Oracle-owned | Postgres ✅ |
| **Full-Text Search** | Built-in | Requires plugins | Postgres ✅ |
| **Popularity** | Growing fast | Established | Tie |

**For modern apps: Postgres is the better choice.**

> **⚠️ When to Use MySQL**
>
> MySQL is still fine for:
> - Legacy systems already using it
> - Simple read-heavy workloads
> - WordPress sites (optimized for MySQL)
>
> But for new projects? Start with Postgres!

---

### Serverless Postgres (Neon)

**The problem with traditional databases:**

**Traditional Postgres (Self-hosted or AWS RDS):**
```
You manage:
├── Server setup (hours of configuration)
├── Scaling (manual or complex auto-scaling)
├── Backups (cron jobs, S3, monitoring)
├── Updates (security patches, downtime)
├── Security patches (constant vigilance)
└── Monitoring (alerts, dashboards, logs)

Cost: $50-200/month
Time: 5-10 hours/month
Risk: One mistake = data loss
```

**Neon (Serverless Postgres):**
```
Neon manages:
├── Server setup ✅ (instant)
├── Scaling ✅ (automatic)
├── Backups ✅ (continuous)
├── Updates ✅ (zero downtime)
├── Security ✅ (automatic patches)
└── Monitoring ✅ (built-in dashboard)

Cost: FREE (up to 500MB)
Time: 0 hours/month
Risk: Minimal (managed by experts)
```

**Unique Neon benefits:**

1. **Scales to zero:**
   ```
   No traffic? Database pauses.
   Request comes in? Wakes up in <1 second.
   Only pay for active time!
   ```

2. **Instant branching:**
   ```
   Need a test database?
   Create a branch in 1 second (like Git!)
   Test safely, merge or discard.
   ```

3. **Serverless-friendly:**
   ```
   Connection pooling built-in.
   Handles thousands of Lambda connections.
   No "too many connections" errors!
   ```

4. **No credit card required:**
   ```
   500MB storage free
   Plenty for learning and small apps
   Upgrade when you need more
   ```

> **⚡ Key Takeaway**
>
> **Neon = Postgres + Vercel's ease-of-use**
>
> Deploy database as easily as deploying frontend!  
> Push to GitHub → Database updates automatically

---

## Setting Up Neon

### Step 1: Create Account

1. Go to [neon.tech](https://neon.tech)
2. Click "Sign Up" (no credit card!)
3. Sign in with GitHub (easiest)

### Step 2: Create Project

1. Click "New Project"
2. Give it a name: `wikiapp-db`
3. Select region (closest to you)
4. Click "Create Project"

**What just happened?**
- Neon created a Postgres database
- Set up connection pooling
- Configured backups
- Ready in ~10 seconds!

### Step 3: Get Connection String

You'll see something like:

```
postgres://username:password@ep-cool-name-12345.region.aws.neon.tech/neondb
```

**Understanding the URL:**

```
postgres://              ← Protocol
username:password@       ← Credentials
ep-cool-name.region...   ← Host
/neondb                  ← Database name
```

**Copy this URL!** You'll need it.

### Step 4: Add to .env.local

```bash
# .env.local
DATABASE_URL="postgres://username:password@ep-cool-name.region.aws.neon.tech/neondb?sslmode=require"
```

**Important:** 
- Add `?sslmode=require` at the end
- This ensures encrypted connection
- Neon requires SSL

---

## Understanding ORMs

> **📚 TL;DR - What is an ORM?**
>
> **ORM = Object-Relational Mapping**  
> Translates between your TypeScript code and SQL database.
>
> **Without ORM:** Write SQL strings (typos, no types, SQL injection risk)  
> **With ORM:** Write TypeScript (autocomplete, type-safe, secure)
>
> **We use Drizzle:** Lightweight, SQL-like, perfect for Next.js!

---

### What is an ORM?

**ORM = Object-Relational Mapping**

It's a translator between your code and the database:

```
Your TypeScript Code  ←→  SQL Database
    (Objects)              (Tables)

ORM translates between them automatically!
```

> **💡 Think of ORM Like Google Translate**
>
> You speak TypeScript → ORM speaks SQL  
> Database speaks SQL → ORM speaks TypeScript
>
> You never have to write SQL manually!

---

### The Problem: Raw SQL is Painful

**Without ORM (Raw SQL):**
```typescript
// Manual SQL (the old way)
const result = await pool.query(
  'SELECT * FROM articles WHERE author_id = $1',
  [userId]
);
const articles = result.rows;

// Problems:
// ❌ SQL in strings (typos! No syntax highlighting!)
// ❌ No TypeScript types (what fields exist?)
// ❌ Manual parameter binding ($1, $2, $3...)
// ❌ SQL injection risks (if not careful)
// ❌ Hard to refactor (Find/Replace across strings)
// ❌ No autocomplete (what columns exist?)
```

**Real-world pain:**
```typescript
// Typo in SQL (only found at runtime!)
const result = await pool.query(
  'SELECT * FROM articels WHERE auther_id = $1'  // Typo!
  //              ^^^^^^^^       ^^^^^^^^
);
// Error: relation "articels" does not exist

// Wrong parameter binding
const result = await pool.query(
  'SELECT * FROM articles WHERE id = $2',  // Should be $1!
  [articleId]  
);
// Runtime error or wrong results!
```

---

### The Solution: ORM (Drizzle)

**With ORM (Drizzle):**
```typescript
// Type-safe queries (the modern way)
const articles = await db
  .select()
  .from(articlesTable)
  .where(eq(articlesTable.authorId, userId));
//            ^^^^^^^^^^^^^^^^^^^^^^
//            Autocomplete works! ✅

// Benefits:
// ✅ TypeScript autocomplete (IDE helps you!)
// ✅ Type checking (catch errors at compile time!)
// ✅ SQL injection prevention (automatic escaping!)
// ✅ Cleaner syntax (reads like English!)
// ✅ Easy refactoring (TypeScript rename works!)
// ✅ No SQL string typos (editor catches them!)
```

**TypeScript catches errors:**
```typescript
// Typo in table name
const articles = await db
  .select()
  .from(articelsTable)  // Error: Cannot find name 'articelsTable'
//      ^^^^^^^^^^^^^^
// TypeScript error at compile time! ✅

// Wrong field name
const articles = await db
  .select()
  .from(articlesTable)
  .where(eq(articlesTable.autherI, userId));
//                        ^^^^^^^^
// TypeScript error: Property 'autherI' does not exist ✅

// Wrong type
const articles = await db
  .select()
  .from(articlesTable)
  .where(eq(articlesTable.authorId, "not-a-number"));
//                                   ^^^^^^^^^^^^^^^
// TypeScript error: Type 'string' is not assignable to type 'number' ✅
```

> **⚡ Key Takeaway**
>
> **ORM = Catch errors BEFORE deployment!**
>
> - Raw SQL: Errors found by users (bad!)
> - ORM: Errors found by TypeScript (good!)
>
> Your editor becomes your database documentation!

---
// - SQL injection prevention ✅
// - Cleaner syntax ✅
```

### Why Drizzle Over Prisma?

> **⚡ Quick Comparison**
>
> **Drizzle:** Lightweight, SQL-like, edge-ready  
> **Prisma:** Heavier, own syntax, easier to start
>
> **For Next.js serverless:** Drizzle wins!

| Feature | Drizzle | Prisma | Why It Matters |
|---------|---------|--------|----------------|
| **Bundle Size** | ~10KB | ~500KB | Faster cold starts ⚡ |
| **Speed** | Faster | Slower | Better performance |
| **SQL-Like** | Yes | No (own syntax) | Easy to optimize queries |
| **Edge Support** | Perfect | Workarounds | Future-proof |
| **Learning Curve** | Medium | Easier | Requires SQL knowledge |
| **Migrations** | Manual control | Auto-generated | More control |
| **Type Safety** | Excellent | Excellent | Both are great! |

**For WikiApp, Drizzle wins because:**
- ✅ **Lightweight:** 50x smaller bundle (10KB vs 500KB)
- ✅ **SQL-like:** If you know SQL, you know Drizzle
- ✅ **Edge-compatible:** Works on Vercel Edge without hacks
- ✅ **Faster:** No query engine overhead

> **⚠️ When to Use Prisma Instead**
>
> Use Prisma if:
> - You're new to databases (easier to start)
> - You want auto-generated migrations
> - Bundle size doesn't matter (traditional servers)
> - Your team prefers Prisma's syntax
>
> Both are excellent choices!

---

### Drizzle Mental Model

> **💡 Key Insight**
>
> **Drizzle = TypeScript wrapper around SQL**  
> Not a new query language!
>
> If you know SQL, Drizzle feels natural.

Think of Drizzle as **TypeScript wrapper around SQL**:

```typescript
// Raw SQL:
SELECT id, title FROM articles WHERE author_id = 1

// Drizzle (almost identical!):
db.select({ id, title })
  .from(articles)
  .where(eq(articles.authorId, 1))

// Nearly 1:1 mapping!
```

**More examples:**

```typescript
// SQL: SELECT * FROM articles ORDER BY created_at DESC LIMIT 10
db.select()
  .from(articles)
  .orderBy(desc(articles.createdAt))
  .limit(10)

// SQL: UPDATE articles SET title = 'New' WHERE id = 1
db.update(articles)
  .set({ title: 'New' })
  .where(eq(articles.id, 1))

// SQL: INSERT INTO articles (title, content) VALUES ('Hello', '...')
db.insert(articles)
  .values({ title: 'Hello', content: '...' })
```

**The pattern:** Drizzle methods map to SQL keywords!

| SQL | Drizzle | Same! |
|-----|---------|-------|
| SELECT | .select() | ✅ |
| FROM | .from() | ✅ |
| WHERE | .where() | ✅ |
| ORDER BY | .orderBy() | ✅ |
| LIMIT | .limit() | ✅ |
| INSERT | .insert() | ✅ |
| UPDATE | .update() | ✅ |
| DELETE | .delete() | ✅ |

> **⚡ Key Takeaway**
>
> **Learn SQL → Learn Drizzle for free!**
>
> SQL skills transfer directly.  
> Drizzle just adds TypeScript safety on top.

---

## Setting Up Drizzle

### Step 1: Configure Drizzle

Create `drizzle.config.ts`:

```typescript
import { defineConfig } from 'drizzle-kit';

export default defineConfig({
  schema: './src/lib/db/schema.ts',
  out: './src/lib/db/migrations',
  dialect: 'postgresql',
  dbCredentials: {
    url: process.env.DATABASE_URL!,
  },
});
```

**Understanding the config:**

```typescript
schema: './src/lib/db/schema.ts'
// ↑ Where you define your tables

out: './src/lib/db/migrations'
// ↑ Where migration files go

dialect: 'postgresql'
// ↑ Which database (postgres, mysql, sqlite)

dbCredentials: { url: process.env.DATABASE_URL! }
// ↑ How to connect to database
```

### Step 2: Initialize Database Client

Create `src/lib/db/index.ts`:

```typescript
import { drizzle } from 'drizzle-orm/neon-serverless';
import { Pool } from '@neondatabase/serverless';
import * as schema from './schema';

// Create connection pool
const pool = new Pool({ 
  connectionString: process.env.DATABASE_URL! 
});

// Initialize Drizzle with schema
export const db = drizzle(pool, { schema });
```

**What's a connection pool?**

```
Without Pool:
Request 1 → Open connection → Query → Close
Request 2 → Open connection → Query → Close
Request 3 → Open connection → Query → Close
(Slow! Opening connections is expensive)

With Pool:
Request 1 → Grab from pool → Query → Return to pool
Request 2 → Grab from pool → Query → Return to pool
Request 3 → Grab from pool → Query → Return to pool
(Fast! Reuse connections)
```

**Why `{ schema }`?**

```typescript
export const db = drizzle(pool, { schema });
// ↑ Pass your schema definitions

// Now you can do:
db.query.articles.findMany()
//       ↑ TypeScript knows about articles!

// Instead of:
db.select().from(articlesTable)
```

Both work, but `db.query` is nicer for simple queries.

---

### ☕ Take a Break (10 minutes)

**You've covered a LOT of database fundamentals!**

**So far you've learned:**
- ✅ Why databases (not files)
- ✅ SQL vs NoSQL (we chose SQL)
- ✅ Postgres vs MySQL (we chose Postgres)
- ✅ Neon setup (serverless Postgres)
- ✅ What ORMs are (TypeScript → SQL translator)
- ✅ Drizzle vs Prisma (we chose Drizzle)

**Take a full 10-minute break:**
1. Stand up, walk around
2. Grab water or coffee
3. Rest your eyes (look away from screen!)
4. Review your notes if you're taking any

**Coming up next:** Schema design (how to structure your data)  
This is foundational - worth being fresh for!

---

## Designing Your Schema

> **📚 TL;DR - Schema Design**
>
> **Schema = Blueprint of your database**
>
> Like designing a house:
> - Tables = Rooms (users, articles)
> - Columns = Features in each room (id, title, content)
> - Relationships = How rooms connect (user writes articles)
>
> **Golden rule:** Think in relationships!

---

### Understanding Schema Design

**Schema** = Blueprint of your database structure

> **🧠 Think of Schema Like Designing a House**
>
> **Tables = Rooms:**
> - Kitchen table (food prep)
> - Bedroom table (sleeping)
> - Living room table (entertainment)
>
> **Columns = Features in each room:**
> - Kitchen: stove, sink, fridge
> - Bedroom: bed, closet, window
>
> **Relationships = How rooms connect:**
> - Kitchen connects to dining room
> - Bedroom connects to bathroom
>
> Bad design? You'd walk through bedroom to reach kitchen!  
> Bad schema? You'd join 5 tables to get one article!

**Key principles:**

1. **Normalization:** Don't duplicate data
   ```
   ❌ Bad: Store author name in every article
   ✅ Good: Store author ID, link to users table
   ```

2. **Relationships:** Model real-world connections
   ```
   User ──< writes >── Article
   (One user, many articles)
   ```

3. **Data types:** Choose appropriate types
   ```
   ❌ Bad: Store numbers as text
   ✅ Good: Use integer, float, etc.
   ```

---

### WikiApp Schema Requirements

**What data do we need to store?**

**1. Users**
- Who can sign up and create content
- Authentication handled by Stack Auth (they store passwords)
- We just store basic profile info

**2. Articles**
- Created by users
- Have title, content, summary
- Can have featured images (URLs from Cloudinary)
- Track view counts
- Track creation/update times

**Relationships:**
```
User ──< writes >── Article
(One user can write many articles)

Each article belongs to exactly one user (author)
Each user can have zero or many articles
```

> **💡 Pro Tip - Reading Relationship Diagrams**
>
> ```
> User ──< writes >── Article
>      ^^           ^^
>       |            |
>       |            └── One article has one author
>       └── One user has many articles
>
> The "crow's foot" (< or >) shows "many"
> The single line shows "one"
> ```

---
```

### Creating the Schema

Create `src/lib/db/schema.ts`:

```typescript
import { pgTable, text, timestamp, integer, uuid } from 'drizzle-orm/pg-core';
import { relations } from 'drizzle-orm';

// Users table
export const users = pgTable('users', {
  id: text('id').primaryKey(),
  email: text('email').notNull().unique(),
  displayName: text('display_name'),
  profileImageUrl: text('profile_image_url'),
  createdAt: timestamp('created_at').defaultNow().notNull(),
});

// Articles table
export const articles = pgTable('articles', {
  id: uuid('id').primaryKey().defaultRandom(),
  title: text('title').notNull(),
  content: text('content').notNull(),
  summary: text('summary'),
  imageUrl: text('image_url'),
  authorId: text('author_id')
    .notNull()
    .references(() => users.id),
  views: integer('views').default(0).notNull(),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').defaultNow().notNull(),
});

// Define relationships
export const usersRelations = relations(users, ({ many }) => ({
  articles: many(articles),
}));

export const articlesRelations = relations(articles, ({ one }) => ({
  author: one(users, {
    fields: [articles.authorId],
    references: [users.id],
  }),
}));

// TypeScript types (auto-generated!)
export type User = typeof users.$inferSelect;
export type NewUser = typeof users.$inferInsert;
export type Article = typeof articles.$inferSelect;
export type NewArticle = typeof articles.$inferInsert;
```

### Understanding the Schema Code

#### 1. Table Definition

```typescript
export const users = pgTable('users', {
  // columns here
});
```

**Breaking down:**
- `pgTable` = Define a Postgres table
- `'users'` = Table name in database
- `{ ... }` = Column definitions

#### 2. Column Types

```typescript
id: text('id').primaryKey(),
```

**Understanding:**
- `text('id')` = Column named "id", type TEXT
- `.primaryKey()` = This uniquely identifies each row
- Every table needs a primary key

**Common column types:**

```typescript
// Text
text('name')              // Variable length text
text('bio', { length: 500 }) // Max 500 chars

// Numbers
integer('age')            // Whole numbers
numeric('price', { precision: 10, scale: 2 }) // Decimals

// UUID
uuid('id')                // Universally unique identifier

// Timestamps
timestamp('created_at')   // Date + time

// Boolean
boolean('is_active')      // true/false
```

#### 3. Column Modifiers

```typescript
email: text('email').notNull().unique(),
```

**Understanding modifiers:**

```typescript
.notNull()        // Cannot be NULL (required)
.unique()         // Must be unique across all rows
.default(0)       // Default value if not provided
.defaultNow()     // Default to current timestamp
.primaryKey()     // Unique identifier for row
.references()     // Foreign key (links to another table)
```

#### 4. Foreign Keys

```typescript
authorId: text('author_id')
  .notNull()
  .references(() => users.id),
```

**What's a foreign key?**

```
articles table:
┌────┬──────────┬───────────┐
│ id │ title    │ author_id │ ← Must match a users.id
├────┼──────────┼───────────┤
│ 1  │ "Post 1" │ user_123  │ ✅ Valid
│ 2  │ "Post 2" │ user_999  │ ❌ Error if user_999 doesn't exist
└────┴──────────┴───────────┘
```

**Benefits:**
- ✅ Data integrity (can't have orphaned articles)
- ✅ Prevents deleting users with articles
- ✅ Database enforces the relationship

#### 5. Relations

```typescript
export const usersRelations = relations(users, ({ many }) => ({
  articles: many(articles),
}));
```

**What are relations?**

Relations tell Drizzle how tables connect:

```typescript
// User → Articles (one-to-many)
usersRelations = relations(users, ({ many }) => ({
  articles: many(articles),  // User has many articles
}));

// Article → User (many-to-one)
articlesRelations = relations(articles, ({ one }) => ({
  author: one(users, {       // Article has one author
    fields: [articles.authorId],
    references: [users.id],
  }),
}));
```

**This enables easy queries:**

```typescript
// Get user with all their articles
const user = await db.query.users.findFirst({
  where: eq(users.id, userId),
  with: {
    articles: true,  // ← Automatically joins!
  },
});

// user = {
//   id: '123',
//   name: 'John',
//   articles: [
//     { title: 'Post 1' },
//     { title: 'Post 2' },
//   ]
// }
```

#### 6. Type Inference

```typescript
export type User = typeof users.$inferSelect;
export type NewUser = typeof users.$inferInsert;
```

**Magic! Drizzle generates types:**

```typescript
// $inferSelect = Type for reading from DB
type User = {
  id: string;
  email: string;
  displayName: string | null;
  profileImageUrl: string | null;
  createdAt: Date;
}

// $inferInsert = Type for inserting into DB
type NewUser = {
  id: string;
  email: string;
  displayName?: string | null;  // Optional on insert
  profileImageUrl?: string | null;
  // createdAt auto-generated, so not needed
}
```

**Use them in your code:**

```typescript
function getUser(id: string): Promise<User> {
  return db.query.users.findFirst({
    where: eq(users.id, id),
  });
}

function createUser(data: NewUser): Promise<User> {
  return db.insert(users).values(data).returning();
}
```

---

## 🧠 CHECKPOINT: Understanding Schema

Before moving on, explain:

1. **What is a primary key and why does every table need one?**
2. **What is a foreign key and what problem does it solve?**
3. **What's the difference between `User` and `NewUser` types?**
4. **Why do we define relations?**
5. **What does `.notNull()` do?**

Take a moment. Explain each out loud using the Feynman Technique.

---

## Migrations

> **📚 TL;DR - Database Migrations**
>
> **Migrations = Git for your database**
>
> - Each migration is like a Git commit
> - Track every change to database structure
> - Apply changes consistently across all environments
> - Can roll back if something goes wrong
>
> **Why critical:** Keeps dev, staging, and production in sync!

---

### What Are Migrations?

**Migration** = A change to your database structure

> **💡 Perfect Analogy: Migrations Are Like Git**
>
> **Git (for code):**
> ```
> commit 1: Initial files
> commit 2: Add login feature  
> commit 3: Update user model
> ```
>
> **Migrations (for database):**
> ```
> migration 1: Create users table
> migration 2: Create articles table
> migration 3: Add views column to articles
> ```
>
> **Both track changes over time!**

**The migration lifecycle:**

```
Schema Change → Generate Migration → Review SQL → Apply to Database

schema.ts          migration_001.sql         Database
   |                      |                       |
   |---> db:generate ---->|                       |
                          |----> db:migrate ----->|
```

---

### Why Migrations Matter

**Problem WITHOUT migrations:**

```
❌ The Chaos Scenario:

Developer A: Manually creates tables
Developer B: Manually creates different tables  
Staging: Someone added columns manually
Production: Has yet another structure

Result: COMPLETE CHAOS! 💥

- App works on A's machine ✅
- App breaks on B's machine ❌
- App crashes in staging ❌  
- Production is corrupted ❌
```

**Solution WITH migrations:**

```
✅ The Organized Way:

migration_001_initial.sql
migration_002_add_views.sql
migration_003_add_summary.sql

Everyone runs the same migrations in order
→ Same database structure everywhere ✅

- App works on A's machine ✅
- App works on B's machine ✅
- App works in staging ✅
- Production is stable ✅
```

> **⚡ Key Takeaway**
>
> **Migrations = Single source of truth for database structure**
>
> - Never manually change production database!
> - Always use migrations
> - Version control your migrations (Git them!)
> - Apply in order on all environments
>
> This is how professionals manage databases!

---

### Generate Migration

```bash
npm run db:generate
```

**What happens:**

1. Drizzle reads your `schema.ts`
2. Compares to existing migrations
3. Generates SQL to update database
4. Saves in `src/lib/db/migrations/`

**You'll see:**

```
📦 Generating migration...
✅ Migration generated!
📁 0000_initial.sql
```

**Open the file:**

```sql
-- src/lib/db/migrations/0000_initial.sql

CREATE TABLE IF NOT EXISTS "users" (
  "id" text PRIMARY KEY NOT NULL,
  "email" text NOT NULL UNIQUE,
  "display_name" text,
  "profile_image_url" text,
  "created_at" timestamp DEFAULT now() NOT NULL
);

CREATE TABLE IF NOT EXISTS "articles" (
  "id" uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  "title" text NOT NULL,
  "content" text NOT NULL,
  "summary" text,
  "image_url" text,
  "author_id" text NOT NULL,
  "views" integer DEFAULT 0 NOT NULL,
  "created_at" timestamp DEFAULT now() NOT NULL,
  "updated_at" timestamp DEFAULT now() NOT NULL,
  CONSTRAINT "articles_author_id_users_id_fk" 
    FOREIGN KEY ("author_id") REFERENCES "users"("id")
);
```

**This is the actual SQL that will run!**

### Run Migration

```bash
npm run db:migrate
```

**What happens:**

1. Connects to your Neon database
2. Runs the SQL from migration file
3. Creates tables with all constraints
4. Updates migration history

**You'll see:**

```
📦 Running migrations...
✅ Migration 0000_initial.sql applied
🎉 Database up to date!
```

**Check Neon dashboard:**
- You'll see `users` and `articles` tables!
- With all columns and constraints

### Migration Workflow

**The standard workflow:**

```
1. Edit schema.ts
   └─> Add new column, table, relationship, etc.

2. Generate migration
   └─> npm run db:generate
   └─> Creates SQL file in migrations folder

3. Review SQL (IMPORTANT!)
   └─> Check migrations/XXXX_name.sql
   └─> Make sure SQL looks correct
   └─> Never skip this step!

4. Run migration
   └─> npm run db:migrate  
   └─> Applies SQL to database

5. Tables updated!
   └─> Change applied successfully
   └─> Commit migration to Git
```

> **⚠️ Common Migration Mistakes**
>
> **DON'T:**
> - ❌ Edit old migrations (breaks history!)
> - ❌ Delete migrations (breaks consistency!)
> - ❌ Skip reviewing SQL (might delete data!)
> - ❌ Manually edit database (bypasses migrations!)
> - ❌ Forget to commit migrations to Git
>
> **DO:**
> - ✅ Always generate new migration for changes
> - ✅ Review SQL before applying
> - ✅ Keep migrations in version control
> - ✅ Test migrations on dev first
> - ✅ Create rollback plan for production

> **💡 Pro Tips**
>
> **1. Descriptive names:**
> ```bash
> # Good names (clear what changed)
> 0001_initial_schema.sql
> 0002_add_views_to_articles.sql
> 0003_add_user_bio.sql
>
> # Bad names (unclear)
> 0001_update.sql
> 0002_changes.sql
> 0003_fix.sql
> ```
>
> **2. One change per migration:**
> ```bash
> # Good (focused)
> 0001_create_users.sql
> 0002_create_articles.sql
>
> # Bad (too many changes)
> 0001_everything.sql
> ```
>
> **3. Test rollback:**
> ```bash
> # Before applying to production:
> npm run db:migrate     # Apply
> npm run db:rollback    # Test undo
> npm run db:migrate     # Reapply
> ```

---

This is Part 3 content so far. Should I continue with:
- Seeding data
- Writing queries (SELECT, INSERT, UPDATE, DELETE)
- Working with relations
- Query optimization tips
- Best practices
- Exercises

Or move on to starting Part 4? Let me know! 🚀

## Seeding the Database

### What is Seeding?

**Seeding** = Adding initial/test data to your database

**Why seed?**
- ✅ Test your app with realistic data
- ✅ Don't manually create test records
- ✅ Consistent across environments
- ✅ Faster development

### Create Seed Script

Create `src/lib/db/seed.ts`:

```typescript
import { db } from './index';
import { users, articles } from './schema';

async function seed() {
  console.log('🌱 Seeding database...');
  
  try {
    // Create test users
    console.log('Creating users...');
    const [user1, user2] = await db.insert(users).values([
      {
        id: 'user_1',
        email: 'john@example.com',
        displayName: 'John Doe',
      },
      {
        id: 'user_2',
        email: 'jane@example.com',
        displayName: 'Jane Smith',
      },
    ]).returning();
    
    console.log('✅ Created users');
    
    // Create test articles
    console.log('Creating articles...');
    await db.insert(articles).values([
      {
        title: 'Getting Started with Next.js',
        content: `# Getting Started

Next.js is a powerful React framework...

## Key Features
- Server-side rendering
- Static site generation
- API routes
- File-based routing`,
        summary: 'Learn the basics of Next.js',
        authorId: user1.id,
      },
      {
        title: 'Understanding TypeScript',
        content: `# TypeScript Guide

TypeScript adds static typing to JavaScript...`,
        summary: 'Master TypeScript fundamentals',
        authorId: user2.id,
      },
      {
        title: 'Building with Drizzle ORM',
        content: `# Drizzle ORM

Modern TypeScript ORM for SQL databases...`,
        summary: 'Database queries made easy',
        authorId: user1.id,
      },
    ]);
    
    console.log('✅ Created articles');
    console.log('🎉 Seeding complete!');
    
  } catch (error) {
    console.error('❌ Seeding failed:', error);
    throw error;
  }
}

seed()
  .catch((error) => {
    console.error(error);
    process.exit(1);
  })
  .finally(() => {
    process.exit(0);
  });
```

### Run the Seed

```bash
npm run db:seed
```

**Output:**
```
🌱 Seeding database...
Creating users...
✅ Created users
Creating articles...
✅ Created articles
🎉 Seeding complete!
```

**Check your database!**
- Go to Neon dashboard
- Click "Tables" → "articles"
- You'll see your test data!

---

---

### ☕ Take a Break (5 minutes)

**You've learned schema design and migrations!**

**Covered so far:**
- ✅ Schema structure (tables, columns, relationships)
- ✅ Migrations (Git for database)
- ✅ Seeding data (test data)

**Before queries, take 5:**
1. Stand and stretch
2. Review your schema design
3. Check your migration files

**Coming up:** Writing queries (the fun part!)

---

## Writing Queries

> **📚 TL;DR - Database Queries (CRUD)**
>
> **The 4 fundamental operations:**
> 1. **CREATE** (INSERT) - Add new data
> 2. **READ** (SELECT) - Get data
> 3. **UPDATE** - Modify existing data
> 4. **DELETE** - Remove data
>
> **Drizzle provides 2 query styles:**
> - **Builder style:** `db.select().from(table)`
> - **Query API:** `db.query.table.findMany()` (easier!)
>
> **We'll use Query API (cleaner for most cases)**

---

### Query Decision Guide

> **💡 Which Query Style to Use?**
>
> **Use Query API when:**
> - ✅ Simple queries (findFirst, findMany)
> - ✅ Need relations (with: { author: true })
> - ✅ Standard operations (90% of queries)
>
> **Use Builder style when:**
> - ✅ Complex joins (multiple tables)
> - ✅ Aggregations (COUNT, SUM, AVG)
> - ✅ Custom SQL needed
>
> **When in doubt:** Start with Query API!

---

### SELECT: Reading Data

> **⚡ Quick Reference - SELECT Operations**
>
> | Operation | Method | Example |
> |-----------|--------|---------|
> | Get all | `findMany()` | Get all articles |
> | Get one | `findFirst()` | Get article by ID |
> | Filter | `where:` | Articles by author |
> | Sort | `orderBy:` | Newest first |
> | Limit | `limit:` | First 10 results |
> | Relations | `with:` | Include author |

---

#### Get All Records

```typescript
// Get all articles (Query API - cleaner!)
const allArticles = await db.query.articles.findMany();

// Result: Article[]
```

**When to use:**
- Displaying all items
- Export functionality
- Admin dashboards

> **⚠️ Warning**
>
> **Don't get all records without limit in production!**
> ```typescript
> // ❌ Bad: Could return 10,000 records
> const all = await db.query.articles.findMany();
>
> // ✅ Good: Use pagination
> const articles = await db.query.articles.findMany({
>   limit: 20,
>   offset: 0,
> });
> ```

---

#### Get Specific Columns

```typescript
// Builder style (only get what you need)
const articlesMin = await db
  .select({
    id: articles.id,
    title: articles.title,
  })
  .from(articles);

// Result: { id: string, title: string }[]
```

**Why select specific columns?**
- ⚡ Faster (less data transferred)
- 💾 Less memory (especially for large content fields)
- 🔒 Security (don't expose sensitive fields)

**Example use case:**
```typescript
// Dropdown list - only need id and title
const options = await db
  .select({
    value: articles.id,
    label: articles.title,
  })
  .from(articles);
```

---

#### Get One Record

```typescript
import { eq } from 'drizzle-orm';

// Get article by ID
const article = await db
  .select()
  .from(articles)
  .where(eq(articles.id, articleId))
  .limit(1);

// Result: Article[] (array with 0 or 1 item)
```

**Using Query API (cleaner):**

```typescript
// Get one article
const article = await db.query.articles.findFirst({
  where: eq(articles.id, articleId),
});

// Result: Article | undefined
```

#### Filtering with WHERE

```typescript
import { eq, and, or, gt, lt, like } from 'drizzle-orm';

// Articles by specific author
const userArticles = await db.query.articles.findMany({
  where: eq(articles.authorId, userId),
});

// Articles with > 100 views
const popular = await db.query.articles.findMany({
  where: gt(articles.views, 100),
});

// Search by title (case-insensitive)
const search = await db.query.articles.findMany({
  where: like(articles.title, '%next%'),
});

// Multiple conditions (AND)
const recent = await db.query.articles.findMany({
  where: and(
    eq(articles.authorId, userId),
    gt(articles.createdAt, lastWeek),
  ),
});

// Multiple conditions (OR)
const either = await db.query.articles.findMany({
  where: or(
    eq(articles.authorId, userId),
    gt(articles.views, 1000),
  ),
});
```

#### Sorting (ORDER BY)

```typescript
import { desc, asc } from 'drizzle-orm';

// Newest first
const newest = await db.query.articles.findMany({
  orderBy: [desc(articles.createdAt)],
});

// Most viewed first
const popular = await db.query.articles.findMany({
  orderBy: [desc(articles.views)],
});

// Multiple sort
const sorted = await db.query.articles.findMany({
  orderBy: [
    desc(articles.views),      // Primary: by views
    desc(articles.createdAt),  // Secondary: by date
  ],
});
```

#### Pagination (LIMIT & OFFSET)

```typescript
const page = 1;
const pageSize = 10;

const articles = await db.query.articles.findMany({
  limit: pageSize,
  offset: (page - 1) * pageSize,
  orderBy: [desc(articles.createdAt)],
});

// Page 1: offset 0 (items 1-10)
// Page 2: offset 10 (items 11-20)
// Page 3: offset 20 (items 21-30)
```

#### Joining Related Data

```typescript
// Get articles WITH author info
const articlesWithAuthors = await db.query.articles.findMany({
  with: {
    author: true,  // Include related user
  },
});

// Result:
// [
//   {
//     id: '1',
//     title: 'My Article',
//     author: {
//       id: 'user_1',
//       name: 'John Doe',
//     }
//   }
// ]
```

**Nested relations:**

```typescript
// Get user WITH all their articles
const user = await db.query.users.findFirst({
  where: eq(users.id, userId),
  with: {
    articles: {
      orderBy: [desc(articles.createdAt)],
      limit: 5,  // Only get 5 most recent
    },
  },
});

// Result:
// {
//   id: 'user_1',
//   name: 'John',
//   articles: [
//     { title: 'Post 1' },
//     { title: 'Post 2' },
//   ]
// }
```

### INSERT: Creating Data

#### Insert One Record

```typescript
// Create article
const [article] = await db.insert(articles)
  .values({
    title: 'My New Article',
    content: 'Article content here...',
    authorId: user.id,
  })
  .returning();  // Returns the created record

// article = { id: '...', title: 'My New Article', ... }
```

**Why `returning()`?**

```typescript
// Without returning
await db.insert(articles).values({ ... });
// Returns nothing ❌

// With returning
const [article] = await db.insert(articles)
  .values({ ... })
  .returning();
// Returns the created article ✅
```

#### Insert Multiple Records

```typescript
// Create multiple articles at once
const newArticles = await db.insert(articles)
  .values([
    { title: 'Article 1', content: '...', authorId: userId },
    { title: 'Article 2', content: '...', authorId: userId },
    { title: 'Article 3', content: '...', authorId: userId },
  ])
  .returning();

// newArticles = [Article, Article, Article]
```

#### Insert with TypeScript Types

```typescript
// Use the NewArticle type from schema
const newArticleData: NewArticle = {
  title: 'Type-Safe Article',
  content: 'This is validated by TypeScript',
  authorId: user.id,
  // TypeScript ensures all required fields are present!
};

const [article] = await db.insert(articles)
  .values(newArticleData)
  .returning();
```

### UPDATE: Modifying Data

#### Update Records

```typescript
import { eq } from 'drizzle-orm';

// Update article
await db.update(articles)
  .set({
    title: 'Updated Title',
    updatedAt: new Date(),
  })
  .where(eq(articles.id, articleId));
```

#### Update and Return

```typescript
// Update and get the updated record
const [updated] = await db.update(articles)
  .set({ views: sql`${articles.views} + 1` })  // Increment
  .where(eq(articles.id, articleId))
  .returning();

console.log(updated.views);  // Old views + 1
```

#### Conditional Update

```typescript
// Only update if conditions met
await db.update(articles)
  .set({ summary: aiGeneratedSummary })
  .where(
    and(
      eq(articles.id, articleId),
      eq(articles.authorId, userId),  // Only if user owns it
    )
  );
```

### DELETE: Removing Data

#### Delete Records

```typescript
// Delete article
await db.delete(articles)
  .where(eq(articles.id, articleId));
```

#### Delete with Multiple Conditions

```typescript
// Only delete if user owns it
await db.delete(articles)
  .where(
    and(
      eq(articles.id, articleId),
      eq(articles.authorId, userId),
    )
  );
```

#### Delete and Return

```typescript
// Delete and see what was deleted
const [deleted] = await db.delete(articles)
  .where(eq(articles.id, articleId))
  .returning();

console.log(`Deleted: ${deleted.title}`);
```

---

## Real-World Query Examples

### Get Articles for Homepage

```typescript
export async function getRecentArticles(limit = 10) {
  return await db.query.articles.findMany({
    with: {
      author: {
        columns: {
          id: true,
          displayName: true,
          profileImageUrl: true,
        },
      },
    },
    orderBy: [desc(articles.createdAt)],
    limit,
  });
}
```

### Get Single Article with Author

```typescript
export async function getArticleById(id: string) {
  const article = await db.query.articles.findFirst({
    where: eq(articles.id, id),
    with: {
      author: true,
    },
  });
  
  if (!article) {
    throw new Error('Article not found');
  }
  
  return article;
}
```

### Get User Profile with Articles

```typescript
export async function getUserProfile(userId: string) {
  const user = await db.query.users.findFirst({
    where: eq(users.id, userId),
    with: {
      articles: {
        orderBy: [desc(articles.createdAt)],
        limit: 10,
      },
    },
  });
  
  if (!user) {
    throw new Error('User not found');
  }
  
  return user;
}
```

### Search Articles

```typescript
import { ilike, or } from 'drizzle-orm';

export async function searchArticles(query: string) {
  const searchTerm = `%${query}%`;
  
  return await db.query.articles.findMany({
    where: or(
      ilike(articles.title, searchTerm),
      ilike(articles.content, searchTerm),
    ),
    with: {
      author: {
        columns: {
          displayName: true,
        },
      },
    },
    orderBy: [desc(articles.views)],
    limit: 20,
  });
}
```

### Increment View Count

```typescript
import { sql } from 'drizzle-orm';

export async function incrementViews(articleId: string) {
  const [article] = await db.update(articles)
    .set({
      views: sql`${articles.views} + 1`,
    })
    .where(eq(articles.id, articleId))
    .returning();
  
  return article.views;
}
```

---

## Using Queries in Server Components

Remember: Server Components can directly query the database!

### Example: Articles Page

```typescript
// src/app/articles/page.tsx
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { desc } from 'drizzle-orm';
import { ArticleCard } from '@/components/article-card';

export default async function ArticlesPage() {
  // Query database directly in Server Component!
  const allArticles = await db.query.articles.findMany({
    with: {
      author: true,
    },
    orderBy: [desc(articles.createdAt)],
  });
  
  return (
    <div>
      <h1>All Articles</h1>
      <div className="grid grid-cols-3 gap-6">
        {allArticles.map((article) => (
          <ArticleCard
            key={article.id}
            article={article}
          />
        ))}
      </div>
    </div>
  );
}
```

### Example: Article Detail Page

```typescript
// src/app/articles/[id]/page.tsx
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';
import { eq } from 'drizzle-orm';
import { notFound } from 'next/navigation';

export default async function ArticlePage({
  params,
}: {
  params: { id: string };
}) {
  const article = await db.query.articles.findFirst({
    where: eq(articles.id, params.id),
    with: {
      author: true,
    },
  });
  
  if (!article) {
    notFound();  // Shows 404 page
  }
  
  return (
    <article>
      <h1>{article.title}</h1>
      <p>By {article.author?.displayName}</p>
      <div>{article.content}</div>
    </article>
  );
}
```

---

## Best Practices

### 1. Always Use Transactions for Related Operations

```typescript
// ❌ Bad: Two separate queries
await db.insert(users).values(userData);
await db.insert(articles).values(articleData);
// If second fails, user exists but article doesn't!

// ✅ Good: Single transaction
await db.transaction(async (tx) => {
  await tx.insert(users).values(userData);
  await tx.insert(articles).values(articleData);
  // Both succeed or both fail
});
```

### 2. Use Indexes for Frequently Queried Columns

```typescript
// Add to schema.ts
export const articles = pgTable('articles', {
  // ... columns
}, (table) => {
  return {
    authorIdx: index('author_idx').on(table.authorId),
    titleIdx: index('title_idx').on(table.title),
  };
});
```

### 3. Select Only Needed Columns

```typescript
// ❌ Bad: Get everything
const articles = await db.select().from(articlesTable);

// ✅ Good: Only get what you need
const articles = await db
  .select({
    id: articlesTable.id,
    title: articlesTable.title,
  })
  .from(articlesTable);
```

### 4. Use Prepared Statements for Repeated Queries

```typescript
// Prepare once
const getArticleById = db.query.articles
  .findFirst({
    where: eq(articles.id, sql.placeholder('id')),
  })
  .prepare();

// Execute many times (faster!)
const article1 = await getArticleById.execute({ id: 'abc' });
const article2 = await getArticleById.execute({ id: 'def' });
```

### 5. Handle Errors Properly

```typescript
try {
  const article = await db.insert(articles)
    .values(data)
    .returning();
    
  return article;
} catch (error) {
  if (error.code === '23505') {  // Unique violation
    throw new Error('Article with this title already exists');
  }
  if (error.code === '23503') {  // Foreign key violation
    throw new Error('Invalid author');
  }
  throw error;  // Unknown error
}
```

---

## 🧠 CHECKPOINT: Understanding Queries

Before moving on, make sure you can:

1. **Write a SELECT query** to get all articles by a user
2. **Write an INSERT query** to create a new article
3. **Write an UPDATE query** to increment view count
4. **Write a DELETE query** with authorization check
5. **Explain what `.returning()` does**
6. **Explain the difference between `db.select()` and `db.query`**
7. **Explain why transactions are important**

**Take a moment. Try writing each query from memory.**

---

---

## 🧪 EXERCISES: Practice Queries

> **📚 Practice Makes Perfect**
>
> **Time needed:** 30-45 minutes total  
> **Difficulty:** Beginner to Intermediate
>
> **Before you start:**
> - ✅ Make sure `npm run dev` is running
> - ✅ Database is seeded with test data
> - ✅ Have Drizzle documentation open
> - ✅ Review query examples above if needed
>
> **If you get stuck:**
> 1. Read the error message carefully
> 2. Check your imports (eq, gt, and, or, etc.)
> 3. Review the query examples above
> 4. Check solution (no shame in learning!)

---

### Exercise 1: Get Popular Articles

**📝 Task:** Write a query to get articles with more than 100 views

**Requirements:**
- Filter by views (> 100)
- Sort by most viewed first
- Include author information

**⏱️ Time:** 5-10 minutes

```typescript
// Your code here
```

<details>
<summary>💡 Hint</summary>

You'll need:
- `gt()` for greater than comparison
- `orderBy` with `desc()` for sorting
- `with` to include relations

</details>

<details>
<summary>✅ Solution</summary>

```typescript
import { gt, desc } from 'drizzle-orm';

const popular = await db.query.articles.findMany({
  where: gt(articles.views, 100),
  orderBy: [desc(articles.views)],
  with: {
    author: true,
  },
});
```

**What this does:**
- `gt(articles.views, 100)` - Articles with views > 100
- `desc(articles.views)` - Highest views first
- `with: { author: true }` - Include author info

</details>

---

### Exercise 2: Update Article (Authorization Check)

**📝 Task:** Write a function to update an article, but only if the user owns it

**Requirements:**
- Check article exists
- Check user is the author (authorization!)
- Update title and content
- Update `updatedAt` timestamp
- Return the updated article

**⏱️ Time:** 10-15 minutes

```typescript
async function updateArticle(
  articleId: string, 
  userId: string, 
  data: { title: string; content: string }
) {
  // Your code here
}
```

<details>
<summary>💡 Hint</summary>

You'll need:
- `db.update()` for updating
- `and()` to combine conditions (id AND authorId)
- `.returning()` to get the updated record
- Check if result exists (authorization failed)

</details>

<details>
<summary>✅ Solution</summary>

```typescript
import { eq, and } from 'drizzle-orm';

async function updateArticle(
  articleId: string,
  userId: string,
  data: { title: string; content: string }
) {
  const [updated] = await db.update(articles)
    .set({
      ...data,
      updatedAt: new Date(),
    })
    .where(
      and(
        eq(articles.id, articleId),
        eq(articles.authorId, userId),  // Authorization check!
      )
    )
    .returning();
  
  if (!updated) {
    throw new Error('Article not found or unauthorized');
  }
  
  return updated;
}
```

**What this does:**
- `and()` - Both conditions must be true
- `eq(articles.id, articleId)` - Find the article
- `eq(articles.authorId, userId)` - User must be author
- `if (!updated)` - Handle not found or unauthorized

**Why this matters:** This is how you do authorization in database queries!

</details>

---

### Exercise 3: Complex Query (Multiple Filters)

**📝 Task:** Write a query with multiple requirements:

**Requirements:**
- Get 10 most recent articles
- Only articles that have summaries (not null)
- Include author name (not all author fields)
- Sort by creation date (newest first)

**⏱️ Time:** 10-15 minutes

```typescript
// Your code here
```

<details>
<summary>💡 Hint</summary>

You'll need:
- `isNotNull()` to check for non-null summaries
- `with` for relations
- `columns` to select specific author fields
- `orderBy` with `desc()` for sorting
- `limit` for the count

</details>

<details>
<summary>✅ Solution</summary>

```typescript
import { isNotNull, desc } from 'drizzle-orm';

const recent = await db.query.articles.findMany({
  where: isNotNull(articles.summary),
  with: {
    author: {
      columns: {
        displayName: true,  // Only get displayName, not all fields
      },
    },
  },
  orderBy: [desc(articles.createdAt)],
  limit: 10,
});
```

**What this does:**
- `isNotNull(articles.summary)` - Articles with summaries only
- `columns: { displayName: true }` - Select specific fields from relation
- `desc(articles.createdAt)` - Newest first
- `limit: 10` - Top 10 only

**Result type:**
```typescript
{
  id: string;
  title: string;
  summary: string;  // Not null!
  author: {
    displayName: string;  // Only this field
  }
}[]
```

</details>

---

### Exercise 4: Transaction (Advanced)

**📝 Task:** Write a function that creates a user AND their first article in a transaction

**Requirements:**
- Create user first
- Create article with user's ID
- If either fails, roll back both
- Return both user and article

**⏱️ Time:** 10-15 minutes  
**Difficulty:** 🔴 Advanced

```typescript
async function createUserWithArticle(
  userData: NewUser,
  articleData: Omit<NewArticle, 'authorId'>
) {
  // Your code here
}
```

<details>
<summary>💡 Hint</summary>

You'll need:
- `db.transaction()` wrapper
- `tx.insert()` instead of `db.insert()` inside transaction
- `.returning()` to get created records
- Use user.id for articleData.authorId

</details>

<details>
<summary>✅ Solution</summary>

```typescript
async function createUserWithArticle(
  userData: NewUser,
  articleData: Omit<NewArticle, 'authorId'>
) {
  return await db.transaction(async (tx) => {
    // Step 1: Create user
    const [user] = await tx.insert(users)
      .values(userData)
      .returning();
    
    // Step 2: Create article with user's ID
    const [article] = await tx.insert(articles)
      .values({
        ...articleData,
        authorId: user.id,  // Use newly created user's ID
      })
      .returning();
    
    // Step 3: Return both
    return { user, article };
  });
}
```

**What this does:**
- `db.transaction()` - Wraps both operations
- If user creation fails → Nothing saved
- If article creation fails → User creation rolled back too
- All or nothing! ✅

**Real-world use case:** User registration with welcome article

</details>

---

### 🎓 Bonus Challenge (Optional)

**📝 Task:** Create a function to get a user's profile with statistics

**Requirements:**
- Get user by ID
- Include all their articles
- Calculate total views across all articles
- Include article count

**⏱️ Time:** 15-20 minutes  
**Difficulty:** 🔴🔴 Advanced

<details>
<summary>💡 Hint</summary>

You might need:
- Query API for user with articles
- JavaScript `.reduce()` to sum views
- Or raw SQL with aggregation

</details>

<details>
<summary>✅ Solution</summary>

**Option 1: Query API + JavaScript**
```typescript
async function getUserProfile(userId: string) {
  const user = await db.query.users.findFirst({
    where: eq(users.id, userId),
    with: {
      articles: true,
    },
  });
  
  if (!user) throw new Error('User not found');
  
  // Calculate stats in JavaScript
  const totalViews = user.articles.reduce(
    (sum, article) => sum + article.views, 
    0
  );
  
  return {
    ...user,
    stats: {
      articleCount: user.articles.length,
      totalViews,
    },
  };
}
```

**Option 2: SQL Aggregation (more efficient)**
```typescript
import { sql, eq } from 'drizzle-orm';

async function getUserProfile(userId: string) {
  const [profile] = await db
    .select({
      id: users.id,
      displayName: users.displayName,
      email: users.email,
      articleCount: sql<number>`count(${articles.id})`,
      totalViews: sql<number>`coalesce(sum(${articles.views}), 0)`,
    })
    .from(users)
    .leftJoin(articles, eq(articles.authorId, users.id))
    .where(eq(users.id, userId))
    .groupBy(users.id);
  
  return profile;
}
```

**Which to use?**
- Option 1: Simpler, good for small data
- Option 2: Faster, good for large data

</details>

---

### 🎯 Exercise Summary

**If you completed:**
- ✅ Exercise 1-2: You understand basic queries!
- ✅ Exercise 3: You can handle complex filtering!
- ✅ Exercise 4: You know transactions!
- ✅ Bonus: You're ready for production! 🚀

**Common mistakes to avoid:**
- ❌ Forgetting to import operators (eq, gt, and, etc.)
- ❌ Using wrong table name (articles vs articlesTable)
- ❌ Forgetting `.returning()` when you need the result
- ❌ Not handling null/undefined cases

**Next steps:**
- Try modifying these exercises
- Experiment with different operators
- Add error handling
- Write tests for your queries

---

## Summary

You now understand:

✅ **What databases are** and why we need them  
✅ **SQL vs NoSQL** - When to use each  
✅ **Postgres fundamentals** - ACID, tables, relationships  
✅ **Drizzle schema** - Defining tables and relations  
✅ **Migrations** - Versioning database structure  
✅ **CRUD operations** - SELECT, INSERT, UPDATE, DELETE  
✅ **Query patterns** - Filtering, sorting, joining  
✅ **Best practices** - Transactions, indexes, error handling  

### Key Takeaways

1. **Databases persist data** safely and efficiently
2. **Schema is your contract** - Define it well upfront
3. **Migrations are essential** - Never modify database manually
4. **Relations connect data** - Use foreign keys
5. **TypeScript types** are auto-generated from schema
6. **Transactions ensure** all-or-nothing operations
7. **Select only what you need** - Performance matters

### What's Next?

In Part 4, we'll add authentication:
- Understanding auth concepts
- Stack Auth integration
- Protected routes
- Authorization checks
- Security best practices

[→ Continue to Part 4: Authentication & Security](./04-authentication-security.md)

---

**Great job completing Part 3!** Take a break, then continue when ready. 🚀
