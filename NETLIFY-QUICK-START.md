# Netlify Deployment Quick Start Checklist

## Pre-Deployment Setup (5-10 minutes)

### 1. Prepare Git Repository
- [ ] Initialize git: `git init`
- [ ] Add files: `git add .`
- [ ] Create initial commit: `git commit -m "Initial commit"`
- [ ] Create `.gitignore` (already provided)
- [ ] Ensure `.env` is in `.gitignore` (don't commit secrets)

### 2. Push to GitHub
- [ ] Create GitHub account: https://github.com
- [ ] Create new repository
- [ ] Add remote: `git remote add origin https://github.com/USERNAME/REPO.git`
- [ ] Push code: `git push -u origin main`

### 3. Prepare Production Database
- [ ] Create PostgreSQL database (not SQLite!)
  - [ ] Railway: https://railway.app (free tier available)
  - [ ] Supabase: https://supabase.com (PostgreSQL + Auth)
  - [ ] AWS RDS / Digital Ocean / etc.
- [ ] Copy PostgreSQL connection string (DATABASE_URL format)
- [ ] Test connection locally (optional)

### 4. Get API Keys

#### Google Maps (Required for location features)
- [ ] Go to https://console.cloud.google.com
- [ ] Create new project
- [ ] Enable APIs:
  - [ ] Maps JavaScript API
  - [ ] Places API
  - [ ] Directions API
- [ ] Create API Key
- [ ] Restrict to your domain

#### Gemini AI (For itinerary generation)
- [ ] Go to https://ai.google.dev
- [ ] Sign in with Google account
- [ ] Create API Key
- [ ] Copy key

#### Groq API (Optional, for AI)
- [ ] Go to https://groq.com
- [ ] Sign up
- [ ] Create API Key (optional)

#### JWT Secret (Create custom)
- [ ] Generate: `openssl rand -base64 32`
- [ ] Or use online tool: https://www.lastpass.com/password-generator
- [ ] Copy the generated secret

## Netlify Deployment (10-15 minutes)

### 5. Create Netlify Account
- [ ] Go to https://netlify.com
- [ ] Sign up with GitHub account
- [ ] Authorize Netlify

### 6. Deploy Site
- [ ] Click "Add new site"
- [ ] Choose "Import an existing project"
- [ ] Select GitHub
- [ ] Authorize and select your repository
- [ ] Build settings:
  - [ ] Build command: `npm run build`
  - [ ] Publish directory: `.next`
- [ ] Click "Deploy site"
- [ ] Wait for build to complete (~5 minutes)

### 7. Add Environment Variables
In Netlify Dashboard → Site settings → Build & deploy → Environment:

- [ ] `DATABASE_URL` = your PostgreSQL connection string
- [ ] `JWT_SECRET` = your generated secret (32+ chars)
- [ ] `NEXT_PUBLIC_APP_URL` = your-site.netlify.app (or custom domain)
- [ ] `GOOGLE_MAPS_API_KEY` = your API key
- [ ] `GEMINI_API_KEY` = your API key
- [ ] `GROQ_API_KEY` = your API key (optional)

**Important:** After adding variables, trigger a redeploy:
- [ ] Click "Trigger deploy" → "Deploy site"

### 8. Enable Custom Domain (Optional)
- [ ] Site settings → Domain management
- [ ] Add custom domain
- [ ] Follow DNS setup instructions
- [ ] Wait for SSL certificate (automatic)

## Post-Deployment Testing (5-10 minutes)

### 9. Test Core Features
- [ ] Visit deployed URL
- [ ] Test user signup/login
- [ ] Test destination search
- [ ] Search for a place
- [ ] View place details
- [ ] Try AI itinerary generation
- [ ] Check console for errors (F12)
- [ ] Test on mobile device

### 10. Monitor & Optimize
- [ ] Check Netlify deployment logs
- [ ] Enable Netlify Analytics (optional)
- [ ] Set up error monitoring (optional)
- [ ] Configure cache headers (netlify.toml provided)
- [ ] Test SSL certificate

## Database Setup (One-time after deployment)

After first successful deployment:
```bash
# Run migrations on production database
npx prisma migrate deploy

# Or seed initial data if needed
npx prisma db seed
```

## Continuous Deployment

From now on:
- [ ] Push code to main branch: `git push origin main`
- [ ] Netlify automatically rebuilds and redeploys
- [ ] Check deployment status in Netlify dashboard

## Troubleshooting Quick Links

| Issue | Solution |
|-------|----------|
| Build fails | Check logs in Netlify dashboard |
| Env vars not working | Redeploy after adding variables |
| Database errors | Verify PostgreSQL connection string |
| Maps not showing | Check Google Maps API key |
| AI features not working | Verify Gemini/Groq API keys |
| Images broken | Ensure image URLs are correct |

## Support Resources

- **Netlify Docs:** https://docs.netlify.com
- **Next.js Deployment:** https://nextjs.org/docs/deployment
- **Documentation:** See other .md files in project
- **Project Guides:**
  - API Integration: [API_INTEGRATION_GUIDE.md](API_INTEGRATION_GUIDE.md)
  - Authentication: [AUTHENTICATION-GUIDE.md](AUTHENTICATION-GUIDE.md)
  - Google Maps: [GOOGLE-MAPS-INTEGRATION.md](GOOGLE-MAPS-INTEGRATION.md)
  - Real-time Features: [REALTIME-IMPLEMENTATION.md](REALTIME-IMPLEMENTATION.md)

## What's Next?

After successful deployment:
1. **Share your app URL** with users
2. **Set up custom domain** for branding
3. **Enable analytics** to track usage
4. **Configure email service** for notifications
5. **Add file upload** for user profiles
6. **Monitor performance** and optimize

---

**Estimated total time: 30-45 minutes from start to live deployment**

For detailed instructions, see [NETLIFY-DEPLOYMENT.md](NETLIFY-DEPLOYMENT.md) and [PRODUCTION-ENV.md](PRODUCTION-ENV.md)
