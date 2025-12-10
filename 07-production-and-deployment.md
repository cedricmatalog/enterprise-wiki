# Part 7: Production & Deployment
## Taking Your App Live with Confidence

> **Goal:** Deploy your app to production with CI/CD  
> **Time:** 5-7 hours  
> **Prerequisites:** Completed Parts 1-6

[← Back to Part 6](./06-services-and-integrations.md) | [Back to Index](./README.md)

---

## What We'll Learn

✅ **DevOps fundamentals** - What is it?  
✅ **Git & GitHub** - Version control basics  
✅ **Vercel deployment** - Going live (FREE!)  
✅ **Environment variables** - Managing configs  
✅ **Preview deployments** - Test before production  
✅ **CI/CD with GitHub Actions** - Automated testing  
✅ **Monitoring** - Know what's happening  
✅ **Production best practices** - Stay reliable  

---

## The Problem: Manual Deployment

### Old Way (Manual Deployment) 🔴

```
Developer workflow:
1. Write code locally
2. Test manually (sometimes)
3. FTP files to server
4. Cross fingers 🤞
5. Something breaks
6. Panic! 😱
7. Try to rollback
8. More panic!
```

**Problems:**
- ❌ No testing before deploy
- ❌ Manual process (error-prone)
- ❌ Downtime during deploy
- ❌ Hard to rollback
- ❌ No deployment history
- ❌ Team conflicts (who deployed what?)

### Modern Way (CI/CD) ✅

```
Developer workflow:
1. Write code locally
2. Commit to git
3. Push to GitHub
4. Automatic tests run
5. Automatic deployment
6. Rollback in 1 click
7. Sleep well! 😴
```

**Benefits:**
- ✅ Automated testing
- ✅ Push to deploy
- ✅ Zero downtime
- ✅ Easy rollback
- ✅ Full history
- ✅ Team collaboration

---

## Understanding DevOps

### What is DevOps?

**DevOps = Development + Operations**

```
Before DevOps:
Developers ─────────────► Write code
                          │
                          ▼
                          Wait...
                          │
                          ▼
Operations ──────────────► Deploy code
                          (Manual, slow)

With DevOps:
Developers ──► Code ──► Git ──► Automated ──► Production
                                 Pipeline
                                    ↓
                                  Tests
                                    ↓
                                  Build
                                    ↓
                                  Deploy
```

**Key Concepts:**

1. **Version Control** (Git)
   - Track all changes
   - Collaborate with team
   - Revert mistakes

2. **Continuous Integration** (CI)
   - Automatically test code
   - Catch bugs early
   - Maintain quality

3. **Continuous Deployment** (CD)
   - Automatically deploy
   - Fast releases
   - Reduce risk

4. **Monitoring**
   - Track errors
   - Measure performance
   - User analytics

---

## Git & GitHub Setup

### Understanding Version Control

**Git = Time machine for code**

```
Without Git:
my-project/
├── index.js
├── index-old.js
├── index-BACKUP.js
├── index-FINAL.js
└── index-FINAL-FINAL.js  😱

With Git:
my-project/
├── index.js
└── .git/  (All history stored here)

git log:
├── commit abc123: "Add feature"
├── commit def456: "Fix bug"
└── commit ghi789: "Initial commit"
```

### Initialize Git Repository

```bash
# In your project folder
git init

# Create .gitignore
cat > .gitignore << EOF
node_modules/
.next/
.env
.env.local
.vercel
*.log
EOF

# Initial commit
git add .
git commit -m "Initial commit"
```

### Create GitHub Repository

