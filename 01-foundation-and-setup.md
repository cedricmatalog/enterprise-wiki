# Part 1: Foundation & Setup
## Understanding Modern Web Architecture

> **Goal:** Understand WHY we build apps this way  
> **Time:** 6-8 hours  
> **Prerequisites:** Basic programming knowledge

[Back to Index](./README.md) | [Next: Part 2 →](./02-ui-and-components.md)

---

## 📍 Progress: Part 1 of 7 (14% Complete)

---

## 🎯 What You'll Learn

By the end of this part, you'll understand:

✅ **Modern web architecture** - How apps work today  
✅ **Serverless computing** - Why it's the future  
✅ **Next.js fundamentals** - Server vs Client Components  
✅ **Tech stack decisions** - Why each tool was chosen  
✅ **Project setup** - Getting everything running  

---

## ⏱️ Time Breakdown

- **Reading & Understanding:** 2-3 hours
- **Environment Setup:** 1-2 hours  
- **Coding & Exercises:** 2-3 hours
- **Debugging & Troubleshooting:** 30 min - 1 hour

**Total:** 6-8 hours (spread over 2-3 days recommended)

---

## 🔴 THE PROBLEM: Building Modern Web Apps

### What Are We Building?

> **TL;DR**  
> A Wikipedia-style knowledge base where users can read and write articles.  
> Think Medium + Wikipedia, but you understand every piece.

**Core Features:**
- ✅ Anyone can read articles
- ✅ Users must sign in to write
- ✅ Authors own their content  
- ✅ Images, summaries, view tracking
- ✅ Fast and scalable

**Real-World Examples:**
- Medium (article creation)
- Wikipedia (knowledge sharing)
- Dev.to (community content)

---

### Why This Project?

This isn't just a tutorial project. It teaches you:

**1. Real production patterns**
- Authentication & authorization
- Database design & optimization
- Caching strategies
- File uploads & storage
- Email systems
- AI integration
- CI/CD deployment

**2. Modern architecture**
- Serverless functions
- Edge computing
- Type-safe development
- Component-based UI

**3. Career skills**
You'll be able to:
- Build full-stack apps independently
- Make architectural decisions
- Explain your code in interviews
- Deploy to production
- Debug complex issues

> **💡 Pro Tip**  
> This single project covers 80% of what you'll do as a web developer.  
> Master it, and you're job-ready!

---

## 🌍 Modern Web Architecture

### The Evolution

> **TL;DR**  
> We went from servers in closets to functions in the cloud.  
> Modern apps are faster, cheaper, and scale automatically.

**2010s - Traditional Architecture:**

```
Problem: Need a web app
Solution:
1. Buy/rent a server ($50-200/month)
2. Install Linux, configure everything
3. Set up MySQL, configure backups
4. Deploy app manually via FTP
5. Monitor server 24/7
6. Scale by buying bigger servers

Issues:
❌ Expensive (always running)
❌ Manual maintenance
❌ Limited by server size
❌ Single point of failure
❌ Slow deployment
```

**2020s - Serverless Architecture:**

```
Problem: Need a web app  
Solution:
1. Write code
2. Push to GitHub
3. Automatic deployment
4. Pay only for usage
5. Auto-scales to billions

Benefits:
✅ Cheap ($0-5/month typical)
✅ Zero maintenance
✅ Infinite scaling
✅ Global edge network
✅ Deploy in seconds
```

---

### Understanding Serverless

> **⚠️ Common Misconception**  
> "Serverless" doesn't mean "no servers."  
> It means YOU don't manage them!

**Mental Model:**

Think of serverless like **electricity**:

**Old Way (Traditional Servers):**
- Buy a generator
- Maintain it yourself
- Pay for it even when not using
- Limited by generator size

**New Way (Serverless):**
- Plug into the grid
- Pay only for what you use
- Automatic scaling
- Someone else handles maintenance

**Your app works the same way:**

```
Traditional Server:
┌─────────────────────┐
│ Your Server         │
│ (running 24/7)      │
├─────────────────────┤
│ Idle at night?      │
│ → Still paying! 💸  │
│                     │
│ Traffic spike?      │
│ → Server crashes! 💥│
└─────────────────────┘

Serverless Functions:
┌─────────────────────┐
│ Cloud Functions     │
│ (on-demand only)    │
├─────────────────────┤
│ No traffic?         │
│ → No cost! ✅       │
│                     │
│ Traffic spike?      │
│ → Auto-scales! ⚡   │
└─────────────────────┘
```

