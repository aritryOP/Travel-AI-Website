# Netlify Runtime Configuration

This file contains Netlify-specific runtime configurations and edge function setup for optimal performance.

## Netlify Edge Functions (Optional Performance Enhancement)

To enable edge functions for better performance, create `netlify/edge-functions/`:

```javascript
// netlify/edge-functions/redirects.js
export default async (request, context) => {
  const url = new URL(request.url);
  
  // Redirect HTTP to HTTPS
  if (url.protocol === 'http:') {
    return Response.redirect(url.href.replace('http://', 'https://'), 301);
  }
  
  return context.next();
};
```

## Netlify Functions (Serverless Backend)

Move API routes to Netlify Functions for better serverless integration:

```javascript
// netlify/functions/api-proxy.js
export async function handler(event, context) {
  const { path } = event;
  const apiUrl = `${process.env.API_URL}${path}`;
  
  try {
    const response = await fetch(apiUrl, {
      method: event.httpMethod,
      headers: event.headers,
      body: event.body,
    });
    
    return {
      statusCode: response.status,
      body: await response.text(),
    };
  } catch (error) {
    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message }),
    };
  }
}
```

## Cache Configuration

The `netlify.toml` includes optimized caching:

```toml
[[headers]]
for = "/images/*"
[headers.values]
  Cache-Control = "public, max-age=31536000, immutable"

[[headers]]
for = "/*.js"
[headers.values]
  Cache-Control = "public, max-age=31536000, immutable"

[[headers]]
for = "/*.css"
[headers.values]
  Cache-Control = "public, max-age=31536000, immutable"

[[headers]]
for = "/"
[headers.values]
  Cache-Control = "public, max-age=0, must-revalidate"
```

## Redirects for SPA

```toml
[[redirects]]
from = "/*"
to = "/index.html"
status = 200
conditions = {Language = ["en"], Country = ["US"]}
```

## Environment-Specific Configuration

```toml
[context.production]
command = "npm run build"
environment = { NODE_ENV = "production" }

[context.deploy-preview]
command = "npm run build"

[context.branch-deploy]
command = "npm run build"
```

## Serverless Function Timeout

```toml
[functions]
node_bundler = "esbuild"

[[edge_functions]]
path = "/api/*"
function = "api-handler"
```

## Performance Monitoring

Add to `package.json`:
```json
{
  "scripts": {
    "lighthouse": "lighthouse https://your-site.netlify.app --output-path ./lighthouse"
  }
}
```

## Build Optimizations

The `next.config.mjs` is already optimized:
- `unoptimized: true` for images (Netlify handles optimization)
- TypeScript errors ignored (set during build)
- SWC compilation (faster builds)

## Database Connection Pooling

For PostgreSQL on Netlify, consider using PgBouncer or Prisma's connection pooling:

```prisma
// prisma/schema.prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
  directUrl = env("DIRECT_URL")  // Direct connection for migrations
}
```

Add to Netlify environment:
```
DATABASE_URL="postgresql://user:pass@pooler-host:5432/db?schema=public"
DIRECT_URL="postgresql://user:pass@direct-host:5432/db"
```

## Monitor Build Performance

Check build times in Netlify dashboard:
- Aim for < 5 minute builds
- Optimize dependencies if exceeding
- Consider splitting code

## Deployment Notifications

Configure in `netlify.toml`:

```toml
[build.environment]
ENABLE_NETLIFY_ANALYTICS = "true"

[context.production]
environment = { ENABLE_NETLIFY_ANALYTICS = "true" }
```

## Rate Limiting

Set up API rate limiting in your Next.js middleware or use Netlify Add-ons.

## Error Pages

Create custom error pages:
- `public/404.html` - Not found
- `public/500.html` - Server error

## Security Headers

Already configured in `netlify.toml`:
- X-Frame-Options: SAMEORIGIN
- X-Content-Type-Options: nosniff
- X-XSS-Protection: 1; mode=block

## Additional Recommendations

1. **Enable Netlify Analytics** - Track visitor stats
2. **Set up Deploy Notifications** - Email on deploy
3. **Use Netlify Forms** - For contact forms
4. **Enable Netlify Identity** - For user auth
5. **Use Netlify CMS** - For content management
6. **Enable Split Testing** - A/B test features

## Troubleshooting

### High Build Times
- Check dependencies in package.json
- Remove unused packages
- Consider lazy loading

### Cold Starts
- Pre-warm functions with scheduled jobs
- Optimize function code size
- Use edge functions for low-latency

### Connection Issues
- Verify database connection pooling
- Check firewall rules
- Monitor network requests

---

For more information, see:
- Netlify Docs: https://docs.netlify.com
- Next.js on Netlify: https://docs.netlify.com/integrations/frameworks/next-js/
- Prisma with Netlify: https://www.prisma.io/docs/orm/deployment/edge-deployment