1. Go to [github.com](https://github.com)
2. Click "New repository"
3. Name: `wikiapp`
4. Keep it **public** (for free deployment)
5. Don't initialize (we already have code)
6. Click "Create"

### Push to GitHub

```bash
# Add remote
git remote add origin https://github.com/YOUR_USERNAME/wikiapp.git

# Push code
git branch -M main
git push -u origin main
```

**Now your code is on GitHub!** ✅

---

## Deploying to Vercel

### Why Vercel?

**Comparison:**

| Platform | Free Tier | CI/CD | Edge | Setup |
|----------|-----------|-------|------|-------|
| Heroku | Limited | ❌ | ❌ | Medium |
| Railway | $5/month | ✅ | ❌ | Easy |
| Netlify | Good | ✅ | ✅ | Easy |
| **Vercel** | **Great** | **✅** | **✅** | **Easiest** |

**Vercel wins:**
- ✅ Unlimited deploys (free!)
- ✅ Made by Next.js creators
- ✅ Automatic CI/CD
- ✅ Global edge network
- ✅ Zero config
- ✅ Preview deployments

### Step 1: Create Vercel Account

1. Go to [vercel.com](https://vercel.com)
2. Click "Sign Up"
3. **Sign up with GitHub** (important!)
4. Authorize Vercel

### Step 2: Import Project

1. Click "Add New Project"
2. Import `wikiapp` repository
3. Vercel detects Next.js automatically ✅

### Step 3: Configure Environment Variables

**Important:** Add ALL your environment variables!

```
NEXT_PUBLIC_STACK_PROJECT_ID=...
NEXT_PUBLIC_STACK_PUBLISHABLE_CLIENT_KEY=...
STACK_SECRET_SERVER_KEY=...
DATABASE_URL=...
UPSTASH_REDIS_REST_URL=...
UPSTASH_REDIS_REST_TOKEN=...
CLOUDINARY_CLOUD_NAME=...
CLOUDINARY_API_KEY=...
CLOUDINARY_API_SECRET=...
RESEND_API_KEY=...
OPENROUTER_API_KEY=...
CRON_SECRET=...
NEXT_PUBLIC_APP_URL=https://wikiapp.vercel.app
```

**Click "Deploy"!** 🚀

### Step 4: Wait for Build

```
Building...
├── Installing dependencies
├── Running build
├── Optimizing assets
└── Deploying to edge

✅ Deployed to https://wikiapp.vercel.app
```

**Your app is LIVE!** 🎉

---

## Understanding Deployments

### What Just Happened?

```
1. You pushed code to GitHub
   └─> GitHub repository updated

2. Vercel detected the push
   └─> Webhook triggered

3. Vercel cloned your code
   └─> Downloaded all files

4. Vercel built your app
   ├─> npm install
   ├─> npm run build
   └─> Generated optimized files

5. Vercel deployed to edge
   ├─> Distributed to 20+ locations
   └─> Available globally in seconds

6. You got a URL
   └─> https://wikiapp.vercel.app
```

### Production vs Preview

**Production Deployment:**
```
main branch
└─> Deploys to: wikiapp.vercel.app
    (Your main public URL)
```

**Preview Deployment:**
```
feature-branch
└─> Deploys to: wikiapp-abc123.vercel.app
    (Temporary URL for testing)
```

**Every branch gets its own URL!**

---

## Environment Management

### Understanding Environments

**Three environments:**

1. **Development** (`npm run dev`)
   - Your laptop
   - `.env.local`
   - Fast iteration

2. **Preview** (Vercel)
   - Temporary URL
   - Test before production
   - Preview environment variables

3. **Production** (Vercel)
   - Live app
   - `wikiapp.vercel.app`
   - Production environment variables

### Environment Variables in Vercel

**Set different values per environment:**

```
Database URL:
├─> Development: localhost:5432
├─> Preview: preview.neon.tech
└─> Production: production.neon.tech

This prevents:
- Testing on production database ❌
- Accidentally deleting real data ❌
```

**How to set:**

1. Go to Vercel project settings
2. Environment Variables
3. Add variable
4. Select environments:
   - ☑️ Production
   - ☑️ Preview
   - ☑️ Development

---

## Preview Deployments

### The Workflow

```
1. Create feature branch
   git checkout -b add-comments

2. Make changes
   [code changes]

3. Commit and push
   git add .
   git commit -m "Add comments feature"
   git push origin add-comments

4. Create Pull Request on GitHub
   [Open PR]

5. Vercel automatically deploys preview
   ✅ Preview URL: wikiapp-abc123.vercel.app

6. Test preview URL
   ✅ Everything works!

7. Merge PR
   └─> Automatic production deploy!
```

**Benefits:**
- ✅ Test before merging
- ✅ Share with team
- ✅ Client preview
- ✅ No impact on production

---

## GitHub Actions CI/CD

### Why GitHub Actions?

**Add automated testing before deploy:**

```
Without CI:
Push code ──► Deploy ──► Breaks production 💥

With CI:
Push code ──► Run tests ──► Tests pass ──► Deploy ✅
                        └──► Tests fail ──► Block deploy ❌
```

### Create Workflow

Create `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  lint-and-test:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linter
        run: npm run lint
      
      - name: Type check
        run: npx tsc --noEmit
      
      - name: Build
        run: npm run build
        env:
          # Use dummy values for build test
          DATABASE_URL: postgresql://dummy@localhost/test
          NEXT_PUBLIC_STACK_PROJECT_ID: test
          NEXT_PUBLIC_STACK_PUBLISHABLE_CLIENT_KEY: test
          STACK_SECRET_SERVER_KEY: test
```

**What this does:**

```
On every push or PR:
1. Checkout code
2. Setup Node.js
3. Install dependencies
4. Run linter (code quality)
5. Run TypeScript check (type safety)
6. Test build (make sure it builds)

If any step fails ❌
└─> PR shows "Checks failed"
    └─> Can't merge!

If all pass ✅
└─> PR shows "All checks passed"
    └─> Safe to merge!
```

### Add Tests (Optional but Recommended)

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Create `vitest.config.ts`:

```typescript
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
});
```

Create example test `src/lib/__tests__/example.test.ts`:

```typescript
import { describe, it, expect } from 'vitest';

describe('Example test', () => {
  it('should pass', () => {
    expect(1 + 1).toBe(2);
  });
});
```

Add to `package.json`:

```json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest"
  }
}
```

Update GitHub Actions:

```yaml
- name: Run tests
  run: npm test
```

---

## Monitoring & Analytics

### Vercel Analytics (FREE)

**Enable in Vercel dashboard:**
1. Go to project settings
2. Analytics tab
3. Enable "Web Analytics"

**What you get:**
- ✅ Page views
- ✅ Top pages
- ✅ Referrers
- ✅ Countries
- ✅ Devices
- ✅ Real-time data

### Add to Your App

```bash
npm install @vercel/analytics
```

Update `src/app/layout.tsx`:

```typescript
import { Analytics } from '@vercel/analytics/react';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  );
}
```

**Now tracking all page views!** 📊

### Speed Insights

```bash
npm install @vercel/speed-insights
```

Update layout:

```typescript
import { SpeedInsights } from '@vercel/speed-insights/next';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
        <SpeedInsights />
      </body>
    </html>
  );
}
```

**Now tracking performance!** ⚡

---

## Error Tracking

### Console Logs in Production

```typescript
// src/app/error.tsx
'use client';

import { useEffect } from 'react';
import { Button } from '@/components/ui/button';

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    // Log to console (Vercel logs)
    console.error('Error occurred:', error);
  }, [error]);
  
  return (
    <div className="flex flex-col items-center justify-center min-h-screen">
      <h2 className="text-2xl font-bold mb-4">Something went wrong!</h2>
      <Button onClick={reset}>Try again</Button>
    </div>
  );
}
```

**View logs in Vercel:**
1. Go to project
2. "Logs" tab
3. See all errors in real-time

---

## Production Best Practices

### 1. Always Use Environment Variables

```typescript
// ❌ Don't hardcode
const API_KEY = 'abc123';

// ✅ Use env vars
const API_KEY = process.env.API_KEY;
```

### 2. Add Health Check Endpoint

```typescript
// src/app/api/health/route.ts
export async function GET() {
  return Response.json({
    status: 'ok',
    timestamp: new Date().toISOString(),
  });
}
```

**Use for monitoring:**
- Uptime monitors
- Load balancers
- Status pages

### 3. Implement Rate Limiting

```typescript
// Already implemented with Redis!
import { ratelimit } from '@/lib/redis';

export async function POST(request: Request) {
  const ip = request.headers.get('x-forwarded-for') || 'unknown';
  
  const { success } = await ratelimit.limit(ip);
  
  if (!success) {
    return Response.json(
      { error: 'Rate limit exceeded' },
      { status: 429 }
    );
  }
  
  // Handle request
}
```

### 4. Use Proper HTTP Status Codes

```typescript
// 200: Success
return Response.json({ success: true });

// 201: Created
return Response.json({ article }, { status: 201 });

// 400: Bad Request
return Response.json({ error: 'Invalid input' }, { status: 400 });

// 401: Unauthorized
return Response.json({ error: 'Not logged in' }, { status: 401 });

// 403: Forbidden
return Response.json({ error: 'Not allowed' }, { status: 403 });

// 404: Not Found
return Response.json({ error: 'Not found' }, { status: 404 });

// 429: Too Many Requests
return Response.json({ error: 'Rate limited' }, { status: 429 });

// 500: Server Error
return Response.json({ error: 'Server error' }, { status: 500 });
```

### 5. Add robots.txt

```typescript
// src/app/robots.ts
import { MetadataRoute } from 'next';

export default function robots(): MetadataRoute.Robots {
  return {
    rules: {
      userAgent: '*',
      allow: '/',
    },
    sitemap: 'https://wikiapp.vercel.app/sitemap.xml',
  };
}
```

### 6. Add sitemap.xml

```typescript
// src/app/sitemap.ts
import { MetadataRoute } from 'next';
import { db } from '@/lib/db';
import { articles } from '@/lib/db/schema';

export default async function sitemap(): Promise<MetadataRoute.Sitemap> {
  const allArticles = await db.select({
    id: articles.id,
    updatedAt: articles.updatedAt,
  }).from(articles);
  
  const articleUrls = allArticles.map((article) => ({
    url: `https://wikiapp.vercel.app/articles/${article.id}`,
    lastModified: article.updatedAt,
    changeFrequency: 'weekly' as const,
    priority: 0.8,
  }));
  
  return [
    {
      url: 'https://wikiapp.vercel.app',
      lastModified: new Date(),
      changeFrequency: 'daily',
      priority: 1,
    },
    {
      url: 'https://wikiapp.vercel.app/articles',
      lastModified: new Date(),
      changeFrequency: 'daily',
      priority: 0.9,
    },
    ...articleUrls,
  ];
}
```

### 7. Optimize Images

Already using Cloudinary, but also use Next.js Image:

```typescript
import Image from 'next/image';

