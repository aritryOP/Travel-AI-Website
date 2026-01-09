# Production Environment Configuration

This file documents all environment variables needed for production deployment on Netlify.

## Environment Variables Setup

### In Netlify Dashboard:
1. Go to **Site settings** → **Build & deploy** → **Environment**
2. Add each variable as **Key** and **Value**
3. Redeploy after adding variables

## Required Variables for Production

### Database (CRITICAL - SQLite won't work)
```
DATABASE_URL=postgresql://user:password@host:5432/travel_app_db
```
- Must use PostgreSQL for Netlify production
- Get from: Railway, Supabase, AWS RDS, or similar
- Example format: `postgresql://username:password@db.example.com:5432/dbname`

### Authentication
```
JWT_SECRET=your-very-long-secure-random-string-minimum-32-characters-change-this
```
- Generate with: `openssl rand -base64 32`
- Minimum 32 characters
- Keep secure - never commit to repository

### Application URL
```
NEXT_PUBLIC_APP_URL=https://your-domain.netlify.app
```
or if using custom domain:
```
NEXT_PUBLIC_APP_URL=https://your-custom-domain.com
```

### Google Services
```
GOOGLE_MAPS_API_KEY=your-google-maps-api-key
```
- Get from: https://console.cloud.google.com
- Enable: Maps JavaScript API, Places API, Directions API
- Restrict to your domain

### AI Services

#### Gemini API
```
GEMINI_API_KEY=your-gemini-api-key
```
- Get from: https://ai.google.dev
- Used for itinerary generation
- Free tier available

#### Groq API (Optional)
```
GROQ_API_KEY=your-groq-api-key
```
- Get from: https://groq.com
- Alternative AI provider
- Optional, used if available

### Email Service (Optional)
```
EMAIL_FROM=noreply@your-domain.com
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-app-specific-password
```
- Used for email verification and password reset
- For Gmail: Use App Password (not regular password)

### File Upload (Optional)
```
CLOUDINARY_CLOUD_NAME=your-cloud-name
CLOUDINARY_API_KEY=your-api-key
CLOUDINARY_API_SECRET=your-api-secret
```
- Get from: https://cloudinary.com
- Used for profile pictures and uploads
- Optional if not using file upload features

### Analytics (Optional)
```
NEXT_PUBLIC_ANALYTICS_ID=your-analytics-id
```
- For tracking and monitoring

## Environment Variables by Feature

### Core Features (Required)
- `DATABASE_URL`
- `JWT_SECRET`
- `NEXT_PUBLIC_APP_URL`

### Maps & Location (Highly Recommended)
- `GOOGLE_MAPS_API_KEY`

### AI Features (Recommended)
- `GEMINI_API_KEY` (for AI itineraries)
- `GROQ_API_KEY` (alternative AI)

### Authentication Features (Optional)
- `EMAIL_FROM`, `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS`

### File Uploads (Optional)
- `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY`, `CLOUDINARY_API_SECRET`

## How to Add Variables in Netlify

1. **Via Dashboard:**
   - Site settings → Environment variables
   - Click "Add"
   - Enter key and value
   - Click "Save"
   - Redeploy

2. **Via netlify.toml:**
   ```toml
   [context.production.environment]
   DATABASE_URL = "postgresql://..."
   JWT_SECRET = "..."
   NEXT_PUBLIC_APP_URL = "https://..."
   ```

3. **Via Netlify CLI:**
   ```bash
   netlify env:set DATABASE_URL "postgresql://..."
   ```

## Important Notes

⚠️ **DO NOT:**
- Commit `.env` files to Git
- Share your API keys
- Use development database URLs in production
- Use simple JWT secrets

✅ **DO:**
- Use strong, random secrets (minimum 32 characters)
- Keep sensitive variables in Netlify environment only
- Rotate keys periodically
- Use NEXT_PUBLIC_ prefix only for client-safe variables
- Test each API key before deploying

## Verification Checklist

After adding all variables:

- [ ] Database connects successfully
- [ ] Login/signup works
- [ ] Place search works (Google Maps)
- [ ] Itinerary generation works (AI APIs)
- [ ] Images load correctly
- [ ] No 401/403 errors in console
- [ ] Analytics tracking (if enabled)

## Troubleshooting

### Variables not appearing in app
- Redeploy after adding variables
- Check variable names match exactly (case-sensitive)
- Use `NEXT_PUBLIC_` prefix for client-side variables

### Database connection failed
- Verify PostgreSQL string format
- Check firewall allows Netlify IPs
- Test connection from Netlify CLI: `netlify env:list`

### API Keys invalid
- Verify key hasn't expired
- Check key restrictions match your domain
- Regenerate keys if needed

## Support Resources

- Netlify Environment: https://docs.netlify.com/configure-builds/environment/
- Google Cloud Console: https://console.cloud.google.com
- Google AI Studio: https://ai.google.dev
- Groq: https://groq.com
