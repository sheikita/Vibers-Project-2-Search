# Sheikita's Search Page - AI-Powered News Aggregation

## Overview

This is a full-stack web application that aggregates content from multiple sources (YouTube, Google News, and Substack) based on user-selected categories, generates AI-powered summaries, and optionally posts results to Slack. The application uses a modern tech stack with React frontend, Express.js backend, and PostgreSQL database.

## System Architecture

### Frontend Architecture
- **Framework**: React 18 with TypeScript
- **UI Library**: Shadcn/ui components with Radix UI primitives
- **Styling**: Tailwind CSS with custom category colors
- **State Management**: TanStack Query for server state management
- **Routing**: Wouter for lightweight client-side routing
- **Build Tool**: Vite with custom configuration for development and production

### Backend Architecture
- **Runtime**: Node.js 20 with Express.js framework
- **Language**: TypeScript with ES modules
- **API Design**: RESTful endpoints with centralized error handling
- **Database ORM**: Drizzle ORM with PostgreSQL
- **Schema Validation**: Zod for runtime type checking
- **External Services**: Integration with OpenAI, YouTube API, web scraping

### Database Architecture
- **Database**: PostgreSQL 16
- **ORM**: Drizzle with migrations support
- **Schema**: Two main tables (users, search_results) with JSON storage for content
- **Connection**: Neon Database serverless adapter

## Key Components

### Content Aggregation Service
- **YouTube Integration**: Uses YouTube Data API v3 for video search
- **Google News Scraper**: Puppeteer-based scraping with cheerio for parsing
- **Google News RSS**: Direct RSS feed integration for related news articles (up to 5 articles)
- **Substack RSS Integration**: RSS feed-based content aggregation from curated publications
- **Substack Scraper**: Web scraping fallback approach
- **Error Handling**: Graceful degradation with partial results

### AI Summary Generation
- **Provider**: OpenAI GPT-4o model
- **Functionality**: Synthesizes content from multiple sources into 65-word summaries
- **Fallback**: Error handling for API failures

### Slack Integration
- **Method**: Webhook-based posting with user-controlled scheduling
- **User Control**: "Schedule to Slack" button for optional posting
- **Scheduling**: Node-cron for delayed message posting (1-minute delay)
- **Format**: Rich message formatting with content organization including RSS highlights

### Cost Monitoring System
- **Real-time Tracking**: Logs all API usage with timestamps, costs, and performance metrics
- **Cost Calculation**: Accurate cost tracking for OpenAI tokens ($0.0025/$0.01 per 1K tokens), YouTube API quotas, and RSS feeds
- **Dashboard Interface**: Interactive web dashboard with service breakdowns, trends, and usage analytics
- **Automated Alerts**: Threshold-based alerts for daily costs ($5+) and high failure rates (>10%)
- **Performance Monitoring**: Response time tracking and success rate analysis
- **Cost Optimization**: Automated recommendations for reducing API expenses

### Database Schema
```typescript
// Users table for potential authentication
users: {
  id: serial primary key
  username: text unique
  password: text
}

// Search results with JSON content storage
search_results: {
  id: serial primary key
  category: text
  youtubeContent: jsonb
  googleNewsContent: jsonb  
  substackContent: jsonb
  googleNewsRssContent: jsonb
  aiSummary: text
  slackPosted: boolean
  createdAt: timestamp
}
```

## Data Flow

1. **User Selection**: User selects category from dropdown (AI, Bollywood, Pets, Sports, Tech)
2. **Content Aggregation**: Parallel requests to YouTube API, Substack RSS feeds, web scrapers, and Google News RSS
3. **AI Processing**: OpenAI generates 65-word summary from aggregated content
4. **Database Storage**: Results stored with unique ID and metadata
5. **User Interface**: Results displayed with mobile-responsive design and accessibility features
6. **Slack Control**: User can optionally schedule posting to Slack with "Schedule to Slack" button
7. **Response**: Structured JSON response with all content, RSS feed, Substack articles, and summary

## External Dependencies

### Required API Keys
- **OpenAI API Key**: For AI summary generation (GPT-4o model)
- **YouTube API Key**: For video search functionality
- **Slack Webhook URL**: For automatic Slack posting

### Third-Party Services
- **@neondatabase/serverless**: PostgreSQL database connection
- **puppeteer**: Web scraping for Google News and Substack
- **cheerio**: HTML parsing for scraped content
- **@slack/web-api**: Slack integration
- **node-cron**: Scheduled task execution

### Development Tools
- **tsx**: TypeScript execution for development
- **esbuild**: Fast bundling for production
- **drizzle-kit**: Database migrations and schema management

## Deployment Strategy

### Environment Configuration
- **Development**: Uses tsx for hot reloading with Vite dev server
- **Production**: esbuild compilation with static file serving
- **Database**: Automatic migration support with drizzle-kit

### Replit Configuration
- **Modules**: nodejs-20, web, postgresql-16
- **Ports**: Application serves on port 5000, external port 80
- **Build Command**: `npm run build` (Vite + esbuild)
- **Start Command**: `npm run start` (production) or `npm run dev` (development)

### Error Handling
- **Graceful Degradation**: Continues with partial results if some sources fail
- **Logging**: Comprehensive error logging for debugging
- **Validation**: Input validation with Zod schemas
- **Rate Limiting**: Built-in handling for API rate limits

## Changelog
```
Changelog:
- June 22, 2025. Initial setup and complete implementation
- June 22, 2025. All API connections tested and verified working
- July 14, 2025. Added Google News RSS feed integration with 5 related articles
- July 14, 2025. Reduced AI summary length to 65 words for conciseness
- July 14, 2025. Added new Substack RSS integration for authentic content (up to 5 articles)
- July 14, 2025. Implemented "Schedule to Slack" feature with user control
- July 14, 2025. Enhanced mobile responsiveness and accessibility features
- July 14, 2025. Integrated RSS results into existing Google News section
- July 14, 2025. Updated UI to display multiple Substack articles in organized layout
- July 14, 2025. Implemented comprehensive cost monitoring system with real-time tracking and dashboard
- July 14, 2025. Added navigation bar with cost monitoring access
- July 14, 2025. Integrated cost tracking across all API services (OpenAI, YouTube, RSS feeds)
- July 14, 2025. Implemented admin-only authentication system for cost monitoring dashboard
- July 14, 2025. Added secure session management with admin login/logout functionality
- July 14, 2025. Updated "Schedule to Slack" feature with proper API endpoint integration
- July 14, 2025. Created admin initialization endpoint for first-time setup
- July 14, 2025. Protected cost monitoring routes with authentication middleware
- July 14, 2025. Fixed Slack message formatting to include all content sources (YouTube, Google News RSS, Substack)
- July 14, 2025. Created comprehensive documentation suite for GitHub upload (README, API docs, deployment guide, etc.)
```

## User Preferences
```
Preferred communication style: Simple, everyday language.
```