// ✅ Next.js optimizes automatically
<Image
  src={article.imageUrl}
  alt={article.title}
  width={1200}
  height={600}
  priority={isAboveTheFold}
/>

// ❌ Regular img tag (not optimized)
<img src={article.imageUrl} alt={article.title} />
```

---

## 🧠 CHECKPOINT: Understanding Deployment

Before proceeding, make sure you can explain:

1. **What is DevOps?**
   - Development + Operations
   - Automates deployment process
   - Continuous Integration (CI)
   - Continuous Deployment (CD)
   - Faster, safer releases

2. **What happens when you push to GitHub?**
   - Git tracks changes
   - Push sends to GitHub
   - Vercel webhook triggered
   - Automatic build starts
   - Tests run (if configured)
   - Deploy to edge network
   - Live in seconds!

3. **What are environment variables?**
   - Configuration values
   - Secrets (API keys, DB URLs)
   - Different per environment
   - Never hardcode in code
   - Set in Vercel dashboard

4. **What is a preview deployment?**
   - Every branch gets URL
   - Test before merging
   - Share with team
   - No impact on production
   - Automatic on PR

5. **Why CI/CD matters?**
   - Catch bugs early
   - Automatic testing
   - Consistent deploys
   - Fast rollback
   - Team confidence

**Explain each concept clearly before moving forward.**

---

## 🧪 EXERCISES: Practice DevOps

### Exercise 1: Add Feature Flag

Implement a feature flag to toggle features without redeploying:

```typescript
// src/lib/features.ts
// Create a feature flag system
```

<details>
<summary>💡 Solution</summary>

```typescript
// src/lib/features.ts
export const FEATURES = {
  AI_SUMMARIES: process.env.NEXT_PUBLIC_FEATURE_AI_SUMMARIES === 'true',
  EMAIL_NOTIFICATIONS: process.env.NEXT_PUBLIC_FEATURE_EMAILS === 'true',
  COMMENTS: process.env.NEXT_PUBLIC_FEATURE_COMMENTS === 'true',
  ANALYTICS: process.env.NEXT_PUBLIC_FEATURE_ANALYTICS === 'true',
} as const;

