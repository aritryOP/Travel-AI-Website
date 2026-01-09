# Netlify Deployment Troubleshooting Guide

Quick solutions for common issues during and after Netlify deployment.

---

## 🔧 Pre-Deployment Issues

### Issue: "Build command not found"
**Cause:** Missing or incorrect `npm` script  
**Solution:**
```bash
# Verify package.json has build script
cat package.json | grep -A 5 "scripts"

# Should show: "build": "next build"
# Fix: Update netlify.toml build command to match
```

### Issue: ".next directory not created"
**Cause:** Build failing silently  
**Solution:**
1. Check Netlify build logs for errors
2. Run locally: `npm run build`
3. Check for TypeScript errors (next.config allows these)
4. Ensure all dependencies installed

### Issue: "Cannot find module" during build
**Cause:** Missing dependency  
**Solution:**
```bash
npm install missing-package
git add package.json package-lock.json
git push origin main
```

---

## 🌐 Deployment Issues

### Issue: "Build succeeded but site shows 404"
**Cause:** Wrong publish directory  
**Solution:**
1. Check netlify.toml: `publish = ".next"`
2. Verify `.next` folder exists locally
3. Redeploy after fixing

### Issue: "Deployment stuck or timeout"
**Cause:** Long build time or process hanging  
**Solution:**
```bash
# Check build logs in Netlify dashboard
# Optimization:
# 1. Remove unused dependencies
# 2. Enable incremental static regeneration
# 3. Reduce bundle size
# 4. Check for infinite loops in build

# Manual trigger redeploy:
# Netlify Dashboard → Deploys → Trigger Deploy
```

### Issue: "CORS errors after deployment"
**Cause:** API calls blocked by CORS policy  
**Solution:**
```javascript
// Add to your API routes or middleware
res.setHeader('Access-Control-Allow-Origin', '*');
res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
res.setHeader('Access-Control-Allow-Headers', 'Content-Type, Authorization');
```

---

## 🔑 Environment Variable Issues

### Issue: "Environment variables not working"
**Cause:** Variables not redeployed  
**Solution:**
1. Add variable in Netlify dashboard
2. **Critical:** Click "Trigger deploy" → "Deploy site"
3. Wait for build to complete
4. Check variable: `console.log(process.env.VAR_NAME)`

### Issue: "NEXT_PUBLIC_ variables undefined"
**Cause:** Not prefixed with `NEXT_PUBLIC_` for client-side  
**Solution:**
```javascript
// Client-side only
const appUrl = process.env.NEXT_PUBLIC_APP_URL;  // ✅ Works
const secret = process.env.JWT_SECRET;           // ❌ Undefined

// Use in server actions only
export async function getSecret() {
  return process.env.JWT_SECRET;                 // ✅ Works
}
```

### Issue: "DATABASE_URL connection refused"
**Cause:** Database URL invalid or firewall blocking  
**Solution:**
```bash
# 1. Verify connection string format
# postgresql://user:password@host:port/dbname

# 2. Test locally first
npx prisma db push

# 3. Add Netlify IP to database firewall
# Check: https://docs.netlify.com/reference/runtime-variables/

# 4. Use connection pooling for serverless
# DATABASE_URL with pooler endpoint
```

---

## 📱 Runtime Issues

### Issue: "404 errors on page refresh"
**Cause:** SPA routing not configured  
**Solution:**
netlify.toml already has:
```toml
[[redirects]]
from = "/*"
to = "/index.html"
status = 200
```
If not working:
1. Verify netlify.toml exists
2. Check syntax is correct
3. Redeploy

### Issue: "Images not loading"
**Cause:** Image optimization issues or wrong paths  
**Solution:**
```javascript
// next.config.mjs has unoptimized: true (good for Netlify)
// Check:
// 1. Image paths are relative or absolute
// 2. External URLs are whitelisted
// 3. Image formats are supported
// 4. Check browser console for specific errors

import Image from 'next/image';
export default function MyImage() {
  return (
    <Image 
      src="/images/photo.jpg"
      alt="Description"
      width={300}
      height={200}
    />
  );
}
```

### Issue: "Login/authentication not working"
**Cause:** JWT_SECRET not set or session issues  
**Solution:**
1. Verify `JWT_SECRET` is set in environment
2. Redeploy after adding it
3. Clear browser cookies
4. Check localStorage for tokens
5. Verify token format in requests

### Issue: "Maps not showing"
**Cause:** Google Maps API key invalid or not set  
**Solution:**
```bash
# 1. Verify GOOGLE_MAPS_API_KEY in environment
# 2. Check key is not restricted to localhost
# 3. Enable required APIs:
#    - Maps JavaScript API
#    - Places API
#    - Directions API
# 4. Verify key works: curl https://maps.googleapis.com/maps/api/js?key=YOUR_KEY
# 5. Redeploy after fixing
```

### Issue: "AI features not responding"
**Cause:** API keys not set or quota exceeded  
**Solution:**
```bash
# 1. Verify GEMINI_API_KEY and GROQ_API_KEY
# 2. Check API quotas in service dashboards
# 3. Verify keys haven't expired
# 4. Check rate limiting
# 5. Monitor function logs in Netlify
# 6. Test locally first: npm run dev
```

---

