# Netlify Deployment Guide

## Quick Start

1. **Upload to Netlify**
   - Go to [netlify.com](https://netlify.com)
   - Click "New site from Git" or drag the project folder
   - Set build command: `npm run build`
   - Set publish directory: `dist`

2. **Configure Build Settings**
   ```
   Build command: npm run build
   Publish directory: dist
   Node version: 20
   ```

3. **Deploy**
   - Click "Deploy site"
   - Your site will be live at a netlify URL

## Environment Variables (Optional)

For real API integration, add in Netlify dashboard → Site settings → Environment variables:

```
VITE_OPENAI_API_KEY=your_openai_key
VITE_YOUTUBE_API_KEY=your_youtube_key  
VITE_SLACK_WEBHOOK_URL=your_slack_webhook
```

## Custom Domain (Optional)

1. Go to Site settings → Domain management
2. Add custom domain
3. Configure DNS records as instructed

## Performance Optimizations

The site is pre-configured with:
- Bundle splitting for faster loading
- CDN optimization via Netlify
- Image optimization ready
- SPA routing with redirects

## Monitoring

- Use Netlify Analytics for basic metrics
- Add Google Analytics if needed
- Monitor Core Web Vitals in deployment

Your simplified search page is now ready for production!