export function isFeatureEnabled(feature: keyof typeof FEATURES): boolean {
  return FEATURES[feature] ?? false;
}
```

**Use in components:**

```typescript
import { FEATURES } from '@/lib/features';

export function ArticleForm() {
  return (
    <form>
      {/* ...existing fields... */}
      
      {FEATURES.AI_SUMMARIES && (
        <Button onClick={generateSummary}>
          Generate AI Summary
        </Button>
      )}
    </form>
  );
}
```

**Toggle in Vercel:**
1. Go to Environment Variables
2. Add: `NEXT_PUBLIC_FEATURE_AI_SUMMARIES=true`
3. Redeploy (or wait for next deploy)
4. Feature is now live!

**Benefits:**
- Enable/disable without code changes
- Test in preview first
- Gradual rollout
- Quick rollback
</details>

### Exercise 2: Add Health Check with Database

Enhance the health check to verify database connection:

```typescript
// src/app/api/health/route.ts
export async function GET() {
  // Check database connectivity
  // Return detailed status
}
```

<details>
<summary>💡 Solution</summary>

```typescript
import { db } from '@/lib/db';
import { sql } from 'drizzle-orm';
import { redis } from '@/lib/redis';

export async function GET() {
  const checks = {
    timestamp: new Date().toISOString(),
    status: 'ok',
    checks: {
      database: 'checking',
      redis: 'checking',
    },
  };
  
  // Check database
  try {
    await db.execute(sql`SELECT 1`);
    checks.checks.database = 'ok';
  } catch (error) {
    checks.checks.database = 'error';
    checks.status = 'degraded';
  }
  
  // Check Redis
  try {
    await redis.ping();
    checks.checks.redis = 'ok';
  } catch (error) {
    checks.checks.redis = 'error';
    checks.status = 'degraded';
  }
  
  const statusCode = checks.status === 'ok' ? 200 : 503;
  
  return Response.json(checks, { status: statusCode });
}
```

**Response examples:**

```json
// All good:
{
  "timestamp": "2025-12-10T10:30:00Z",
  "status": "ok",
  "checks": {
    "database": "ok",
    "redis": "ok"
  }
}