> **⚡ Key Takeaway**
>
> **Serverless = Pay for execution time, not idle time**
>
> - Traditional server: $50/month even with 0 visitors  
> - Serverless: $0/month with 0 visitors, $5/month with 10,000 visitors
>
> **Result:** 90% cost savings for most apps!

---

### Real-World Cost Comparison

**Scenario:** Blog with 5,000 monthly visitors

| Approach | Monthly Cost | Maintenance | Scaling |
|----------|--------------|-------------|---------|
| **VPS (Digital Ocean)** | $10-20 | Manual updates | Manual |
| **Managed Hosting** | $20-50 | Some updates | Limited |
| **Serverless (Vercel)** | **$0** | Automatic | Automatic |

**Why is serverless free?**
- Only pay for actual compute time
- 5,000 visitors = ~10 seconds of compute  
- Free tier covers most small apps!

**This tutorial teaches the MODERN way.**

---

### ☕ Quick Break (5 minutes)

**You've learned:**
- ✅ What we're building and why
- ✅ Evolution of web architecture  
- ✅ What serverless means
- ✅ Cost comparison

**Before continuing:** Can you explain serverless to someone in your own words?

**Coming up next:** Next.js fundamentals and Server vs Client Components

---

# Modern Web Architecture

> **📚 TL;DR - The Big Picture**
>
> Modern web apps have **3 layers:**
> 1. **Frontend** (browser) - What users see
> 2. **Backend** (serverless) - Your logic
> 3. **Services** (managed) - Database, cache, auth
>
> **Key insight:** You only write the middle part!  
> The rest is handled by services.

---

## Understanding the Pieces

Before we code, let's understand what a modern web app actually IS.

### The Mental Model: Restaurant Analogy 🍽️

Think of building an app like running a restaurant:

**Traditional Restaurant (Old Web):**
```
❌ You own the building (Expensive server)
❌ You hire chefs (Write all code)
❌ You maintain kitchen (Manage database)
❌ You serve customers (Handle traffic)
❌ Open 24/7 even if empty (Always paying)
❌ Pay rent with no customers (Wasted money)
```

**Cloud Kitchen (Modern Web):**
```
✅ Rent space when needed (Serverless)
✅ Use shared equipment (Managed services)
✅ Pay per meal cooked (Usage-based pricing)
✅ Auto-expand during rush (Auto-scaling)
✅ Close when no customers (Pay nothing!)
```

> **💡 Pro Tip**
>
> Your first 5,000 users? Usually costs $0/month with free tiers!
> That's why startups can launch with no budget.

---

### Modern Web App Components

Here's how all the pieces fit together:

```
┌─────────────────────────────────────────────┐
│           USER'S BROWSER                     │
│  (React/Next.js Frontend)                    │
│  - Shows UI                                  │
│  - Handles user input                        │
│  - Makes requests                            │
└──────────────┬──────────────────────────────┘
               │
               ↓ (Request goes to nearest edge)
┌─────────────────────────────────────────────┐
│         VERCEL EDGE NETWORK                  │
│  (Global CDN - 20+ locations)                │
│  - Routes requests                           │
│  - Caches content                            │
│  - Runs at edge locations                    │
└──────────────┬──────────────────────────────┘
               │
               ↓ (Executes your code)
┌─────────────────────────────────────────────┐
│      NEXT.JS SERVER FUNCTIONS                │
│  (Your Application Code - Serverless!)       │
│  - Server actions                            │
│  - API routes                                │
│  - Business logic                            │
└───┬────────┬────────┬────────┬──────────────┘
    │        │        │        │
    ↓        ↓        ↓        ↓ (Your code talks to services)
┌────────┐ ┌────────┐ ┌──────┐ ┌──────────┐
│ Neon   │ │Upstash │ │Cloud-│ │  Stack   │
│Postgres│ │ Redis  │ │inary │ │   Auth   │
│Database│ │ Cache  │ │Images│ │ Login    │
└────────┘ └────────┘ └──────┘ └──────────┘
   (Data)   (Speed)   (Files)  (Users)
```

