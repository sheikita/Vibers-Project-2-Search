# Deployment Guide

## Overview

This guide covers deployment options for Sheikita's Search Page application across different platforms.

## Deployment Options

### 1. Replit Deployment (Recommended)

Replit provides seamless deployment with automatic scaling and environment management.

#### Prerequisites
- Replit account
- GitHub repository

#### Steps
1. **Connect Repository**
   - Import your GitHub repository to Replit
   - Replit will automatically detect the Node.js project

2. **Configure Environment Variables**
   - Go to Replit Secrets panel
   - Add the following secrets:
     ```
     DATABASE_URL=your_postgresql_connection_string
     OPENAI_API_KEY=your_openai_api_key
     YOUTUBE_API_KEY=your_youtube_api_key
     SLACK_WEBHOOK_URL=your_slack_webhook_url
     SESSION_SECRET=your_secure_session_secret
     ```

3. **Database Setup**
   - Replit automatically provisions PostgreSQL
   - Run `npm run db:push` to create tables

4. **Deploy**
   - Click "Run" or use `npm run dev`
   - Replit automatically handles deployment

#### Replit Configuration
```json
{
  "language": "nodejs",
  "run": "npm run dev",
  "modules": ["nodejs-20", "web", "postgresql-16"],
  "domains": ["your-app.replit.app"],
  "ports": {
    "5000": {
      "protocol": "http",
      "public": true
    }
  }
}
```

### 2. Vercel Deployment

Vercel offers excellent performance for full-stack applications.

#### Prerequisites
- Vercel account
- GitHub repository

#### Steps
1. **Connect Repository**
   - Import GitHub repository to Vercel
   - Configure as Node.js project

2. **Build Configuration**
   ```json
   {
     "buildCommand": "npm run build",
     "outputDirectory": "dist",
     "installCommand": "npm install"
   }
   ```

3. **Environment Variables**
   - Add environment variables in Vercel dashboard
   - Include all required API keys and database URL

4. **Database Setup**
   - Use Vercel Postgres or external PostgreSQL
   - Run migrations: `npm run db:push`

### 3. Railway Deployment

Railway provides simple deployment with PostgreSQL integration.

#### Steps
1. **Connect Repository**
   - Link GitHub repository to Railway
   - Railway auto-detects Node.js application

2. **Add PostgreSQL Service**
   - Add PostgreSQL plugin
   - Railway automatically sets `DATABASE_URL`

3. **Environment Variables**
   - Configure API keys in Railway dashboard
   - Use Railway's environment variable management

4. **Deploy**
   - Railway automatically deploys on git push
   - Access via provided Railway URL

### 4. Heroku Deployment

Traditional cloud platform with extensive add-on ecosystem.

#### Prerequisites
- Heroku CLI installed
- Heroku account

#### Steps
1. **Create Heroku App**
   ```bash
   heroku create your-app-name
   ```

2. **Add PostgreSQL**
   ```bash
   heroku addons:create heroku-postgresql:hobby-dev
   ```

3. **Set Environment Variables**
   ```bash
   heroku config:set OPENAI_API_KEY=your_key
   heroku config:set YOUTUBE_API_KEY=your_key
   heroku config:set SLACK_WEBHOOK_URL=your_webhook
   heroku config:set SESSION_SECRET=your_secret
   ```

4. **Deploy**
   ```bash
   git push heroku main
   ```

5. **Run Migrations**
   ```bash
   heroku run npm run db:push
   ```

### 5. Docker Deployment

Containerized deployment for any platform.

#### Dockerfile
```dockerfile
FROM node:20-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./
RUN npm ci --only=production

# Copy source code
COPY . .

# Build application
RUN npm run build

# Expose port
EXPOSE 5000

# Start application
CMD ["npm", "start"]
```