// Database down:
{
  "timestamp": "2025-12-10T10:30:00Z",
  "status": "degraded",
  "checks": {
    "database": "error",
    "redis": "ok"
  }
}
```

**Use with monitoring:**
- UptimeRobot: Check every 5 minutes
- StatusPage: Public status display
- PagerDuty: Alert on failures
</details>

### Exercise 3: Create Deployment Script

Create a pre-deployment checklist script:

```bash
# scripts/pre-deploy.sh
# Verify everything is ready before deploying
```

<details>
<summary>💡 Solution</summary>

```bash
#!/bin/bash

# scripts/pre-deploy.sh

echo "🚀 Pre-Deployment Checklist"
echo "=========================="
echo ""

# Track failures
FAILED=0

# 1. Check Git status
echo "1. Checking Git status..."
if [[ -n $(git status -s) ]]; then
  echo "   ❌ Uncommitted changes detected"
  git status -s
  FAILED=1
else
  echo "   ✅ No uncommitted changes"
fi

# 2. Run linter
echo ""
echo "2. Running linter..."
if npm run lint; then
  echo "   ✅ Linter passed"
else
  echo "   ❌ Linter failed"
  FAILED=1
fi

# 3. Type check
echo ""
echo "3. Type checking..."
if npx tsc --noEmit; then
  echo "   ✅ Type check passed"
else
  echo "   ❌ Type errors found"
  FAILED=1
fi

# 4. Run tests (if you have them)
echo ""
echo "4. Running tests..."
if npm test; then
  echo "   ✅ Tests passed"
else
  echo "   ❌ Tests failed"
  FAILED=1
fi

# 5. Check environment variables
echo ""
echo "5. Checking required env vars..."
REQUIRED_VARS=(
  "DATABASE_URL"
  "NEXT_PUBLIC_STACK_PROJECT_ID"
  "UPSTASH_REDIS_REST_URL"
)

for VAR in "${REQUIRED_VARS[@]}"; do
  if [[ -z "${!VAR}" ]]; then
    echo "   ❌ Missing: $VAR"
    FAILED=1
  else
    echo "   ✅ Found: $VAR"
  fi
done

# 6. Build test
echo ""
echo "6. Testing build..."
if npm run build; then
  echo "   ✅ Build successful"
else
  echo "   ❌ Build failed"
  FAILED=1
fi

# Summary
echo ""
echo "=========================="
if [[ $FAILED -eq 0 ]]; then
  echo "✅ All checks passed! Ready to deploy."
  exit 0
else
  echo "❌ Some checks failed. Fix issues before deploying."
  exit 1
fi
```

**Make it executable:**
```bash
chmod +x scripts/pre-deploy.sh
```

**Add to `package.json`:**
```json
{
  "scripts": {
    "predeploy": "./scripts/pre-deploy.sh",
    "deploy": "vercel --prod"
  }
}
```

**Usage:**
```bash
npm run predeploy  # Runs all checks
npm run deploy     # Only if checks pass
```
</details>

### Exercise 4: Setup Staging Environment

Create a staging environment separate from production:

<details>
<summary>💡 Solution</summary>

**Step 1: Create Staging Branch**
```bash
git checkout -b staging
git push origin staging
```

**Step 2: Configure Vercel Staging**

1. Go to Vercel dashboard
2. Project Settings → Git
3. Add production branch: `main`
4. Add staging branch: `staging`

**Step 3: Separate Environment Variables**

In Vercel, set different values per environment:

```
DATABASE_URL (Production):
└─> production-db.neon.tech

DATABASE_URL (Preview - staging branch):
└─> staging-db.neon.tech

NEXT_PUBLIC_APP_URL (Production):
└─> https://wikiapp.vercel.app

NEXT_PUBLIC_APP_URL (Preview - staging):
└─> https://wikiapp-staging.vercel.app
```

**Step 4: Workflow**

```
Development:
├─> Work in feature branch
├─> Push to GitHub
└─> Creates preview deployment

Staging:
├─> Merge to staging branch
├─> Automatic deploy to staging URL
├─> Test thoroughly
└─> Share with team

