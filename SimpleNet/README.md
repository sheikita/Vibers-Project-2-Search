# Simple Sheikita's Search - Netlify Version

A simplified version of Sheikita's AI-powered news aggregation platform, optimized for Netlify deployment.

## Features

- Clean, responsive interface for news search
- Multi-source content aggregation (demo mode)
- Category-based news filtering
- Mock Slack integration
- No cost monitoring (simplified version)
- Optimized for static hosting

## Quick Deploy to Netlify

### Option 1: One-Click Deploy
[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/your-username/simple-sheikitas-search)

### Option 2: Manual Deploy

1. **Clone or download this project**
   ```bash
   git clone https://github.com/your-username/simple-sheikitas-search.git
   cd simple-sheikitas-search
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Build the project**
   ```bash
   npm run build
   ```

4. **Deploy to Netlify**
   - Go to [Netlify](https://netlify.com)
   - Drag and drop the `dist` folder to deploy
   - Or connect your GitHub repository for automatic deployments

### Option 3: Netlify CLI

1. **Install Netlify CLI**
   ```bash
   npm install -g netlify-cli
   ```

2. **Login and deploy**
   ```bash
   netlify login
   netlify init
   netlify deploy --prod
   ```

## Development

### Local Development
```bash
npm install
npm run dev
```

### Build for Production
```bash
npm run build
npm run preview
```

## Configuration

### Environment Variables (for production)
When you integrate with real APIs, add these environment variables in Netlify:

- `VITE_OPENAI_API_KEY` - Your OpenAI API key
- `VITE_YOUTUBE_API_KEY` - Your YouTube Data API key
- `VITE_SLACK_WEBHOOK_URL` - Your Slack webhook URL

### Netlify Functions Integration
To replace mock data with real API calls:

1. Create `netlify/functions` directory
2. Add serverless functions for API integrations
3. Update the `mockAPIRequest` function in `src/pages/home.tsx` to call your Netlify Functions

Example Netlify Function (`netlify/functions/search.js`):
```javascript
exports.handler = async (event, context) => {
  const { category } = JSON.parse(event.body);
  
  // Your API integration logic here
  const result = await aggregateContent(category);
  
  return {
    statusCode: 200,
    headers: {
      "Access-Control-Allow-Origin": "*",
      "Access-Control-Allow-Headers": "Content-Type",
    },
    body: JSON.stringify(result),
  };
};
```

## Project Structure

```
simple-sheikitas-search/
├── src/
│   ├── components/ui/     # UI components
│   ├── hooks/            # React hooks
│   ├── lib/              # Utilities
│   ├── pages/            # Page components
│   ├── App.tsx           # Main app component
│   ├── main.tsx          # Entry point
│   └── index.css         # Global styles
├── netlify.toml          # Netlify configuration
├── _redirects            # Netlify redirects
├── package.json          # Dependencies
├── vite.config.ts        # Vite configuration
└── tailwind.config.ts    # Tailwind configuration
```

## Key Differences from Full Version

- **No Authentication**: Removed Replit OAuth and admin features
- **No Cost Monitoring**: Simplified to remove complex dashboard
- **No Backend**: Static-only deployment with mock data
- **Netlify Optimized**: Configured for Netlify's CDN and redirects
- **Mock APIs**: Placeholder API calls (ready for Netlify Functions)

## Next Steps for Production

1. **Add Netlify Functions** for real API integrations
2. **Configure environment variables** for API keys
3. **Set up form handling** for user feedback
4. **Add analytics** (Netlify Analytics or Google Analytics)
5. **Configure custom domain** in Netlify settings

## Tech Stack

- **Frontend**: React 18, TypeScript, Vite
- **Styling**: Tailwind CSS, Shadcn/ui components
- **State Management**: TanStack Query
- **Routing**: Wouter (lightweight)
- **Deployment**: Netlify
- **Build Tool**: Vite

## Performance

- Optimized bundle splitting
- CDN delivery via Netlify
- Responsive images and lazy loading
- Minimal JavaScript footprint

## License

MIT License - Feel free to use this project as a starting point for your own applications.