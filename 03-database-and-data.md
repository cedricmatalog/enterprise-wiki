# Part 3: Database & Data Layer
## Understanding Databases, Schema Design, and Drizzle ORM

> **Goal:** Master database fundamentals and build a type-safe data layer  
> **Time:** 6-8 hours  
> **Prerequisites:** Completed Parts 1 & 2

[← Back to Part 2](./02-ui-and-components.md) | [Back to Index](./README.md) | [Next: Part 4 →](./04-authentication-security.md)

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

---

## The Problem: Storing Data

### Why Do We Need a Database? 🔴

**Scenario:** You're building WikiApp. Where do you store articles?

#### ❌ Bad Idea #1: Variables

```typescript
// Don't do this!
let articles = [
  { id: '1', title: 'My Article', content: '...' },
  { id: '2', title: 'Another Article', content: '...' },
];
```

**Problems:**
- Data disappears when server restarts
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
- Slow for large datasets
- No concurrent access (corruption risk)
- No relationships between data
- No indexing (slow searches)
- Manual backup management

#### ✅ Good Idea: Database

```typescript
// This is the way!
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

### Real-World Example

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

## SQL vs NoSQL: Decision Framework

### Understanding the Difference

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
```

### When to Use SQL

✅ **Use SQL when:**
- Data has clear relationships
- Need complex queries (joins, aggregations)
- Data integrity is critical
- Transactions are important
- Schema is relatively stable
- Need strong consistency

**Examples:**
- E-commerce (orders, products, customers)
- Financial systems
- User management
- Content management (our WikiApp!)

### When to Use NoSQL

✅ **Use NoSQL when:**
- Data is hierarchical/nested
- Schema changes frequently
- Need horizontal scaling
- Flexible data structure
- High write throughput

**Examples:**
- User activity logs
- Real-time analytics
- Product catalogs (varied attributes)
- Social media feeds

### For WikiApp: We Choose SQL (Postgres)

**Why?**

1. **Clear relationships:**
   ```
   Users ──< writes >── Articles
   (One user writes many articles)
   ```

2. **Data integrity:**
   ```
   Can't delete user if they have articles
   Can't create article without valid author
   ```

3. **Complex queries:**
   ```sql
   -- Get all articles by author, with view counts
   SELECT a.*, COUNT(v.id) as views
   FROM articles a
   LEFT JOIN views v ON v.article_id = a.id
   WHERE a.author_id = ?
   GROUP BY a.id
   ```

4. **Industry standard:**
   - Mature tooling
   - Wide adoption
   - SQL skills transferable

---

## Understanding Postgres

### What is Postgres?

**PostgreSQL** (Postgres) is:
- Open-source SQL database
- ACID compliant
- Supports JSON (best of both worlds!)
- Advanced features (arrays, full-text search)
- Battle-tested (30+ years)

### Why Postgres Over MySQL?

| Feature | Postgres | MySQL |
|---------|----------|-------|
| **Standards** | Strict SQL compliance | Looser |
| **Data Types** | Rich (JSON, Arrays) | Basic |
| **Concurrency** | Better (MVCC) | Good |
| **JSON** | Native support | Added later |
| **Extensions** | Powerful | Limited |
| **Open Source** | True OSS | Oracle-owned |

**For modern apps: Postgres is the better choice.**

### Serverless Postgres (Neon)

**Traditional Postgres:**
```
You manage:
├── Server setup
├── Scaling
├── Backups
├── Updates
├── Security patches
└── Monitoring

Cost: $50-200/month + your time
```

**Neon (Serverless Postgres):**
```
Neon manages:
├── Server setup ✅
├── Scaling ✅
├── Backups ✅
├── Updates ✅
├── Security ✅
└── Monitoring ✅

Cost: FREE (up to 500MB)
```

**Benefits:**
- ✅ Scales to zero (pay nothing when idle)
- ✅ Instant branching (test databases)
- ✅ Connection pooling (handles serverless)
- ✅ No credit card required

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

### What is an ORM?

**ORM = Object-Relational Mapping**

It translates between:
```
Your Code (Objects)  ←→  Database (Tables)
```

**Without ORM (Raw SQL):**
```typescript
// Manual SQL
const result = await pool.query(
  'SELECT * FROM articles WHERE author_id = $1',
  [userId]
);
const articles = result.rows;

// Problems:
// - SQL strings (typos!)
// - No TypeScript types
// - Manual parameter binding
// - SQL injection risks
```

**With ORM (Drizzle):**
```typescript
// Type-safe queries
const articles = await db
  .select()
  .from(articlesTable)
  .where(eq(articlesTable.authorId, userId));

// Benefits:
// - TypeScript autocomplete ✅
// - Type checking ✅
// - SQL injection prevention ✅
// - Cleaner syntax ✅
```

