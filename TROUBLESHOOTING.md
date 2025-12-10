# Troubleshooting Guide
## Common Issues & Solutions

> **Quick help for when things go wrong**

[← Back to Index](./README.md)

---

## Quick Reference

| Issue | Quick Fix |
|-------|-----------|
| Server won't start | `lsof -ti:3000 \| xargs kill -9` |
| Module not found | Restart server, check tsconfig.json paths |
| Database connection fails | Check DATABASE_URL, add ?sslmode=require |
| Auth not working | Check all 3 Stack Auth env vars set |
| Redis fails | Use REST API (not TCP), check Upstash creds |
| Build fails | Run `npm run build` locally first |
| Stale cache | Call `redis.del(key)` on update |
| Images not loading | Add domain to next.config.js |

---

## Development Issues

### Server Won't Start
**Error:** `EADDRINUSE: address already in use`  
**Fix:** `lsof -ti:3000 | xargs kill -9 && npm run dev`

### Module Not Found
**Error:** `Cannot find module '@/lib/db'`  
**Fix:** Check tsconfig.json paths, restart server

### TypeScript Errors
**Error:** `Type 'undefined' is not assignable`  
**Fix:** Add `!` or default value: `process.env.VAR!` or `|| 'default'`

### Env Vars Not Working
**Fix:** Restart server after changing .env.local

---

## Database Issues

### Can't Connect
**Error:** `ECONNREFUSED`  
**Fix:** Add `?sslmode=require` to DATABASE_URL

### Migration Fails  
**Error:** `relation already exists`  
**Fix:** Delete migration file, regenerate with `npm run db:generate`

### Query Returns Undefined
**Fix:** Always check: `if (!result) throw new Error('Not found')`

---

## Auth Issues

### User Always Null
**Fix:** Check all 3 env vars set, StackProvider wraps app, use 'use client' with useUser()

### Redirects Not Working
**Fix:** Import from 'next/navigation' not 'next/router'

---

## Caching Issues

### Redis Connection Failed
**Fix:** Use @upstash/redis (REST API), not ioredis (TCP)

### Stale Data
**Fix:** Invalidate cache on update: `await redis.del(key)`

---

## File Upload Issues

### Upload Fails
**Fix:** Check all 3 Cloudinary env vars, verify file size < 5MB

### Images Not Displaying
**Fix:** Add 'res.cloudinary.com' to next.config.js images.domains

---

## Deployment Issues

### Build Fails
**Fix:** Run `npm run build` locally, check build logs in Vercel

### 500 Error in Production
**Fix:** Check Vercel logs, verify all env vars set, check database connection

### Preview Not Created
**Fix:** Check GitHub integration, manually trigger with empty commit

---

## General Debugging

**Always:**
1. Read error message carefully
2. Add console.logs to track execution
3. Check basics (server running? env vars set?)
4. Test locally before debugging production
5. Google the exact error message

**Resources:**
- [Next.js Discord](https://nextjs.org/discord)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/next.js)
- Tutorial's full troubleshooting guide (see above)

---

For detailed solutions, see the complete TROUBLESHOOTING-FULL.md guide.
