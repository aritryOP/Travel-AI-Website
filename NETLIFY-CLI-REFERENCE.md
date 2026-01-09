# Netlify CLI Quick Reference

For developers who prefer command-line deployment.

---

## Installation

```bash
# Install Netlify CLI globally
npm install -g netlify-cli

# Or use as npx
npx netlify-cli [command]
```

## Authentication

```bash
# Login to Netlify (opens browser)
netlify login

# Logout
netlify logout

# Check login status
netlify status
```

---

## Site Management

### Create New Site

```bash
# Interactive setup
netlify init

# Or deploy directly
netlify deploy --prod --open
```

### Link Existing Site

```bash
# Link to existing Netlify site
netlify link

# Link to specific site
netlify link --site=your-site-id
```

### List Sites

```bash
# List all your sites
netlify sites:list
```

---

## Deployment

### Basic Deploy

```bash
# Deploy to preview
netlify deploy

# Deploy to production
netlify deploy --prod

# Deploy specific directory
netlify deploy --dir=.next

# Deploy and open in browser
netlify deploy --prod --open
```

### Advanced Deploy

```bash
# Deploy with message
netlify deploy --prod -m "Deployed feature X"

# Deploy without prompts
netlify deploy --prod --prod

# Deploy with verbose output
netlify deploy --prod --debug

# Trigger build
netlify deploy --trigger
```

---

## Environment Variables

### Set Variables

```bash
# Set single variable
netlify env:set DATABASE_URL "postgresql://..."

# Set multiple variables
netlify env:set KEY1 value1 KEY2 value2

# Set from file
netlify env:set --import-file .env.production
```

### View Variables

```bash
# List all environment variables
netlify env:list

# Show specific variable
netlify env:get DATABASE_URL

# Export variables
netlify env:export > env-backup.txt
```

### Delete Variables

```bash
# Delete variable
netlify env:unset DATABASE_URL

# Delete multiple
netlify env:unset VAR1 VAR2
```

---

## Build Management

### Build Info

```bash
# Get current build settings
netlify build

# Check build status
netlify builds:list

# View specific build logs
netlify builds:log [build-id]
```

### Trigger Builds

```bash
# Trigger a new build
netlify build

# Trigger from production
netlify deploy --trigger --prod
```

---

## Functions

### Local Development

```bash
# Run functions locally
netlify dev

# Run on specific port
netlify dev --port 3001

# Run with debugging
netlify dev --debug
```

### Deploy Functions

```bash
# Deploy functions
netlify functions:deploy

# List deployed functions
netlify functions:list

# Invoke function
netlify functions:invoke my-function

# Invoke with payload
netlify functions:invoke my-function --payload '{"key":"value"}'
```

---

## Sites & Domains

### Site Info

```bash
# Get site details
netlify sites:info

# Get specific site info
netlify sites:info --site=your-site-id
```

### Domains

```bash
# List domains
netlify domains:list

# Add domain
netlify domains:create example.com

# List DNS records
netlify dns:list example.com

# Add DNS record
netlify dns:create example.com --name=www --type=CNAME --value=your-site.netlify.app
```

---

## Redirects & Headers

### View Configuration

```bash
# Check netlify.toml configuration
cat netlify.toml

# Validate configuration
netlify deploy --dry-run
```

### Update Configuration

```bash
# Edit netlify.toml
# Then deploy to apply changes
netlify deploy --prod
```

---

## Local Development

### Development Server

```bash
# Start dev server (watches netlify.toml)
netlify dev

# Specify different port
netlify dev --port 8000

# Live reload
netlify dev --autoplay

# Debug mode
netlify dev --debug
```

### Build Locally

```bash
# Run build locally
netlify build

# Build with specific directory
netlify build --functions=netlify/functions
```

---

## Logs & Debugging

### View Logs

```bash
# Get site status
netlify status

# Get deploy logs
netlify deploy:log

# Get build logs
netlify builds:log [build-id]

# Stream logs
netlify logs:deploy --tail

# View function logs
netlify functions:invoke function-name
```

### Debug Info

```bash
# Get debug information
netlify status --verbose

# Check CLI version
netlify --version

# Check config
netlify config:list
```

---

## Continuous Deployment

### GitHub Integration