### Why Drizzle Over Prisma?

| Feature | Drizzle | Prisma |
|---------|---------|--------|
| **Bundle Size** | ~10KB | ~500KB |
| **Speed** | Faster | Slower |
| **SQL-Like** | Yes | No (own syntax) |
| **Edge Support** | Perfect | Workarounds |
| **Learning Curve** | Medium | Easier |
| **Migrations** | Manual control | Auto-generated |

**For WikiApp:**
- Drizzle is lightweight (better for serverless)
- SQL-like (easier to optimize)
- Edge-compatible (future-proof)

### Drizzle Mental Model

Think of Drizzle as **TypeScript wrapper around SQL**:

```typescript
// SQL:
SELECT id, title FROM articles WHERE author_id = 1

// Drizzle:
db.select({ id, title })
  .from(articles)
  .where(eq(articles.authorId, 1))

// Nearly 1:1 mapping!
```

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

## Designing Your Schema

### Understanding Schema Design

**Schema** = Blueprint of your database structure

Think of it like designing a house:
- Tables = Rooms
- Columns = Features in each room
- Relationships = How rooms connect

### WikiApp Schema Requirements

**What data do we need to store?**

1. **Users**
   - Who can sign up
   - Authentication handled by Stack Auth
   - We just store basic info

2. **Articles**
   - Created by users
   - Have title, content, summary
   - Can have featured images
   - Track view counts
   - Track creation/update times

**Relationships:**
```
User ──< writes >── Article
(One user can write many articles)
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

### What Are Migrations?

**Migration** = A change to your database structure

Think of it like version control for your database:

```
Git:
v1: Initial commit
v2: Add feature X
v3: Update feature Y

Migrations:
v1: Create users table
v2: Create articles table
v3: Add views column to articles
```

### Why Migrations?

**Problem without migrations:**

```
Developer A: Creates tables manually
Developer B: Creates different tables
Production: Has yet another structure
Result: CHAOS! 💥
```

**Solution with migrations:**

```
migration_001_initial.sql
migration_002_add_views.sql
migration_003_add_summary.sql

Everyone runs same migrations
→ Same database structure everywhere ✅
```

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

```
1. Edit schema.ts
   └─> Add new column, table, etc.

2. Generate migration
   └─> npm run db:generate

3. Review SQL
   └─> Check migrations folder

4. Run migration
   └─> npm run db:migrate

5. Tables updated!
   └─> Change applied to database
```

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

## Writing Queries

### SELECT: Reading Data

#### Get All Records

```typescript
// Get all articles
const allArticles = await db.select().from(articles);

// Result: Article[]
```

#### Get Specific Columns

```typescript
// Only get title and id
const articlesMin = await db
  .select({
    id: articles.id,
    title: articles.title,
  })
  .from(articles);

// Result: { id: string, title: string }[]
```

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

## 🧪 EXERCISES: Practice Queries

### Exercise 1: Get Popular Articles

Write a query to get articles with more than 100 views:

```typescript
// Your code here
```

<details>
<summary>Solution</summary>

```typescript
import { gt } from 'drizzle-orm';

const popular = await db.query.articles.findMany({
  where: gt(articles.views, 100),
  orderBy: [desc(articles.views)],
  with: {
    author: true,
  },
});
```
</details>

### Exercise 2: Update Article

Write a function to update an article, but only if the user owns it:

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
<summary>Solution</summary>

```typescript
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
        eq(articles.authorId, userId),
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

### Exercise 3: Complex Query

Write a query to get:
- The 10 most recent articles
- Only include articles with summaries
- Include author name
- Sort by creation date (newest first)

```typescript
// Your code here
```

<details>
<summary>Solution</summary>

```typescript
import { isNotNull } from 'drizzle-orm';

const recent = await db.query.articles.findMany({
  where: isNotNull(articles.summary),
  with: {
    author: {
      columns: {
        displayName: true,
      },
    },
  },
  orderBy: [desc(articles.createdAt)],
  limit: 10,
});
```
</details>

### Exercise 4: Create with Transaction

Write a function that creates a user AND their first article in a transaction:

```typescript
async function createUserWithArticle(
  userData: NewUser,
  articleData: Omit<NewArticle, 'authorId'>
) {
  // Your code here
}
```

<details>
<summary>Solution</summary>

```typescript
async function createUserWithArticle(
  userData: NewUser,
  articleData: Omit<NewArticle, 'authorId'>
) {
  return await db.transaction(async (tx) => {
    const [user] = await tx.insert(users)
      .values(userData)
      .returning();
    
    const [article] = await tx.insert(articles)
      .values({
        ...articleData,
        authorId: user.id,
      })
      .returning();
    
    return { user, article };
  });
}
```
</details>

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
