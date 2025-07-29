# Strapi Railway Deployment Guide

## 🚀 Railway Deployment

### **1. Connect Repository to Railway**
1. Go to [Railway](https://railway.app/)
2. Click "New Project" → "Deploy from GitHub repo"
3. Select `goalsystems-blog-strapi` repository
4. Railway will auto-detect Strapi and configure deployment

### **2. Add PostgreSQL Database**
1. In Railway project dashboard, click "New Service"
2. Select "PostgreSQL"
3. Railway will automatically set `DATABASE_URL` environment variable

### **3. Configure Environment Variables**
Add these in Railway project settings:

```bash
# Production Configuration
NODE_ENV=production
HOST=0.0.0.0
PORT=3000

# Database (Auto-configured by Railway)
DATABASE_CLIENT=postgres
# DATABASE_URL is automatically set by Railway PostgreSQL

# Security Keys (Generate new ones!)
APP_KEYS=generate-strong-keys-here
API_TOKEN_SALT=generate-strong-salt-here
ADMIN_JWT_SECRET=generate-strong-jwt-secret-here
TRANSFER_TOKEN_SALT=generate-strong-transfer-salt-here
JWT_SECRET=generate-strong-jwt-secret-here

# n8n Integration
N8N_WEBHOOK_URL=https://your-n8n-instance.railway.app
```

### **4. Generate Secure Keys**
Run locally to generate secure keys:
```bash
# Generate APP_KEYS (4 keys separated by commas)
node -e "console.log(require('crypto').randomBytes(32).toString('base64'))"

# Generate other secrets
node -e "console.log(require('crypto').randomBytes(32).toString('base64'))"
```

## 🏠 Local Development

### **1. Environment Setup**
Create `.env` file:
```bash
# Copy example file
cp .env.example .env

# Edit with your local settings
```

### **2. Local Development Commands**
```bash
# Install dependencies
npm install

# Start development server
npm run develop

# Seed example data
npm run seed:example

# Build for production testing
npm run build
npm start
```

### **3. Local Database**
- **SQLite database** stored in `.tmp/data.db`
- **No external database** required for local development
- **Admin panel**: http://localhost:1337/admin

## 🔗 n8n Integration

### **Webhook Configuration**
After Railway deployment, configure webhooks in Strapi admin:

1. Go to Settings → Webhooks
2. Add new webhook:
   - **Name**: `n8n-editorial-workflow`
   - **URL**: `https://your-n8n-instance.railway.app/webhook/strapi-editorial`
   - **Events**: `entry.update`, `entry.create`
   - **Headers**: 
     ```
     Authorization: Bearer your-webhook-secret
     Content-Type: application/json
     ```

### **API Token for n8n**
1. Go to Settings → API Tokens
2. Create new token:
   - **Name**: `n8n-editorial-access`
   - **Type**: `Full access`
   - **Duration**: `Unlimited`
3. Copy token for n8n environment variables

## 🚀 Deployment Workflow

### **Development → Production**
1. **Develop locally** with `npm run develop`
2. **Commit changes** to GitHub
3. **Railway auto-deploys** from main branch
4. **Test production** deployment
5. **Configure webhooks** for n8n integration

### **Environment Differences**
| Feature | Local | Railway Production |
|---------|--------|-------------------|
| Database | SQLite | PostgreSQL |
| Webhooks | Disabled | Active to n8n |
| Admin URL | localhost:1337/admin | your-app.railway.app/admin |
| API URL | localhost:1337/api | your-app.railway.app/api | 