```bash
# Link GitHub repo (interactive)
netlify init

# Or manually connect through dashboard

# Redeploy on every push to main
# (Configured in dashboard or netlify.toml)
```

### Deploy Notifications

```bash
# Add deploy notification
netlify notification:create --recipient=email@example.com

# List notifications
netlify notification:list

# Remove notification
netlify notification:delete notification-id
```

---

## Configuration File

### netlify.toml Example

```toml
# Build settings
[build]
command = "npm run build"
functions = "netlify/functions"
publish = ".next"

# Development
[dev]
command = "npm run dev"
port = 3000

# Environment variables
[context.production]
environment = { DATABASE_URL = "..." }

# Redirects
[[redirects]]
from = "/*"
to = "/index.html"
status = 200

# Headers
[[headers]]
for = "/*"
[headers.values]
  X-Frame-Options = "SAMEORIGIN"
```

---

## Complete Deployment Workflow (CLI)

```bash
# 1. Install CLI
npm install -g netlify-cli

# 2. Login
netlify login

# 3. Build locally
npm run build

# 4. Link site (if existing)
netlify link

# 5. Set environment variables
netlify env:set DATABASE_URL "postgresql://..."
netlify env:set JWT_SECRET "your-secret"
netlify env:set NEXT_PUBLIC_APP_URL "https://your-domain"

# 6. Test locally
netlify dev

# 7. Deploy to production
netlify deploy --prod --open

# 8. Verify deployment
netlify status
netlify env:list
```

---

## Useful Aliases

```bash
# Add to ~/.bashrc or ~/.zshrc
alias ndeploy='netlify deploy --prod'
alias ndev='netlify dev'
alias nstatus='netlify status'
alias nlogs='netlify builds:log'
alias nenv='netlify env:list'
```

---

## Environment Variables from CLI

```bash
# Set multiple at once
netlify env:set \
  DATABASE_URL "postgresql://..." \
  JWT_SECRET "secret" \
  GOOGLE_MAPS_API_KEY "key" \
  GEMINI_API_KEY "key"

# Import from file
cat > .env.production << EOF
DATABASE_URL=postgresql://...
JWT_SECRET=secret
NEXT_PUBLIC_APP_URL=https://example.com
EOF

netlify env:set --import-file .env.production
```

---

## Troubleshooting CLI

### Clear Cache

```bash
# Clear CLI cache
netlify config:clear-cache

# Reset configuration
netlify config:reset
```

### Common Errors

```bash
# Not logged in
netlify login

# Site not linked
netlify link

# Build failed
netlify deploy --debug
netlify build --debug

# Wrong directory
netlify deploy --dir=.next
```

---

## Pro Tips

### Parallel Operations

```bash
# Use shell features for speed
(netlify env:set VAR1 value1 &)
(netlify env:set VAR2 value2 &)
wait
netlify deploy --prod
```

### Automation

```bash
#!/bin/bash
# deployment.sh

set -e  # Exit on error

echo "Building..."
npm run build

echo "Setting environment variables..."
netlify env:set DATABASE_URL "$DB_URL"
netlify env:set JWT_SECRET "$JWT_SECRET"

echo "Deploying..."
netlify deploy --prod

echo "Verifying..."
netlify status

echo "✅ Deployment complete!"
```

### Quick Deploy Script

```bash
#!/bin/bash
# quickdeploy.sh

git add .
git commit -m "Deploy: $(date)"
git push origin main
netlify deploy --prod --open
```

---

## Additional Resources

- **Netlify CLI Docs:** https://docs.netlify.com/cli/get-started/
- **Command Reference:** https://cli.netlify.com/
- **Examples:** https://github.com/netlify/cli/tree/main/examples

---

## Quick Command Reference

| Task | Command |
|------|---------|
| Login | `netlify login` |
| Init project | `netlify init` |
| Deploy | `netlify deploy --prod` |
| Dev server | `netlify dev` |
| Build | `netlify build` |
| Set env | `netlify env:set KEY value` |
| List env | `netlify env:list` |
| Site info | `netlify sites:info` |
| View status | `netlify status` |
| View logs | `netlify logs:deploy --tail` |

---

For more commands: `netlify --help`  
For specific command help: `netlify [command] --help`