## ⚡ Performance Issues

### Issue: "Build takes too long (>10 min)"
**Cause:** Large dependencies or inefficient config  
**Solution:**
```bash
# 1. Analyze bundle
npm run build -- --debug

# 2. Remove unused dependencies
npm list  # Check for unused packages

# 3. Consider monorepo if needed

# 4. Enable cached dependencies
# Netlify automatically caches node_modules

# 5. Check next.config.mjs for heavy operations
```

### Issue: "Site slow to load"
**Cause:** Large bundle or CDN issues  
**Solution:**
```bash
# 1. Run Lighthouse audit
# Browser DevTools → Lighthouse → Generate Report

# 2. Check build size
npm run build
du -sh .next/

# 3. Enable caching headers (in netlify.toml)
# Already configured for optimal caching

# 4. Enable Netlify analytics
# Dashboard → Site settings → Analytics
```

### Issue: "Function timeout (>26s)"
**Cause:** Long-running operations in API routes  
**Solution:**
```javascript
// Break into smaller chunks
export async function GET(request) {
  // Netlify limit: 26 seconds (can be extended)
  const result = await quickOperation();
  return Response.json(result);
}

// For long operations, queue as background job
// Use external service: Bull, AWS SQS, etc.
```

---

## 🔍 Debugging

### Enable Detailed Logs
```bash
# Deploy with verbose output
netlify deploy --prod --debug

# View logs in dashboard
# Netlify → Site → Deploys → [Deployment] → Logs
```

### Test Locally Before Deploying
```bash
# Build locally
npm run build

# Start production server
npm run start

# Test at http://localhost:3000
# Check for errors in terminal
```

### Use Netlify Functions Logs
```bash
# View real-time function logs
netlify functions:invoke function-name

# Check performance
netlify functions:list
```

---

## 🛠️ Common Solutions

### Solution 1: Full Redeploy
```bash
# Sometimes a clean deploy fixes issues
# Netlify Dashboard → Deploys → Trigger deploy
# Or from CLI:
netlify deploy --prod --trigger
```

### Solution 2: Clear Cache
```bash
# If static files cached incorrectly
# Dashboard → Site settings → Build & deploy → Clear cache and rebuild
```

### Solution 3: Environment Reset
1. Remove all environment variables
2. Add them back one by one
3. Test after each addition
4. Redeploy after changes

### Solution 4: Rollback Deployment
```bash
# Revert to previous working version
# Netlify Dashboard → Deploys → [Previous] → Restore
```

---

## 📋 Pre-Flight Checklist

Before asking for help, verify:

- [ ] Build works locally: `npm run build`
- [ ] Start works locally: `npm start`
- [ ] All env vars added in Netlify dashboard
- [ ] Redeployed after adding env vars
- [ ] Git repository is public/accessible
- [ ] Latest code pushed to main branch
- [ ] No sensitive data in repository
- [ ] package.json has all dependencies
- [ ] netlify.toml exists and is correct
- [ ] Logs checked in Netlify dashboard

---

## 🆘 Getting Help

### Check These First
1. **Netlify Build Logs** - Most detailed information
   - Dashboard → Deploys → [Deployment] → Logs
2. **Browser Console** - Client-side errors (F12)
3. **Network Tab** - Failed requests (F12 → Network)
4. **Local Testing** - Run `npm run dev` locally

### Resources
- **Netlify Support:** https://support.netlify.com
- **Netlify Docs:** https://docs.netlify.com
- **Next.js Troubleshooting:** https://nextjs.org/docs/basic-features/debugging
- **GitHub Issues:** Check project issues

### Emergency Contacts
- **Netlify Support Chat** - In-app chat
- **Netlify Status Page** - Check for outages
- **Community Forums** - Stack Overflow, GitHub Discussions

---

## 📞 Getting Support

### Include in Support Request
- Deployment ID (found in URL)
- Exact error message
- Screenshot of error
- What you've tried
- Steps to reproduce

### Useful Commands for Support
```bash
# Get build info
netlify status

# Get environment info
netlify env:list

# Get deployment info
netlify deploy --list
```

---

## 🎓 Learning Resources

- **Netlify University:** https://www.netlify.com/blog/
- **Next.js Docs:** https://nextjs.org/docs
- **Prisma Docs:** https://www.prisma.io/docs/
- **Web Dev Docs:** https://developer.mozilla.org/

---

## 📝 Issue Log Template

When documenting issues:

```markdown
### Issue: [Title]

**Symptom:** [What's happening]
**Expected:** [What should happen]
**Cause:** [Why it's happening]
**Solution:** [How to fix]

**Step to Reproduce:**
1. ...
2. ...
3. ...

**Environment:**
- Node version: [version]
- Database: [type]
- Netlify function: [yes/no]

**Screenshots/Logs:**
[Paste relevant error messages]
```

---

## ✅ Success Verification

After deployment, confirm:
- ✅ Site loads without errors
- ✅ User registration works
- ✅ User login works  
- ✅ Search functionality works
- ✅ Maps display correctly
- ✅ AI features respond
- ✅ Images load properly
- ✅ Mobile responsive
- ✅ No console errors
- ✅ HTTPS working

**All green? 🎉 Deployment successful!**

---

For more help, see the main deployment guides in the project.