**What you build:** Just the middle box (Next.js code)  
**What's free:** Everything else (services with free tiers)

---

### Key Concepts to Understand

#### 1. **Client vs Server** (Critical to understand!)

> **📌 Remember This**
>
> **CLIENT = User's device (can't be trusted)**  
> **SERVER = Your code (secure & trusted)**
>
> This distinction is fundamental to web security!

**CLIENT (Browser):**
- ✅ Runs on user's computer
- ❌ Can be slow (old phone, slow internet)
- ❌ Can't be trusted (user can modify code)
- ❌ Can't access secrets (API keys visible in code)

**Example of client code:**
```typescript
'use client';  // This runs in browser

function LikeButton() {
  // ❌ DON'T store secrets here!
  const API_KEY = 'secret123';  // User can see this!
  
  // ✅ DO show UI and handle interactions
  return <button onClick={() => alert('Liked!')}>Like</button>;
}
```

**SERVER (Your code on Vercel):**
- ✅ Always fast and reliable
- ✅ Completely trusted
- ✅ Keeps secrets safe
- ✅ Can access database directly

**Example of server code:**
```typescript
'use server';  // This runs on server

async function likeArticle(articleId: string) {
  // ✅ Can use secrets safely
  const API_KEY = process.env.SECRET_KEY;  // Hidden from users
  
  // ✅ Can access database
  await db.update(articles).set({ likes: likes + 1 });
}
```

> **⚠️ Common Mistake**
>
> Never put database credentials or API keys in client code!
> ```typescript
> // ❌ NEVER DO THIS
> 'use client';
> const DATABASE_URL = 'postgresql://...';  // Exposed to everyone!
> ```

**Rule:** 
- 🎨 Put UI logic in CLIENT
- 🔒 Put sensitive logic in SERVER

---

#### 2. **Serverless Functions** (How your code runs)

Let's understand serverless with a comparison:

**Traditional Server (Old Way):**
```javascript
// Server running 24/7
const express = require('express');
const server = express();
server.listen(3000); 

// Always running
// Always using memory
// Always costing money ($50/month minimum)
```

**Serverless Function (New Way):**
```javascript
// Only exists when someone makes a request
export async function handleRequest() {
  // Spins up: 50ms
  // Runs your code: 100ms
  // Returns response
  // Disappears completely
  // Cost: $0.0000002 per request
}
```

**Benefits:**
- ✅ Pay only for actual usage (10x cheaper)
- ✅ Auto-scales to millions (no config needed)
- ✅ No server maintenance (updates automatic)
- ✅ FREE tier is very generous (100,000 requests/day!)

**When you pay:**
```
Traditional: $50/month with 0 users
Serverless: $0/month with 0 users, $0/month with 5,000 users
```

---

#### 3. **Edge Computing** (Why your app is fast)

> **💡 Pro Tip**
>
> Edge computing = Your code runs in 20+ locations worldwide  
> Users always connect to the nearest one

**Traditional (Slow):**
```
User in Philippines
  → Sends request to US server
  → Travels 12,000 km
  → 200ms delay 🐌

User in US
  → Sends request to US server  
  → Travels 100 km
  → 20ms delay
```

**Edge Computing (Fast):**
```
User in Philippines
  → Request to Manila edge server
  → Travels 50 km
  → 10ms delay ⚡

User in US
  → Request to LA edge server
  → Travels 20 km  
  → 10ms delay ⚡
```

**How it works:**
1. Deploy code once to Vercel
2. Vercel copies it to 20+ locations
3. DNS routes user to nearest location
4. Everyone gets fast response (<50ms)

**Locations include:**
- San Francisco, New York, London
- Tokyo, Singapore, Mumbai
- São Paulo, Sydney, Toronto
- And many more!

---

### ☕ Quick Break (5 minutes)

**You've learned a LOT! Before continuing:**

