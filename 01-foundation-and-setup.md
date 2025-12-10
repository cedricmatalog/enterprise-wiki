# Next.js Enterprise Wiki - Deep Learning Tutorial
## Understanding-First Approach | 100% Free | No Credit Card Required

> **Philosophy:** Learn WHY before HOW. Understand principles, not just code.
> **Your Goal:** Build independently, not just follow tutorials.
> **Time Investment:** 3-4 weeks of focused learning = Years of confidence

---

## How to Use This Tutorial

### ❌ **DON'T Do This:**
1. Copy-paste all code quickly
2. Run `npm run dev` 
3. "It works!" ✓
4. Move to next tutorial

### ✅ **DO This Instead:**
1. **Read the PROBLEM section** - Understand what we're solving
2. **Read the WHY section** - Understand the reasoning
3. **Study the code** - Don't copy yet, read and understand
4. **Explain it out loud** - Feynman Technique (seriously, do this!)
5. **TYPE the code manually** - No copy-paste
6. **Do the exercises** - Active learning checkpoints
7. **Break and fix** - Intentionally break things to understand
8. **Only then** - Move to next section

### **Learning Pace:**
- **Rushing through:** 1 week → 20% understanding ❌
- **Deep learning:** 3-4 weeks → 80% understanding ✅

**Choose wisely.** This tutorial is designed for the second path.

---

## Table of Contents