Production:
├─> Merge staging → main
├─> Automatic deploy to production
└─> Monitor closely
```

**Benefits:**
- Test in production-like environment
- Catch issues before production
- Safer deployments
- Stakeholder review
</details>

---

## Deployment Checklist

Before going live:

### Security ✅
- [ ] All API routes protected
- [ ] Environment variables set
- [ ] Rate limiting enabled
- [ ] Input validation on all forms
- [ ] HTTPS only (Vercel does this automatically)

### Performance ✅
- [ ] Images optimized
- [ ] Redis caching enabled
- [ ] Database indexed
- [ ] Analytics installed
- [ ] Speed Insights installed

### SEO ✅
- [ ] Meta tags on all pages
- [ ] robots.txt created
- [ ] sitemap.xml created
- [ ] Social media preview images

### Monitoring ✅
- [ ] Error boundary added
- [ ] Logging configured
- [ ] Health check endpoint
- [ ] Vercel alerts enabled

### Testing ✅
- [ ] Manual testing complete
- [ ] GitHub Actions working
- [ ] Preview deployments tested
- [ ] Mobile tested

---

## Common Issues & Solutions

### Issue 1: Build Fails

```
Error: Cannot find module '@/lib/db'
```

**Solution:** Check your import paths and `tsconfig.json`

### Issue 2: Environment Variables Not Working

```
Error: DATABASE_URL is undefined
```

**Solution:** 
1. Restart dev server after adding env vars
2. Redeploy on Vercel after changing env vars
3. Make sure no typos in variable names

### Issue 3: Database Connection Fails

```
Error: connect ECONNREFUSED
```

**Solution:**
1. Check DATABASE_URL is correct
2. Ensure Neon database is active
3. Verify SSL mode: `?sslmode=require`

### Issue 4: Images Not Loading

```
Error: Invalid src prop
```

**Solution:**
1. Add domain to `next.config.js`:
```typescript
module.exports = {
  images: {
    domains: ['res.cloudinary.com'],
  },
};
```

---

## Summary

You now understand:

✅ **DevOps fundamentals** - CI/CD concepts  
✅ **Git & GitHub** - Version control  
✅ **Vercel deployment** - Going live  
✅ **Environment management** - Dev/Preview/Prod  
✅ **GitHub Actions** - Automated testing  
✅ **Monitoring** - Analytics and errors  
✅ **Production best practices** - Stay reliable  

### Key Takeaways

1. **Automate everything** - CI/CD saves time
2. **Test before deploy** - GitHub Actions catches errors
3. **Use preview deployments** - Test safely
4. **Monitor production** - Know what's happening
5. **Environment variables** - Never hardcode secrets
6. **Optimize for performance** - Images, caching, CDN
7. **Think about SEO** - Meta tags, sitemap, robots.txt

---

## 🎉 Congratulations!

You've completed the entire Next.js Enterprise Wiki tutorial!

### What You Built

✅ **Production-ready wiki application**  
✅ **Modern UI** with Tailwind and shadcn  
✅ **Type-safe database** with Drizzle  
✅ **Secure authentication** with Stack Auth  
✅ **High performance** with Redis caching  
✅ **File uploads** with Cloudinary  
✅ **Email system** with Resend  
✅ **AI integration** with OpenRouter  
✅ **Full CI/CD** with GitHub Actions  
✅ **Production monitoring** with Vercel  

### What You Learned

✅ **Modern web architecture**  
✅ **Next.js App Router**  
✅ **Server Components**  
✅ **Database design**  
✅ **Authentication & security**  
✅ **Performance optimization**  
✅ **Service integration**  
✅ **DevOps practices**  

### Your Stats

📊 **Total Content:** ~200KB of deep explanations  
⏱️ **Time Invested:** 30-40 hours of learning  
🎯 **Understanding Level:** 80%+  
💪 **Confidence:** Can build production apps  

---

## Next Steps

### 1. Add Features

Practice by adding:
- Search functionality
- Categories/tags
- Comment system
- User profiles
- Bookmarks/favorites
- Article drafts
- Collaborative editing

### 2. Build More Projects

Apply your knowledge:
- Blog platform
- E-commerce store
- Dashboard with charts
- Real-time chat
- Social network
- SaaS application

### 3. Learn Advanced Topics

Deepen your knowledge:
- Advanced caching strategies
- Microservices architecture
- Kubernetes & Docker
- Advanced TypeScript
- System design
- Testing strategies

### 4. Join Communities

Connect with developers:
- [Next.js Discord](https://nextjs.org/discord)
- [Reddit r/nextjs](https://reddit.com/r/nextjs)
- [Dev.to #nextjs](https://dev.to/t/nextjs)
- Local meetups

### 5. Apply for Jobs

You're ready!
- Junior Full-Stack Developer
- Frontend Developer (React/Next.js)
- Backend Developer (Node.js)

**Use WikiApp in your portfolio!** 🚀

---

## 🧠 CHECKPOINT: Understanding Deployment

Before finishing, make sure you understand:

1. **What is DevOps?**
   - How does it differ from traditional deployment?
   - What problems does it solve?

2. **What's the difference between CI and CD?**
   - Continuous Integration vs Continuous Deployment
   - Why are both important?

3. **How do preview deployments work?**
   - What triggers them?
   - Why are they useful?

4. **What are environment variables?**
   - Why not hardcode values?
   - How do they differ per environment?

5. **What's the deployment workflow?**
   - Git push → Tests → Build → Deploy
   - What happens at each step?

**Explain the full deployment flow to someone.**

---

## 🧪 EXERCISES: Practice Deployment

### Exercise 1: Add Environment Variables

You need to add a new feature flag. Add it correctly across all environments:

```typescript
// Feature flag: NEXT_PUBLIC_ENABLE_COMMENTS
// - Development: true
// - Preview: true  
// - Production: false (not ready yet)