Can you explain these concepts?
- ✅ Client vs Server (why the difference matters)
- ✅ Serverless functions (how they work)
- ✅ Edge computing (why it's fast)

**Take 5 minutes to:**
1. Stand up and stretch
2. Grab water or coffee
3. Explain one concept out loud

**Coming up next:** Why we chose each service in our tech stack

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

# Why This Tech Stack?

> **📚 TL;DR - Our Free Tech Stack**
>
> **Every service has 3 requirements:**
> 1. ✅ FREE tier (no credit card needed)
> 2. ✅ Production-ready (not toys)
> 3. ✅ Generous limits (handle real users)
>
> **Result:** $0/month for your first 5,000 users!

---

## The Decision Framework

For each need, we compared ALL options systematically:

**Our criteria:**
1. **Cost** - Must have free tier without credit card
2. **Limits** - Must support real apps (not just demos)
3. **Setup** - Must be quick to get started
4. **Quality** - Must be production-grade
5. **Future** - Must be able to upgrade when needed

Let's see how we chose each service...

---

### ☕ 30-Second Break

You're halfway through Part 1! Quick stretch, then continue.

---

### Decision 1: Database

#### Authentication: Why Stack Auth?

**The Problem:**
Users need to sign up, log in, and stay logged in securely.

> **⚠️ Warning**
>
> Never build authentication yourself!  
> Security is hard, and mistakes expose user data.

**Options Compared:**

| Option | Free Tier | Setup Time | Features | Card Required | Verdict |
|--------|-----------|------------|----------|---------------|---------|
| DIY (custom) | Free | 2 weeks | Basic | No | ❌ Too risky |
| NextAuth.js | Free | 2 days | Good | No | ✅ But complex |
| Clerk | 10k MAU | 1 hour | Great | **Yes** | ❌ Card needed |
| Supabase Auth | 50k MAU | 1 hour | Good | No | ✅ Good option |
| **Stack Auth** | **1k MAU** | **30 min** | **Great** | **No** | **✅ Best!** |

**Why Stack Auth won:**
- ✅ No credit card required
- ✅ Drop-in solution (literally copy-paste)
- ✅ Handles everything (emails, OAuth, password reset)
- ✅ 1,000 monthly active users = plenty for learning

**Trade-off:**
- Only 1,000 MAU on free tier
- Upgrade path: Supabase Auth (50k free) or Clerk (paid)

**Bottom line:** Perfect for learning, can switch later if needed.

---

#### Caching: Why Upstash Redis?

**The Problem:**
Database queries are slow (50-200ms). Users expect fast (<50ms).

**Solution:** Cache frequently-accessed data in memory.

**Options Compared:**

| Option | Free Tier | Setup | Serverless | Card Required | Verdict |
|--------|-----------|-------|------------|---------------|---------|
| In-memory (Map) | Free | 1 min | No | No | ❌ Resets on deploy |
| Vercel KV | 256MB | Easy | Yes | **Yes** | ❌ Card needed |
| Redis Labs | 30MB | Medium | No | No | ✅ OK but tiny |
| **Upstash** | **10k cmds/day** | **Easy** | **Yes** | **No** | **✅ Perfect!** |

**Why Upstash won:**
- ✅ No credit card needed
- ✅ 10,000 commands/day (plenty for learning)
- ✅ REST API (works with serverless)
- ✅ Redis-compatible (industry standard)

**What 10k commands means:**
```
Scenario: Article with 1,000 views/day
- Each view: 1 INCR command = 1 command
- Total: 1,000 commands/day
- Still have 9,000 left for other caching!
```

**Trade-off:**
- Command-limited (not storage-limited)
- Perfect for view counters and query caching
- May need upgrade for extremely high traffic

---

#### Images: Why Cloudinary?

**The Problem:**
Users upload images. Need to store, optimize, and serve them globally.

> **💡 Pro Tip**
>
> Never store images in your database!  
> Use object storage (S3-style) instead.

**Options Compared:**

| Option | Storage | Bandwidth | Optimization | Card Required | Verdict |
|--------|---------|-----------|--------------|---------------|---------|
| Filesystem | N/A | N/A | None | No | ❌ Disappears |
| AWS S3 | $0.023/GB | $0.09/GB | None | **Yes** | ❌ Complex |
| Vercel Blob | 1GB | Included | Some | **Yes** | ❌ Card needed |
| ImageKit | 20GB | 20GB | Great | No | ✅ Good |
| **Cloudinary** | **25GB** | **25GB** | **Amazing** | **No** | **✅ Best!** |

**Why Cloudinary won:**
- ✅ 25GB free storage (huge!)
- ✅ 25GB free bandwidth
- ✅ Automatic optimization (images 90% smaller!)
- ✅ Image transformations (resize, crop, etc.)
- ✅ Global CDN delivery
- ✅ No credit card required

**What 25GB means:**
```
Average image: 500KB (after optimization)
25GB = 25,000 MB
25,000 MB ÷ 0.5 MB = 50,000 images!

That's enough for most apps!
```

**Trade-off:**
- Need to learn their upload API
- Well-documented, takes 30 minutes

---

#### AI: Why OpenRouter?

**The Problem:**
Need AI for article summaries, but OpenAI/Anthropic require payment.

**Options Compared:**

| Option | Cost | Quality | Speed | Card Required | Verdict |
|--------|------|---------|-------|---------------|---------|
| OpenAI GPT-4 | $5 credit | Best | Fast | **Yes** | ❌ Paid |
| Anthropic Claude | Pay-per-use | Best | Fast | **Yes** | ❌ Paid |
| Hugging Face | Free | OK | Slow | No | ✅ Truly free |
| **OpenRouter** | **Free models** | **Good** | **Fast** | **No** | **✅ Best!** |

**Why OpenRouter won:**
- ✅ No credit card required
- ✅ Access to free open-source models (Gemma 2, Llama, etc.)
- ✅ Same API as OpenAI (easy migration later)
- ✅ Good enough quality for summaries

**Free models available:**
- **Gemma 2 9B:** Google's model (good for summaries)
- **Llama 3.1 8B:** Meta's model (fast)
- **Mistral 7B:** Good quality
- **Many more!**

**Trade-off:**
- Free models not as powerful as GPT-4
- But Gemma 2 works great for article summaries
- Can upgrade to paid models when needed

---

### The Complete Stack Summary

Here's everything together:

```
┌─────────────────────────────────────────────┐
│  Frontend: Next.js 15 + React               │
│  ✅ Free, open source                       │
│  ✅ Best React framework                    │
│  ✅ Excellent developer experience          │
├─────────────────────────────────────────────┤
│  Styling: Tailwind CSS + shadcn/ui          │
│  ✅ Free, open source                       │
│  ✅ Fast development                        │
│  ✅ Professional UI components              │
├─────────────────────────────────────────────┤
│  Database: Neon Postgres                    │
│  ✅ 500MB free                              │
│  ✅ Serverless scaling                      │
│  ✅ No credit card needed                   │
├─────────────────────────────────────────────┤
│  ORM: Drizzle                               │
│  ✅ Free, open source                       │
│  ✅ Type-safe queries                       │
│  ✅ Lightweight (10KB)                      │
├─────────────────────────────────────────────┤
│  Auth: Stack Auth                           │
│  ✅ 1,000 MAU free                          │
│  ✅ Drop-in solution                        │
│  ✅ No credit card needed                   │
├─────────────────────────────────────────────┤
│  Cache: Upstash Redis                       │
│  ✅ 10k commands/day free                   │
│  ✅ Serverless Redis                        │
│  ✅ No credit card needed                   │
├─────────────────────────────────────────────┤
│  Images: Cloudinary                         │
│  ✅ 25GB storage free                       │
│  ✅ Auto optimization                       │
│  ✅ No credit card needed                   │
├─────────────────────────────────────────────┤
│  Email: Resend                              │
│  ✅ 100 emails/day free                     │
│  ✅ React Email templates                   │
│  ✅ No credit card needed                   │
├─────────────────────────────────────────────┤
│  AI: OpenRouter                             │
│  ✅ Free models available                   │
│  ✅ OpenAI-compatible API                   │
│  ✅ No credit card needed                   │
├─────────────────────────────────────────────┤
│  Hosting: Vercel                            │
│  ✅ Unlimited deploys free                  │
│  ✅ Global edge network                     │
│  ✅ No credit card needed                   │
└─────────────────────────────────────────────┘

💰 Total Monthly Cost: $0.00
📊 Supports: ~5,000 active users
🚀 Can upgrade: When you need more
```

> **⚡ Key Takeaway**
>
> **This stack is production-ready!**
>
> You're not learning with "toy" tools.  
> These are the same services used by:
> - Vercel (for Next.js itself!)
> - Thousands of startups
> - Many enterprise companies
>
> Master this, and you're job-ready!

---

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