### Part 1: Foundation & Concepts
1. [Understanding the Problem](#part-1-understanding-the-problem)
2. [Modern Web Architecture](#modern-web-architecture)
3. [Why This Tech Stack?](#why-this-tech-stack)

### Part 2: Project Setup
4. [Setting Up Next.js](#setting-up-nextjs)
5. [Project Structure & Organization](#project-structure)

### Part 3: UI & Styling
6. [Understanding Modern CSS](#understanding-modern-css)
7. [Component-Based Design](#component-based-design)

### Part 4: Database & Data
8. [Database Fundamentals](#database-fundamentals)
9. [ORM vs Raw SQL](#orm-vs-raw-sql)
10. [Schema Design](#schema-design)

### Part 5: Authentication
11. [Auth Concepts](#authentication-concepts)
12. [Implementation](#auth-implementation)

### Part 6: Performance
13. [Caching Strategies](#caching-strategies)
14. [Redis Deep Dive](#redis-deep-dive)

### Part 7: Advanced Features
15. [File Uploads](#file-uploads)
16. [Email Systems](#email-systems)
17. [AI Integration](#ai-integration)

### Part 8: Production
18. [DevOps Concepts](#devops-concepts)
19. [Deployment](#deployment)

---

# Part 1: Understanding the Problem

## What Are We Building?

### The Vision
A **Wiki/Knowledge Base platform** where:
- Anyone can read articles
- Registered users can write articles
- Authors own their content
- Articles have images, summaries, and engagement tracking
- Everything is fast and scales well

### Real-World Example
Think of it as:
- **Medium** (for article creation)
- **Wikipedia** (for knowledge sharing)
- **Dev.to** (for community content)

But simpler, and built by YOU to understand every piece.

## The Problems We're Solving

### Problem 1: Content Management 🔴
**Scenario:** 
- You have 1,000 articles
- Users want to read, search, and browse them
- Authors need to create, edit, and delete their articles

**Challenges:**
- Where do you store articles? (Database)
- How do you organize them? (Schema design)
- How do you query them efficiently? (ORM)
- How do you show them fast? (Caching)

### Problem 2: User Identity 🔴
**Scenario:**
- Multiple people using your platform
- Need to know who created what
- Protect content from unauthorized edits

**Challenges:**
- How do you verify who someone is? (Authentication)
- How do you control what they can do? (Authorization)
- How do you persist their session? (Tokens/Cookies)

### Problem 3: Performance 🔴
**Scenario:**
- 10,000 users visiting at once
- Everyone reading the same popular articles
- Database getting hammered with same queries

**Challenges:**
- Reduce repeated database queries (Caching)
- Serve content fast globally (CDN)
- Handle spikes in traffic (Serverless)

### Problem 4: User Experience 🔴
**Scenario:**
- Users upload images
- Need to notify authors of milestones
- Want AI to help with summaries

**Challenges:**
- Where to store files? (Object storage)
- How to send emails reliably? (Email service)
- How to integrate AI affordably? (Free AI models)

## Traditional vs Modern Solutions

### Old Way (2010s):
```
Problem: Need a web app
Solution:
1. Rent a server ($50/month)
2. Install Linux, configure everything
3. Set up MySQL database
4. Write PHP/Rails code
5. Deploy manually with FTP
6. Scale by buying bigger servers
7. Stay up at night when it crashes

Result: Expensive, slow, hard to maintain
```

### Modern Way (2025):
```
Problem: Need a web app
Solution:
1. Use serverless platforms (FREE tier)
2. Connect managed services
3. Deploy with git push
4. Auto-scales to demand
5. Pay only for actual usage
6. Sleep well at night

Result: Free (or cheap), fast, reliable
```

**This tutorial teaches the MODERN way.**

---

# Modern Web Architecture

## Understanding the Pieces

Before we code, let's understand what a modern web app actually IS.

### The Mental Model: Restaurant Analogy 🍽️

```
Traditional Restaurant (Old Web):
- You own the building (Server)
- You hire chefs (Backend code)
- You maintain kitchen (Database)
- You serve customers (Users)
- Open 24/7 even if empty
- Pay rent even with no customers

Cloud Kitchen (Modern Web):
- Rent kitchen space only when needed (Serverless)
- Use shared equipment (Managed services)
- Only pay for meals cooked (Usage-based)
- Automatically expand during rush hour (Auto-scaling)
- Close when no customers (Pay nothing)
```

### Modern Web App Components

```
┌─────────────────────────────────────────────┐
│           USER'S BROWSER                     │
│  (React/Next.js Frontend)                    │
│  - Shows UI                                  │
│  - Handles user input                        │
│  - Makes requests                            │
└──────────────┬──────────────────────────────┘
               │
               ↓
┌─────────────────────────────────────────────┐
│         VERCEL EDGE NETWORK                  │
│  (Global CDN)                                │
│  - Routes requests                           │
│  - Caches content                            │
│  - Runs at edge locations                    │
└──────────────┬──────────────────────────────┘
               │
               ↓
┌─────────────────────────────────────────────┐
│      NEXT.JS SERVER FUNCTIONS                │
│  (Your Application Code)                     │
│  - Server actions                            │
│  - API routes                                │
│  - Business logic                            │
└───┬────────┬────────┬────────┬──────────────┘
    │        │        │        │
    ↓        ↓        ↓        ↓
┌────────┐ ┌────────┐ ┌──────┐ ┌──────────┐
│ Neon   │ │Upstash │ │Cloud-│ │  Stack   │
│Postgres│ │ Redis  │ │inary │ │   Auth   │
│Database│ │ Cache  │ │Images│ │ Login    │
└────────┘ └────────┘ └──────┘ └──────────┘
```

### Key Concepts to Understand

#### 1. **Client vs Server**

**CLIENT (Browser):**
- Runs on user's computer
- Can be slow (old phone)
- Can't be trusted (user can modify code)
- Can't access secrets (API keys visible)

**SERVER (Your code):**
- Runs on Vercel's computers
- Always fast and reliable
- Completely trusted
- Keeps secrets safe

**Rule:** 
- Put UI logic in CLIENT
- Put sensitive logic in SERVER

#### 2. **Serverless Functions**

**Traditional Server:**
```javascript
// Server running 24/7
const server = express();
server.listen(3000); // Always on, always costing money
```

**Serverless Function:**
```javascript
// Only runs when called, then disappears
export async function handleRequest() {
  // Runs for 100ms
  // Then stops existing
  // Pay only for those 100ms
}
```

**Benefits:**
- ✅ Pay only for actual usage
- ✅ Auto-scales to millions of users
- ✅ No server maintenance
- ✅ FREE tier is very generous

#### 3. **Edge Computing**

**Traditional:**
```
User in Philippines → Request to US Server → 200ms delay
User in US → Request to US Server → 20ms delay
```

**Edge Computing:**
```
User in Philippines → Request to Manila Edge → 10ms delay
User in US → Request to LA Edge → 10ms delay
```

**How it works:**
- Code deployed to 20+ locations worldwide
- Request goes to nearest location
- Everyone gets fast response

---

# Why This Tech Stack?

## The Free Tier Stack

We chose each service carefully for:
1. **FREE tier without credit card**
2. **Production-ready** (not just toys)
3. **Generous limits** (can handle real traffic)
4. **Easy to use** (good developer experience)
5. **Can upgrade later** (when you need more)

### Decision Framework: How We Chose

For each need, we evaluated options:

#### Database: Why Neon?

**The Problem:**
We need to store articles, users, and relationships between them.

**Options Compared:**

| Option | Free Tier | Setup | Scaling | Verdict |
|--------|-----------|-------|---------|---------|
| Local SQLite | Unlimited | Easy | Hard | ❌ Dev only |
| MySQL (self-hosted) | $5/mo | Hard | Manual | ❌ Too much work |
| MongoDB Atlas | 512MB free | Easy | Auto | ✅ Good but... |
| Supabase | 500MB free | Easy | Auto | ✅ Good but... |
| **Neon** | **500MB free** | **Easy** | **Auto** | **✅ Best!** |

**Why Neon won:**
- ✅ Serverless Postgres (scales to zero)
- ✅ No credit card required
- ✅ Branching (test databases for free)
- ✅ Auto-pause when not used
- ✅ Postgres = industry standard SQL

**Trade-off:** 
- 500MB limit on free tier (enough for 10,000+ articles)
- If you need more, Supabase or PlanetScale are alternatives

#### Authentication: Why Stack Auth?

**The Problem:**
Users need to sign up, log in, and stay logged in.

**Options Compared:**

| Option | Free Tier | Setup Time | Features | Verdict |
|--------|-----------|------------|----------|---------|
| DIY (custom) | Free | 2 weeks | Basic | ❌ Too risky |
| NextAuth.js | Free | 2 days | Good | ✅ But complex |
| Clerk | 10k MAU | 1 hour | Great | ❌ Needs card |
| Supabase Auth | 50k MAU | 1 hour | Good | ✅ Good option |
| **Stack Auth** | **1k MAU** | **30 min** | **Great** | **✅ Best!** |

**Why Stack Auth won:**
- ✅ No credit card required
- ✅ Drop-in solution (just works)
- ✅ Handles everything (emails, OAuth, etc.)
- ✅ 1,000 users/month is plenty for learning

**Trade-off:**
- Only 1k MAU on free tier
- If you need more, Supabase Auth has 50k free

#### Caching: Why Upstash Redis?

**The Problem:**
Database queries are slow (50-200ms). Need to cache frequently accessed data.

**Options Compared:**

| Option | Free Tier | Setup | Compatible | Verdict |
|--------|-----------|-------|------------|---------|
| In-memory (Map) | Free | 1 min | No | ❌ Resets on deploy |
| Vercel KV | 256MB | Easy | Yes | ❌ Needs card |
| Redis Labs | 30MB | Medium | Yes | ✅ OK but small |
| **Upstash** | **10k cmds/day** | **Easy** | **Yes** | **✅ Perfect!** |

**Why Upstash won:**
- ✅ No credit card needed
- ✅ 10,000 commands/day (plenty for learning)
- ✅ REST API (works with serverless)
- ✅ Redis-compatible (standard)

**Trade-off:**
- Command limit not storage limit
- Perfect for view counters and caching
- May need upgrade for heavy caching

#### Images: Why Cloudinary?

**The Problem:**
Users upload images. Need to store, optimize, and serve them fast.

**Options Compared:**

| Option | Free Tier | Features | Easy Upload | Verdict |
|--------|-----------|----------|-------------|---------|
| Local filesystem | Free | None | Easy | ❌ Disappears |
| S3 | $0.023/GB | Basic | Medium | ❌ Needs card |
| Vercel Blob | 1GB | Good | Easy | ❌ Needs card |
| ImageKit | 20GB | Great | Easy | ✅ Good option |
| **Cloudinary** | **25GB** | **Amazing** | **Easy** | **✅ Best!** |

**Why Cloudinary won:**
- ✅ 25GB free storage (huge!)
- ✅ Automatic optimization
- ✅ Image transformations
- ✅ CDN delivery
- ✅ No credit card required

**Trade-off:**
- Need to learn their upload API
- But it's well documented

#### AI: Why OpenRouter?

**The Problem:**
Need AI for summaries but Anthropic/OpenAI need payment.

**Options Compared:**

| Option | Free Tier | Quality | Speed | Verdict |
|--------|-----------|---------|-------|---------|
| OpenAI GPT | $5 credit | Best | Fast | ❌ Needs card |
| Anthropic | Pay-per-use | Best | Fast | ❌ Needs card |
| Hugging Face | Free | OK | Slow | ✅ Truly free |
| **OpenRouter** | **Free models** | **Good** | **Fast** | **✅ Best!** |

**Why OpenRouter won:**
- ✅ No credit card required
- ✅ Access to free open-source models
- ✅ Same API as OpenAI (easy)
- ✅ Good enough for summaries

**Trade-off:**
- Free models not as good as GPT-4
- But Gemma 2 9B works fine for summaries

### The Complete Stack

```
┌─────────────────────────────────────────────┐
│  Frontend: Next.js 15 + React               │
│  - Free, open source                        │
│  - Best React framework                     │
│  - Excellent DX                             │
├─────────────────────────────────────────────┤
│  Styling: Tailwind CSS + shadcn/ui          │
│  - Free, open source                        │
│  - Fast development                         │
│  - Professional UI                          │
├─────────────────────────────────────────────┤
│  Database: Neon Postgres                    │
│  - 500MB free                               │
│  - Serverless scaling                       │
│  - No credit card                           │
├─────────────────────────────────────────────┤
│  ORM: Drizzle                               │
│  - Free, open source                        │
│  - Type-safe                                │
│  - Lightweight                              │
├─────────────────────────────────────────────┤
│  Auth: Stack Auth                           │
│  - 1,000 MAU free                           │
│  - Drop-in solution                         │
│  - No credit card                           │
├─────────────────────────────────────────────┤
│  Cache: Upstash Redis                       │
│  - 10k commands/day free                    │
│  - Serverless Redis                         │
│  - No credit card                           │
├─────────────────────────────────────────────┤
│  Images: Cloudinary                         │
│  - 25GB free                                │
│  - Auto optimization                        │
│  - No credit card                           │
├─────────────────────────────────────────────┤
│  Email: Resend                              │
│  - 100 emails/day free                      │
│  - React Email templates                    │
│  - No credit card                           │
├─────────────────────────────────────────────┤
│  AI: OpenRouter                             │
│  - Free models available                    │
│  - OpenAI-compatible API                    │
│  - No credit card                           │
├─────────────────────────────────────────────┤
│  Hosting: Vercel                            │
│  - Unlimited deploys free                   │
│  - Edge network                             │
│  - No credit card                           │
└─────────────────────────────────────────────┘

Total Monthly Cost: $0.00 💰
```

## 🧠 **CHECKPOINT: Explain It Back**

Before moving to code, explain these concepts out loud (Feynman Technique):

1. **What's the difference between client and server?**
2. **Why are we using serverless instead of traditional servers?**
3. **What problem does Redis solve?**
4. **Why did we choose Neon over other databases?**
5. **What's the benefit of using managed services?**

If you can't explain them clearly, re-read those sections. Understanding these concepts is crucial for the rest of the tutorial.

---

# Setting Up Next.js

## Understanding Next.js First

### What is Next.js?

**Simple answer:** 
Next.js is a framework built ON TOP of React that handles all the hard stuff (routing, server-side rendering, optimization) so you can focus on building features.

**React alone:**
```javascript
// You have to set up:
- Routing (react-router)
- Build config (webpack)
- Code splitting
- SSR manually
- API routes
- Deployment config
= 2-3 days of setup
```

**With Next.js:**
```javascript
// All of that is built-in
- File-based routing ✅
- Optimized builds ✅
- Auto code splitting ✅
- SSR by default ✅
- API routes ✅
- Vercel deploy ✅
= 30 minutes of setup
```

### Why Next.js Over Plain React?

#### 1. **Server-Side Rendering (SSR)**

**Problem with Plain React:**
```html
<!-- What search engines see -->
<html>
  <body>
    <div id="root"></div>
    <script src="bundle.js"></script>
  </body>
</html>

<!-- Empty! Bad for SEO -->
```

**With Next.js SSR:**
```html
<!-- What search engines see -->
<html>
  <body>
    <h1>Getting Started with Next.js</h1>
    <p>Full article content here...</p>
    <!-- Actual content! Great for SEO -->
  </body>
</html>
```

**Benefits:**
- ✅ Better SEO (Google sees content)
- ✅ Faster first paint (users see content immediately)
- ✅ Works without JavaScript (progressive enhancement)

#### 2. **File-Based Routing**

**React Router (manual):**
```javascript
// Have to manually define routes
<Router>
  <Route path="/" element={<Home />} />
  <Route path="/articles" element={<Articles />} />
  <Route path="/articles/:id" element={<Article />} />
  <Route path="/articles/new" element={<NewArticle />} />
</Router>
```

**Next.js (automatic):**
```
src/app/
├── page.tsx              → /
├── articles/
│   ├── page.tsx          → /articles
│   ├── [id]/
│   │   └── page.tsx      → /articles/123
│   └── new/
│       └── page.tsx      → /articles/new
```

**Benefits:**
- ✅ No routing config needed
- ✅ File system = URL structure (intuitive)
- ✅ Dynamic routes with [brackets]

#### 3. **API Routes**

**Traditional Setup:**
```
Frontend: React (Port 3000)
Backend: Express (Port 8000)
- Two separate servers
- CORS issues
- More complex deployment
```

**Next.js:**
```
Frontend + Backend: Next.js (Port 3000)
- Single codebase
- No CORS issues
- Deploy together
```

### Next.js App Router vs Pages Router

Next.js has TWO routing systems. We're using **App Router** (new, better).

**Why App Router?**

| Feature | Pages Router | App Router |
|---------|--------------|------------|
| Released | 2016 | 2023 |
| Server Components | ❌ No | ✅ Yes |
| Streaming | ❌ No | ✅ Yes |
| Layouts | Manual | Built-in |
| Data Fetching | getServerSideProps | async/await |
| Learning Curve | Easier | Steeper |
| Future | Maintenance | Active development |

**App Router is the future.** Yes, it's a bit harder to learn, but you'll thank yourself later.

## Now Let's Build

### Step 1: Create Next.js Project

Open your terminal and run:

```bash
npx create-next-app@latest wiki-app
```

**What does this command do?**
- `npx` = Run a package without installing globally
- `create-next-app` = Official Next.js project scaffolder
- `@latest` = Use newest version
- `wiki-app` = Project name

**You'll see prompts. Here's what to choose and WHY:**

```
✔ Would you like to use TypeScript? 
→ YES

Why: Type safety catches bugs before runtime
Example bug prevented:
  const user = getUser();
  console.log(user.name); // Error if user is null
```

```
✔ Would you like to use ESLint?
→ YES

Why: Catches common mistakes and enforces code quality
Example:
  let x = 5; // ESLint warns: use const if not reassigning
```

```
✔ Would you like to use Tailwind CSS?
→ YES

Why: Utility-first CSS, faster than writing custom CSS
Example:
  <div className="flex items-center gap-4 p-4 bg-blue-500">
  # vs writing 20 lines of CSS
```

```
✔ Would you like to use `src/` directory?
→ YES

Why: Keeps code separate from config files
Better organization:
  src/ → Your code
  root/ → Config files (next.config.js, etc.)
```

```
✔ Would you like to use App Router?
→ YES

Why: New, powerful features (explained above)
This is the modern way, learn it now
```

```
✔ Would you like to customize the default import alias?
→ NO (use default @/*)

Why: @/ is clean and standard
Example:
  import { Button } from '@/components/ui/button'
  # vs
  import { Button } from '../../components/ui/button'
```

**Now wait while it installs...**

### Step 2: Understanding What Got Created

```bash
cd wiki-app
ls
```

You'll see:

```
wiki-app/
├── src/
│   └── app/
│       ├── page.tsx          # Homepage
│       ├── layout.tsx        # Root layout (wraps all pages)
│       ├── globals.css       # Global styles
│       └── favicon.ico       # Site icon
├── public/                   # Static files (images, etc.)
├── node_modules/             # Dependencies (don't touch)
├── package.json              # Project config
├── tsconfig.json             # TypeScript config
├── tailwind.config.ts        # Tailwind config
├── next.config.js            # Next.js config
└── .gitignore                # Files git should ignore
```

**Let's understand the key files:**

#### `package.json` - Project Manifest

```json
{
  "name": "wiki-app",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",        // Run development server
    "build": "next build",    // Build for production
    "start": "next start",    // Run production server
    "lint": "next lint"       // Check code quality
  },
  "dependencies": {
    "react": "^19.0.0",       // React library
    "react-dom": "^19.0.0",   // React DOM renderer
    "next": "15.1.0"          // Next.js framework
  }
}
```

**What are dependencies?**
Think of them as ingredients for a recipe:
- React = The base framework
- React-DOM = Renders React to browser
- Next.js = The "chef" that orchestrates everything

#### `src/app/layout.tsx` - Root Layout

```typescript
import type { Metadata } from 'next'
import { Inter } from 'next/font/google'
import './globals.css'

// What is this?
const inter = Inter({ subsets: ['latin'] })

export const metadata: Metadata = {
  title: 'Create Next App',
  description: 'Generated by create next app',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        {children}
      </body>
    </html>
  )
}
```

**Understanding this file:**

1. **What is a Layout?**
   - Wraps ALL pages
   - Shows on every route
   - Perfect for nav bars, footers
   - Think of it as the "frame" around your content

2. **What is `{children}`?**
   ```
   Layout (this file)
   └── children
       ├── Home page
       ├── Articles page
       ├── Article detail page
       └── etc.
   ```

3. **What is `Inter`?**
   - A font from Google Fonts
   - Automatically optimized by Next.js
   - Better than `<link>` tags in HTML

4. **What is `metadata`?**
   - SEO information
   - Shows in browser tab
   - Shows in Google search results

#### `src/app/page.tsx` - Homepage

```typescript
export default function Home() {
  return (
    <main>
      <h1>Welcome to Next.js!</h1>
    </main>
  )
}
```

**This is a React Server Component.**

**What's that mean?**
- Runs on SERVER, not browser
- Can directly query database
- Can access secrets (API keys)
- HTML sent to browser (fast!)

**Server Component vs Client Component:**

```typescript
// Server Component (default)
// - Runs on server
// - Can't use useState, useEffect, onClick
// - Can directly query database
export default async function ArticlesPage() {
  const articles = await db.query.articles.findMany()
  return <div>{articles.map(...)}</div>
}

// Client Component (opt-in with 'use client')
// - Runs in browser
// - Can use useState, useEffect, onClick
// - Can't directly query database
'use client'
export default function Counter() {
  const [count, setCount] = useState(0)
  return <button onClick={() => setCount(count + 1)}>{count}</button>
}
```

**Rule of thumb:**
- Use Server Components by default (faster, better SEO)
- Only use Client Components when you need interactivity

### Step 3: Run Development Server

```bash
npm run dev
```

**What happens:**
1. Next.js starts development server
2. Watches files for changes
3. Auto-reloads browser on save
4. Runs on http://localhost:3000

**Open browser:** http://localhost:3000

You should see the Next.js welcome page!

### Step 4: Make Your First Change

Let's prove you understand by making a change.

**Open `src/app/page.tsx` and replace with:**

```typescript
export default function Home() {
  return (
    <main className="flex min-h-screen items-center justify-center">
      <div className="text-center">
        <h1 className="text-4xl font-bold mb-4">
          📚 WikiApp
        </h1>
        <p className="text-gray-600">
          Building a production-ready wiki platform
        </p>
      </div>
    </main>
  )
}
```

**Save the file.** The browser should auto-reload and show your changes!

**Understanding the Tailwind classes:**
```typescript
className="flex min-h-screen items-center justify-center"
// flex → Use flexbox layout
// min-h-screen → Minimum height = full screen
// items-center → Center vertically
// justify-center → Center horizontally

className="text-4xl font-bold mb-4"
// text-4xl → Very large text
// font-bold → Bold weight
// mb-4 → Margin bottom (spacing)
```

## 🧠 **CHECKPOINT: Try It Yourself**

Before moving forward, do these exercises to prove you understand:

### Exercise 1: Add Another Page

1. Create `src/app/about/page.tsx`
2. Add some content
3. Visit http://localhost:3000/about
4. It should work automatically!

**Solution:**
```typescript
// src/app/about/page.tsx
export default function AboutPage() {
  return (
    <main className="p-8">
      <h1 className="text-3xl font-bold">About WikiApp</h1>
      <p className="mt-4">A modern wiki platform built with Next.js</p>
    </main>
  )
}
```

### Exercise 2: Add Navigation

1. Update `src/app/layout.tsx` to include a nav bar
2. Add links to Home and About

**Hint:** Use Next.js `<Link>` component:
```typescript
import Link from 'next/link'

<Link href="/about">About</Link>
```

### Exercise 3: Break and Fix

1. Remove `export default` from page.tsx
2. What error do you get? Why?
3. Fix it and understand the error message

### Exercise 4: Explain It Back

Out loud or in writing, explain:
1. What is a layout and why do we need it?
2. What's the difference between Server and Client Components?
3. Why do we use App Router instead of Pages Router?
4. How does file-based routing work?

**If you can't explain these clearly, re-read the sections above.**

---

## Installing Additional Tools

Now that you understand the basics, let's add more tools.

### Why Do We Need More Tools?

Next.js gives us the foundation, but we need:
- ✅ **Code formatter** (Biome) - Keeps code consistent
- ✅ **UI components** (shadcn) - Professional-looking UI
- ✅ **Database library** (Drizzle) - Talk to database
- ✅ **Authentication** (Stack Auth) - User login
- ✅ **And more...**

### Understanding Package Management

**What is npm?**
- Node Package Manager
- Downloads and manages dependencies
- Like an app store for code libraries

**Three ways to add packages:**

```bash
# 1. Add to package.json + install
npm install package-name

# 2. Add as dev dependency (only for development)
npm install -D package-name

# 3. Install globally (available everywhere)
npm install -g package-name
```

**Dev dependency vs Regular dependency:**

```
Regular dependency (npm install X):
- Needed to RUN the app
- Example: React, Next.js
- Goes to production

Dev dependency (npm install -D X):
- Needed to DEVELOP the app
- Example: TypeScript compiler, linters
- NOT needed in production
```

### Install Everything

Run this command:

```bash
npm install @radix-ui/react-slot class-variance-authority clsx tailwind-merge lucide-react drizzle-orm @neondatabase/serverless @stackframe/stack @upstash/redis cloudinary resend react-email @react-email/components openai zod
```

```bash
npm install -D biome drizzle-kit tsx
```

**What did we just install? Let's understand each:**

#### UI & Styling:
```
@radix-ui/react-slot → Headless UI primitives
class-variance-authority → Manage CSS variants
clsx → Conditional CSS classes
tailwind-merge → Merge Tailwind classes
lucide-react → Icon library
```

#### Database:
```
drizzle-orm → TypeScript ORM for database
@neondatabase/serverless → Neon Postgres driver
drizzle-kit (dev) → Database migration tool
```

#### Services:
```
@stackframe/stack → Authentication
@upstash/redis → Caching
cloudinary → Image uploads
resend → Email sending
react-email → Email templates
@react-email/components → Email components
```

#### AI & Utils:
```
openai → OpenAI-compatible API (works with OpenRouter)
zod → Schema validation
```

#### Dev Tools:
```
biome → Fast formatter/linter (like ESLint + Prettier)
tsx → Run TypeScript files directly
```

### Configure Biome

Biome replaces ESLint + Prettier with one fast tool.

Create `biome.json`:

```json
{
  "$schema": "https://biomejs.dev/schemas/1.9.4/schema.json",
  "vcs": {
    "enabled": true,
    "clientKind": "git",
    "useIgnoreFile": true
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2,
    "lineWidth": 100
  },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true
    }
  },
  "javascript": {
    "formatter": {
      "quoteStyle": "single",
      "semicolons": "always"
    }
  }
}
```

**What does this do?**
- Formats code automatically
- Catches common errors
- Enforces consistent style
- Way faster than ESLint + Prettier

**Add scripts to `package.json`:**

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "biome check .",
    "format": "biome format --write .",
    "db:generate": "drizzle-kit generate",
    "db:migrate": "drizzle-kit migrate",
    "db:seed": "tsx src/lib/db/seed.ts"
  }
}
```

**Test it:**
```bash
npm run lint      # Check for errors
npm run format    # Format all files
```

---

## 🧠 CHECKPOINT: Review Part 1

Before moving to Part 2, make sure you understand:

1. **Why serverless architecture?**
   - No servers to manage
   - Auto-scaling
   - Pay per use
   - Global edge network

2. **What are Server Components?**
   - Run on server only
   - Don't ship to browser
   - Direct database access
   - Better performance

3. **What are Client Components?**
   - Run in browser
   - Interactive (onClick, etc.)
   - Use hooks (useState, etc.)
   - Marked with 'use client'

4. **Why this tech stack?**
   - Next.js: Full-stack framework
   - Neon: Serverless Postgres
   - Drizzle: Type-safe ORM
   - Stack Auth: Authentication
   - All have generous free tiers!

5. **What's the project structure?**
   ```
   src/
   ├── app/         # Pages and routes
   ├── components/  # Reusable UI
   ├── lib/         # Utilities and config
   └── types/       # TypeScript types
   ```

Take a moment to explain each concept out loud using the Feynman Technique.

---

## Summary

You now understand:

✅ **Modern web architecture** - Serverless, edge, SSR  
✅ **Next.js fundamentals** - App Router, Server/Client Components  
✅ **Tech stack decisions** - Why each service was chosen  
✅ **Project setup** - All tools configured properly  
✅ **Development workflow** - Linting, formatting, scripts  

### Key Takeaways

1. **Serverless is the future** - No infrastructure management
2. **Server Components are default** - Client only when needed
3. **TypeScript provides safety** - Catch errors at build time
4. **Free tiers are generous** - Build and deploy for $0
5. **Tools improve productivity** - Linting, formatting, hot reload

### What's Next?

In Part 2, we'll build the UI:
- Modern CSS with Tailwind
- Component architecture with shadcn
- Building Navigation component
- Creating Article Cards
- Responsive design patterns
- Layout strategies

[→ Continue to Part 2: UI & Component Design](./02-ui-and-components.md)

---

**Excellent work completing Part 1!** You've built a solid foundation. Take a break, then continue when ready. 🚀