// Use it in your code:
export function ArticlePage() {
  const commentsEnabled = process.env.NEXT_PUBLIC_ENABLE_COMMENTS === 'true';
  
  return (
    <>
      <Article />
      {commentsEnabled && <CommentSection />}
    </>
  );
}
```

**Tasks:**
1. Add to `.env.local`
2. Add to Vercel (3 environments)
3. Test in each environment

<details>
<summary>Solution</summary>

**1. Add to `.env.local` (Development):**
```bash
# .env.local
NEXT_PUBLIC_ENABLE_COMMENTS=true
```

**2. Add to Vercel:**

Go to Project Settings → Environment Variables:

```
Variable: NEXT_PUBLIC_ENABLE_COMMENTS

Development:  true
Preview:      true
Production:   false
```

**3. Test:**

```bash
# Development
npm run dev
# Should see comments section

# Preview
git checkout -b feature/comments
git push origin feature/comments
# Create PR → Check preview URL
# Should see comments section

# Production  
# Merge to main
# Should NOT see comments section

# When ready, change Production value to "true"
```

**Important:**
- Restart dev server after changing .env.local
- Redeploy after changing Vercel env vars
- Use NEXT_PUBLIC_ prefix for client-side access
</details>

### Exercise 2: Write GitHub Actions Tests

Add tests to your CI pipeline:

```yaml
# .github/workflows/ci.yml
# Add these test steps:
# 1. Run unit tests
# 2. Run integration tests
# 3. Check test coverage
# 4. Block PR if tests fail
```

<details>
<summary>Solution</summary>

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linter
        run: npm run lint
      
      - name: Type check
        run: npx tsc --noEmit
      
      - name: Run unit tests
        run: npm test
        env:
          DATABASE_URL: postgresql://dummy@localhost/test
      
      - name: Check test coverage
        run: npm test -- --coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/coverage-final.json
      
      - name: Build
        run: npm run build
        env:
          DATABASE_URL: postgresql://dummy@localhost/test
          NEXT_PUBLIC_STACK_PROJECT_ID: test
          NEXT_PUBLIC_STACK_PUBLISHABLE_CLIENT_KEY: test
          STACK_SECRET_SERVER_KEY: test
      
      - name: Comment PR
        if: github.event_name == 'pull_request'
        uses: actions/github-script@v6
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '✅ All tests passed! Ready to merge.'
            })
```

**Add test script to package.json:**
```json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
    "test:coverage": "vitest run --coverage"
  }
}
```
</details>

### Exercise 3: Rollback a Deployment

**Scenario:** You deployed broken code to production. How do you rollback?

**Tasks:**
1. Find the last working deployment
2. Rollback using Vercel dashboard
3. Verify the fix

<details>
<summary>Solution</summary>

**Option 1: Vercel Dashboard (Fastest)**

1. Go to Vercel → Your Project → Deployments
2. Find last successful deployment (green checkmark)
3. Click the "..." menu → "Promote to Production"
4. Confirm → Instant rollback! ⚡

**Option 2: Git Revert (More permanent)**

```bash
# Find the bad commit
git log --oneline

# Revert it
git revert <bad-commit-hash>

# Push
git push origin main

# Vercel auto-deploys the revert
```

**Option 3: Redeploy Old Version**

```bash
# Check out old working commit
git checkout <good-commit-hash>

# Force push to main (careful!)
git push origin HEAD:main --force

# Or create new branch
git checkout -b hotfix/rollback
git push origin hotfix/rollback
# Then merge via PR
```

**Verify:**
1. Check production URL
2. Test critical features
3. Monitor error logs
4. Notify team

**Prevention:**
- ✅ Always test in preview first
- ✅ Use feature flags for big changes
- ✅ Deploy during low-traffic hours
- ✅ Have rollback plan ready
</details>

### Exercise 4: Set Up Monitoring

Configure monitoring for your production app:

**Tasks:**
1. Set up error tracking
2. Configure uptime monitoring
3. Create alert rules
4. Test alerts

<details>
<summary>Solution</summary>

**1. Error Tracking with Vercel**

```typescript
// src/app/error.tsx
'use client';

import { useEffect } from 'react';

export default function Error({
  error,
}: {
  error: Error & { digest?: string };
}) {
  useEffect(() => {
    // Log to Vercel (automatically tracked)
    console.error('Application error:', {
      message: error.message,
      stack: error.stack,
      digest: error.digest,
      timestamp: new Date().toISOString(),
    });
    
    // Optional: Send to external service
    // Sentry, LogRocket, etc.
  }, [error]);
  
  return (
    <div className="flex flex-col items-center justify-center min-h-screen">
      <h2>Something went wrong!</h2>
      <p>Error ID: {error.digest}</p>
    </div>
  );
}
```