#### Docker Compose
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=postgresql://user:password@db:5432/sheikita
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - YOUTUBE_API_KEY=${YOUTUBE_API_KEY}
      - SLACK_WEBHOOK_URL=${SLACK_WEBHOOK_URL}
      - SESSION_SECRET=${SESSION_SECRET}
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=sheikita
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  postgres_data:
```

#### Deploy Commands
```bash
# Build and run
docker-compose up --build

# Run migrations
docker-compose exec app npm run db:push
```

## Environment Configuration

### Required Environment Variables
```env
# Database
DATABASE_URL=postgresql://user:password@host:port/database

# API Keys
OPENAI_API_KEY=sk-...
YOUTUBE_API_KEY=AIza...
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/...

# Security
SESSION_SECRET=your-secure-random-string

# Optional (for Replit)
REPL_ID=your-repl-id
REPLIT_DOMAINS=your-domain.replit.app
ISSUER_URL=https://replit.com/oidc
```

### Environment-Specific Settings

#### Development
```env
NODE_ENV=development
PORT=5000
LOG_LEVEL=debug
```

#### Production
```env
NODE_ENV=production
PORT=5000
LOG_LEVEL=info
```

## Database Setup

### PostgreSQL Options

#### 1. Managed Services
- **Neon Database** (Recommended for Replit)
- **Supabase** (Full-featured with dashboard)
- **Railway PostgreSQL** (Simple setup)
- **Heroku Postgres** (Heroku add-on)

#### 2. Self-Hosted
- Docker PostgreSQL container
- Local PostgreSQL installation
- Cloud VM with PostgreSQL

### Migration Commands
```bash
# Create tables
npm run db:push

# Generate migrations
npm run db:generate

# Apply migrations
npm run db:migrate

# Open database studio
npm run db:studio
```

## SSL and Security

### HTTPS Setup
Most platforms provide automatic HTTPS:
- Replit: Automatic HTTPS
- Vercel: Automatic HTTPS
- Railway: Automatic HTTPS
- Heroku: Automatic HTTPS

### Custom Domain
Configure custom domains in your deployment platform:
1. Add domain to platform
2. Configure DNS records
3. Enable SSL certificate

### Security Headers
```typescript
// Add to server configuration
app.use((req, res, next) => {
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Strict-Transport-Security', 'max-age=31536000');
  next();
});
```

## Performance Optimization

### Build Optimization
```json
{
  "scripts": {
    "build": "npm run build:client && npm run build:server",
    "build:client": "vite build",
    "build:server": "esbuild server/index.ts --outfile=dist/server.js --bundle --platform=node"
  }
}
```

### Caching Strategy
- Static assets: CDN caching
- API responses: Redis caching
- Database queries: Connection pooling

### Monitoring
- Application performance monitoring
- Database performance monitoring
- Error tracking and alerting

## Troubleshooting

### Common Issues

#### Database Connection
```bash
# Test database connection
npm run db:studio

# Check connection string format
echo $DATABASE_URL
```

#### API Keys
```bash
# Test API connections
curl -X POST http://localhost:5000/api/test/openai
curl -X POST http://localhost:5000/api/test/youtube
curl -X POST http://localhost:5000/api/test/slack
```

#### Build Errors
```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install

# Check TypeScript errors
npm run type-check
```

### Debugging

#### Enable Debug Logging
```env
NODE_ENV=development
DEBUG=*
```

#### Check Application Logs
```bash
# Heroku
heroku logs --tail

# Railway
railway logs

# Vercel
vercel logs
```

## Maintenance

### Regular Tasks
- Monitor application performance
- Check database health
- Update dependencies
- Review security updates

### Backup Strategy
- Database backups
- Environment variable backups
- Code repository backups

### Scaling Considerations
- Horizontal scaling with load balancers
- Database read replicas
- CDN for static assets
- Caching layer implementation

## Cost Optimization

### API Usage
- Monitor OpenAI token usage
- Implement request caching
- Set usage limits and alerts

### Database
- Connection pooling
- Query optimization
- Regular maintenance

### Hosting
- Choose appropriate plan sizes
- Monitor resource usage
- Implement auto-scaling where available