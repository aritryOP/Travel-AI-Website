# Netlify Deployment Guide for X Travel App

## Prerequisites

1. **GitHub Account** - Your project must be on GitHub
2. **Netlify Account** - Sign up at https://netlify.com
3. **Git** - Make sure Git is installed and configured

## Step 1: Prepare Your Local Repository

```bash
# Initialize git (if not already done)
git init

# Add all files
git add .

# Commit your changes
git commit -m "Initial commit: Ready for Netlify deployment"
```

## Step 2: Push to GitHub

1. Create a new repository on GitHub (https://github.com/new)
2. Push your local code:

```bash
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
git branch -M main
git push -u origin main
```

## Step 3: Deploy to Netlify

### Option A: Using Netlify Dashboard (Recommended)

1. Go to https://app.netlify.com
2. Click **"Add new site"** → **"Import an existing project"**
3. Select **"GitHub"** and authorize Netlify to access your repositories
4. Select your travel app repository
5. Configure build settings:
   - **Build command:** `npm run build`
   - **Publish directory:** `.next`
   - **Node version:** 18 or higher (set in netlify.toml or environment variables)
6. Click **"Deploy site"**

### Option B: Using Netlify CLI

```bash
# Install Netlify CLI globally
npm install -g netlify-cli

# Login to Netlify
netlify login

# Deploy from project directory
netlify deploy --prod
```

## Step 4: Configure Environment Variables

After deployment starts, navigate to your site settings:

1. Go to **Site settings** → **Build & deploy** → **Environment**
2. Add the following environment variables:

```
DATABASE_URL = your-production-database-url
JWT_SECRET = your-production-jwt-secret (min 32 characters)
NEXT_PUBLIC_APP_URL = your-netlify-domain.netlify.app
GOOGLE_MAPS_API_KEY = your-google-maps-key
GEMINI_API_KEY = your-gemini-api-key
GROQ_API_KEY = your-groq-api-key
```

### Required Environment Variables:
- `DATABASE_URL` - PostgreSQL connection string (for production, not SQLite)
- `JWT_SECRET` - Secure random string (minimum 32 characters)
- `NEXT_PUBLIC_APP_URL` - Your deployed app URL
- `GOOGLE_MAPS_API_KEY` - From Google Cloud Console
- `GEMINI_API_KEY` - From Google AI Studio
- `GROQ_API_KEY` - From Groq (if using AI features)

## Step 5: Update Database for Production

⚠️ **IMPORTANT:** SQLite won't work reliably on Netlify's serverless environment.

Switch to PostgreSQL:

1. Create a PostgreSQL database (e.g., on Railway, Supabase, or AWS RDS)
2. Update `DATABASE_URL` environment variable with your PostgreSQL connection string
3. Run migrations:
   ```bash
   npx prisma migrate deploy
   ```

## Step 6: Verify Deployment

1. Check the deployment logs in Netlify dashboard
2. Visit your site URL
3. Test key features:
   - User registration/login
   - Searching destinations
   - Viewing place details
   - AI itinerary generation

## Post-Deployment Tasks

### 1. Set Up Custom Domain (Optional)
- Go to **Site settings** → **Domain management**
- Add your custom domain

### 2. Enable HTTPS (Automatic)
- Netlify automatically provisions SSL certificates

### 3. Set Up Continuous Deployment
- Any push to your main branch automatically triggers a new deployment

### 4. Configure Redirects
- The `netlify.toml` file is already configured for SPA routing

## Troubleshooting

### Build Fails
- Check build logs in Netlify dashboard
- Ensure all dependencies are in package.json
- Verify Node version compatibility

### Environment Variables Not Found
- Confirm variables are set in Site settings
- Redeploy after adding variables
- Use `NEXT_PUBLIC_` prefix for client-side variables

### Database Connection Issues
- Verify PostgreSQL connection string format
- Ensure database is accessible from Netlify
- Check database firewall/security groups

### Images Not Loading
- Verify `next.config.mjs` has `unoptimized: true`
- Check image paths in components

## Performance Optimization Tips

1. **Enable Netlify Analytics** - Monitor traffic and errors
2. **Set up Netlify Functions** - For backend API endpoints
3. **Use Netlify Edge Functions** - For edge caching
4. **Configure Caching** - Adjust headers in netlify.toml

## Support

- **Netlify Docs:** https://docs.netlify.com
- **Next.js Deployment:** https://nextjs.org/docs/app/building-your-application/deploying
- **Project Documentation:** See other .md files in the root directory

## Files Configuration

- `netlify.toml` - Netlify build and deployment configuration
- `.gitignore` - Files to exclude from Git
- `package.json` - Build scripts and dependencies
- `next.config.mjs` - Next.js configuration for production builds