**2. Uptime Monitoring**

Use [UptimeRobot](https://uptimerobot.com) (free):

1. Sign up (free plan: 50 monitors)
2. Add monitor:
   - Type: HTTPS
   - URL: https://wikiapp.vercel.app
   - Interval: 5 minutes
3. Add alert contacts (email, SMS, Slack)

**3. Health Check Endpoint**

```typescript
// src/app/api/health/route.ts
import { db } from '@/lib/db';
import { redis } from '@/lib/redis';

export async function GET() {
  const checks = {
    timestamp: new Date().toISOString(),
    status: 'healthy',
    services: {
      database: 'unknown',
      redis: 'unknown',
    },
  };
  
  try {
    // Check database
    await db.execute('SELECT 1');
    checks.services.database = 'healthy';
  } catch (error) {
    checks.services.database = 'unhealthy';
    checks.status = 'degraded';
  }
  
  try {
    // Check Redis
    await redis.ping();
    checks.services.redis = 'healthy';
  } catch (error) {
    checks.services.redis = 'unhealthy';
    checks.status = 'degraded';
  }
  
  const statusCode = checks.status === 'healthy' ? 200 : 503;
  
  return Response.json(checks, { status: statusCode });
}
```

**4. Vercel Alerts**

Go to Project Settings → Notifications:

```
Alert Type: Deployment Failed
Notify: Your Email
Integrations: Slack (optional)

Alert Type: Performance Degradation
Threshold: 95th percentile > 3s
Notify: Your Email
```

**5. Test Alerts**

```bash
# Trigger error
curl https://wikiapp.vercel.app/api/trigger-error

# Check health
curl https://wikiapp.vercel.app/api/health

# Should receive alerts via email/Slack
```
</details>

### Exercise 5: Production Debugging

**Scenario:** Users report slow page loads. How do you debug?

Write your debugging approach.

<details>
<summary>Debugging Steps</summary>

**Step 1: Check Vercel Analytics**
```
1. Go to Vercel → Analytics
2. Look at:
   - Page load times (p95, p99)
   - Slowest pages
   - Geographic distribution
   - Device breakdown
```

**Step 2: Check Vercel Logs**
```
1. Go to Vercel → Logs
2. Filter by:
   - Time range
   - Status codes (look for 500s)
   - Slow requests (>1s)
3. Look for patterns
```

**Step 3: Check Database**
```typescript
// Add query logging
import { db } from '@/lib/db';

const start = Date.now();
const results = await db.query.articles.findMany();
const duration = Date.now() - start;

if (duration > 1000) {
  console.warn(`Slow query: ${duration}ms`);
}
```

**Step 4: Check Redis**
```typescript
// Check cache hit rate
const cacheHits = await redis.get('cache:hits');
const cacheMisses = await redis.get('cache:misses');
const hitRate = cacheHits / (cacheHits + cacheMisses);

console.log(`Cache hit rate: ${hitRate * 100}%`);
// Aim for >70%
```

**Step 5: Check Bundle Size**
```bash
# Analyze bundle
npm run build
# Check .next/static/ sizes

# Use Bundle Analyzer
npm install @next/bundle-analyzer
```

**Step 6: Common Issues & Fixes**

```typescript
// Issue 1: N+1 Query
// ❌ Bad
for (const article of articles) {
  const author = await db.query.users.findFirst({
    where: eq(users.id, article.authorId),
  });
}

// ✅ Good
const articles = await db.query.articles.findMany({
  with: { author: true },  // Single query
});

// Issue 2: Missing Caching
// ❌ Bad
const articles = await db.query.articles.findMany();

// ✅ Good
const cached = await redis.get('articles');
if (cached) return JSON.parse(cached);
const articles = await db.query.articles.findMany();
await redis.setex('articles', 60, JSON.stringify(articles));

// Issue 3: Large Images
// ❌ Bad
<img src={largeImage} />

// ✅ Good
<Image
  src={largeImage}
  width={1200}
  height={600}
  quality={75}
  loading="lazy"
/>

// Issue 4: Too Much JavaScript
// ❌ Bad
'use client';  // Everything is client component

// ✅ Good
// Server Component by default
// 'use client' only when needed
```
</details>

---

## Final Thoughts

**You did it!** 🎊

Building real applications is the best way to learn. You didn't just copy-paste code - you understand WHY things work this way.

This knowledge will serve you for years. The concepts you learned (databases, caching, authentication, deployment) apply to ANY web framework, not just Next.js.

**Keep building. Keep learning. Keep shipping.** 

The difference between a good developer and a great developer is not talent - it's persistence. You've shown that persistence by completing this guide.

Now go build something amazing! 🚀

---

[← Back to Index](./README.md)

**Thank you for completing this tutorial!** 
**Good luck with your development journey!** 💙
