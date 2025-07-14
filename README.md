# Sheikita's Search Page - AI-Powered News Aggregation

A comprehensive full-stack web application that aggregates content from multiple sources (YouTube, Google News, and Substack), generates AI-powered summaries, and provides optional Slack integration with real-time cost monitoring.

![Node.js](https://img.shields.io/badge/Node.js-20.x-green)
![TypeScript](https://img.shields.io/badge/TypeScript-5.x-blue)
![React](https://img.shields.io/badge/React-18.x-blue)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16.x-blue)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o-orange)

## 🚀 Features

### Core Functionality
- **Multi-Source Content Aggregation**: Fetches content from YouTube, Google News RSS, and Substack
- **AI-Powered Summaries**: Generates 65-word contextual summaries using OpenAI GPT-4o
- **Category-Based Search**: Supports AI, Bollywood, Pets, Sports, and Tech categories
- **Slack Integration**: Optional scheduling with 60-second delay and comprehensive content sharing
- **Real-Time Cost Monitoring**: Track API usage, costs, and performance metrics with admin dashboard

### Technical Features
- **Modern Stack**: React 18, TypeScript, Express.js, PostgreSQL
- **Responsive Design**: Mobile-first approach with Tailwind CSS
- **Authentication**: Secure admin-only access to cost monitoring
- **Error Handling**: Graceful degradation with partial results
- **Performance Monitoring**: Real-time tracking of API response times and success rates

## 📋 Prerequisites

Before you begin, ensure you have:
- Node.js 20.x or higher installed
- PostgreSQL 16.x database
- API keys for external services (see [API Configuration](#api-configuration))

## 🛠️ Installation

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/sheikitas-search-page.git
cd sheikitas-search-page
```

### 2. Install Dependencies
```bash
npm install
```

### 3. Database Setup
```bash
# Create database tables
npm run db:push
```

### 4. Environment Configuration
Create a `.env` file in the root directory:
```env
# Database
DATABASE_URL=your_postgresql_connection_string

# API Keys
OPENAI_API_KEY=your_openai_api_key
YOUTUBE_API_KEY=your_youtube_api_key
SLACK_WEBHOOK_URL=your_slack_webhook_url

# Session Security
SESSION_SECRET=your_secure_session_secret

# Replit Configuration (if deploying on Replit)
REPL_ID=your_repl_id
REPLIT_DOMAINS=your_replit_domains
ISSUER_URL=https://replit.com/oidc
```

### 5. Admin User Setup
On first run, visit `/admin-login` and click "Initialize Admin User" to create admin credentials.

## 🚀 Running the Application

### Development Mode
```bash
npm run dev
```
This starts both the Express server and Vite development server on port 5000.

### Production Mode
```bash
npm run build
npm start
```

### Database Commands
```bash
# Push schema changes to database
npm run db:push

# Generate database migrations
npm run db:generate

# Studio for database management
npm run db:studio
```

## 🔧 API Configuration

### OpenAI API Setup
1. Visit [OpenAI Platform](https://platform.openai.com/)
2. Create an API key
3. Add to `OPENAI_API_KEY` environment variable

### YouTube API Setup
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing
3. Enable YouTube Data API v3
4. Create credentials (API Key)
5. Add to `YOUTUBE_API_KEY` environment variable

### Slack Webhook Setup
1. Visit [Slack API Apps](https://api.slack.com/apps)
2. Create a new app or select existing
3. Go to "Incoming Webhooks"
4. Create a webhook URL
5. Add to `SLACK_WEBHOOK_URL` environment variable

### PostgreSQL Database Setup
1. Create a PostgreSQL database
2. Get connection string in format: `postgresql://user:password@host:port/database`
3. Add to `DATABASE_URL` environment variable

## 📖 Usage Guide

### Basic Search
1. Visit the application homepage
2. Select a category (AI, Bollywood, Pets, Sports, Tech)
3. Click "Search" to aggregate content
4. View results with AI-generated summary

### Slack Integration
1. Complete a search to generate results
2. Click "Schedule to Slack" button
3. Content will be posted to your Slack channel in 60 seconds
4. Includes YouTube video, Google News articles, and Substack content

### Admin Dashboard
1. Visit `/admin-login`
2. Log in with admin credentials
3. Access cost monitoring at `/cost-dashboard`
4. View real-time API usage, costs, and performance metrics

## 🏗️ Project Structure

```
├── client/                 # React frontend
│   ├── src/
│   │   ├── components/     # UI components
│   │   ├── pages/          # Page components
│   │   ├── hooks/          # Custom React hooks
│   │   └── lib/            # Utility functions
├── server/                 # Express backend
│   ├── services/           # External API integrations
│   ├── routes/             # API endpoints
│   └── db.ts               # Database configuration
├── shared/                 # Shared types and schemas
└── docs/                   # Additional documentation
```

## 🔌 API Endpoints

### Search Endpoints
- `POST /api/search` - Create new search with category
- `GET /api/search/:id` - Get search result by ID

### Slack Integration
- `POST /api/slack/schedule/:id` - Schedule Slack post for search result
- `POST /api/test/slack` - Test Slack connection

### Admin Endpoints
- `POST /api/admin/login` - Admin authentication
- `POST /api/admin/logout` - Admin logout
- `POST /api/admin/init` - Initialize admin user
- `GET /api/cost/*` - Cost monitoring endpoints (admin only)

### Test Endpoints
- `POST /api/test/openai` - Test OpenAI connection
- `POST /api/test/youtube` - Test YouTube API connection
- `POST /api/test/substack` - Test Substack connection

## 🔍 Content Sources

### YouTube Integration
- Uses YouTube Data API v3
- Searches for relevant videos by category
- Returns video title, URL, description, and thumbnail

### Google News RSS
- Fetches RSS feeds from Google News
- Returns up to 5 related articles per category
- Includes title, URL, description, and source

### Substack Articles
- Curated RSS feeds from category-specific publications
- Returns up to 5 articles per category
- Includes title, URL, description, and author

## 📊 Cost Monitoring

### Features
- Real-time API usage tracking
- Cost calculation for OpenAI tokens
- Performance metrics (response time, success rate)
- Daily summaries and trends
- Automated cost alerts

### Accessing Dashboard
1. Admin authentication required
2. Visit `/cost-dashboard`
3. View service breakdowns and recommendations
4. Monitor daily usage patterns

## 🛡️ Security Features

### Authentication
- Session-based admin authentication
- 24-hour session expiration
- Secure session storage in PostgreSQL

### API Security
- Input validation with Zod schemas
- Rate limiting protection
- CORS configuration
- Environment variable protection

## 🚀 Deployment

### Replit Deployment
1. Connect your repository to Replit
2. Configure environment variables in Replit Secrets
3. The application will automatically deploy

### Traditional Deployment
1. Build the application: `npm run build`
2. Set up PostgreSQL database
3. Configure environment variables
4. Start the server: `npm start`

## 🔧 Configuration

### Category Configuration
Categories are defined in `shared/schema.ts`:
- `ai` - Artificial Intelligence content
- `bollywood` - Bollywood entertainment
- `pets` - Pet-related content
- `sports` - Sports news and updates
- `tech` - Technology news

### RSS Feed Sources
Substack publications are configured in `server/services/substackService.ts` with curated feeds for each category.

## 📝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit changes: `git commit -m 'Add your feature'`
4. Push to branch: `git push origin feature/your-feature`
5. Submit a pull request

## 🔍 Troubleshooting

### Common Issues

**Database Connection Issues**
- Verify `DATABASE_URL` is correctly formatted
- Ensure PostgreSQL is running
- Check firewall settings

**API Key Issues**
- Verify all API keys are valid and active
- Check API quotas and limits
- Ensure proper permissions are set

**Slack Integration Issues**
- Verify webhook URL is correct
- Check Slack app permissions
- Test webhook manually

### Debug Mode
Enable debug logging by setting `NODE_ENV=development`

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🤝 Support

For support, please open an issue in the GitHub repository or contact the development team.

## 📚 Additional Documentation

- [API Documentation](docs/API.md)
- [Database Schema](docs/DATABASE.md)
- [Deployment Guide](docs/DEPLOYMENT.md)
- [Cost Monitoring Guide](docs/COST_MONITORING.md)