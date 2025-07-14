# Database Schema Documentation

## Overview

Sheikita's Search Page uses PostgreSQL with Drizzle ORM for type-safe database operations.

## Database Configuration

### Connection
```typescript
// server/db.ts
import { Pool, neonConfig } from '@neondatabase/serverless';
import { drizzle } from 'drizzle-orm/neon-serverless';

export const pool = new Pool({ 
  connectionString: process.env.DATABASE_URL 
});
export const db = drizzle({ client: pool, schema });
```

### Environment Variables
```env
DATABASE_URL=postgresql://user:password@host:port/database
```

## Schema Definition

### Users Table
Stores user authentication data for admin access.

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  username VARCHAR UNIQUE NOT NULL,
  password VARCHAR NOT NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

**TypeScript Interface:**
```typescript
interface User {
  id: number;
  username: string;
  password: string;
  createdAt?: Date;
  updatedAt?: Date;
}
```

### Search Results Table
Stores aggregated content and AI summaries.

```sql
CREATE TABLE search_results (
  id SERIAL PRIMARY KEY,
  category VARCHAR NOT NULL,
  youtube_content JSONB,
  google_news_content JSONB,
  substack_content JSONB,
  google_news_rss_content JSONB,
  substack_articles_content JSONB,
  ai_summary TEXT,
  slack_posted BOOLEAN DEFAULT FALSE,
  slack_scheduled BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT NOW()
);
```

**TypeScript Interface:**
```typescript
interface SearchResult {
  id: number;
  category: string;
  youtubeContent?: YouTubeContent;
  googleNewsContent?: GoogleNewsContent;
  substackContent?: SubstackContent;
  googleNewsRssContent?: GoogleNewsRssContent[];
  substackArticlesContent?: SubstackArticle[];
  aiSummary?: string;
  slackPosted: boolean;
  slackScheduled: boolean;
  createdAt: Date;
}
```

### Sessions Table
Handles session storage for admin authentication.

```sql
CREATE TABLE sessions (
  sid VARCHAR PRIMARY KEY,
  sess JSONB NOT NULL,
  expire TIMESTAMP NOT NULL
);

CREATE INDEX IDX_session_expire ON sessions(expire);
```

## Content Types

### YouTube Content
```typescript
interface YouTubeContent {
  title: string;
  url: string;
  description?: string;
  thumbnail?: string;
  publishedAt?: string;
  channelTitle?: string;
}
```

### Google News RSS Content
```typescript
interface GoogleNewsRssContent {
  title: string;
  url: string;
  description?: string;
  publishedAt?: string;
  source?: string;
}
```

### Substack Content
```typescript
interface SubstackContent {
  title: string;
  url: string;
  description?: string;
  author?: string;
  source?: string;
  publishedAt?: string;
}
```

## Database Operations

### Search Results Operations

#### Create Search Result
```typescript
async function createSearchResult(data: InsertSearchResult): Promise<SearchResult> {
  const [result] = await db
    .insert(searchResults)
    .values(data)
    .returning();
  return result;
}
```

#### Get Search Result
```typescript
async function getSearchResult(id: number): Promise<SearchResult | undefined> {
  const [result] = await db
    .select()
    .from(searchResults)
    .where(eq(searchResults.id, id));
  return result;
}
```

#### Update Search Result
```typescript
async function updateSearchResult(
  id: number, 
  updates: Partial<SearchResult>
): Promise<SearchResult | undefined> {
  const [result] = await db
    .update(searchResults)
    .set(updates)
    .where(eq(searchResults.id, id))
    .returning();
  return result;
}
```

#### Get Recent Search Results
```typescript
async function getRecentSearchResults(limit: number = 10): Promise<SearchResult[]> {
  return await db
    .select()
    .from(searchResults)
    .orderBy(desc(searchResults.createdAt))
    .limit(limit);
}
```

### User Operations

#### Create User
```typescript
async function createUser(userData: InsertUser): Promise<User> {
  const [user] = await db
    .insert(users)
    .values(userData)
    .returning();
  return user;
}
```

#### Get User by Username
```typescript
async function getUserByUsername(username: string): Promise<User | undefined> {
  const [user] = await db
    .select()
    .from(users)
    .where(eq(users.username, username));
  return user;
}
```

#### Validate Admin Credentials
```typescript
async function validateAdminCredentials(
  username: string, 
  password: string
): Promise<User | null> {
  const user = await getUserByUsername(username);
  if (user && user.password === password) {
    return user;
  }
  return null;
}
```

## Migration Commands

### Database Setup
```bash
# Initialize database with tables
npm run db:push

# Generate migrations
npm run db:generate

# Apply migrations
npm run db:migrate

# Open database studio
npm run db:studio
```

### Drizzle Configuration
```typescript
// drizzle.config.ts
import type { Config } from 'drizzle-kit';

export default {
  schema: './shared/schema.ts',
  out: './drizzle',
  driver: 'pg',
  dbCredentials: {
    connectionString: process.env.DATABASE_URL!,
  },
} satisfies Config;
```

## JSON Storage

The application uses JSONB columns for flexible content storage:

### Benefits
- Flexible schema for different content types
- Efficient querying with PostgreSQL JSON operators
- Type safety with TypeScript interfaces
- Easy serialization/deserialization

### Querying JSON Data
```sql
-- Query YouTube content
SELECT * FROM search_results 
WHERE youtube_content->>'title' LIKE '%AI%';

-- Query Google News RSS
SELECT * FROM search_results 
WHERE google_news_rss_content @> '[{"source": "CNN"}]';

-- Query Substack articles
SELECT * FROM search_results 
WHERE substack_articles_content @> '[{"author": "Casey Newton"}]';
```

## Performance Considerations

### Indexes
- Primary key indexes on all tables
- Index on `sessions.expire` for session cleanup
- Consider adding indexes on frequently queried JSON fields

### Connection Pooling
- Uses Neon Database serverless adapter
- Automatic connection pooling
- Optimized for serverless environments

### Query Optimization
- Use `select()` with specific columns when possible
- Implement pagination for large result sets
- Use appropriate `limit()` clauses

## Backup and Recovery

### Database Backup
```bash
# Create backup
pg_dump $DATABASE_URL > backup.sql

# Restore from backup
psql $DATABASE_URL < backup.sql
```

### Environment-Specific Considerations
- Development: Use local PostgreSQL or Docker
- Production: Use managed PostgreSQL service (Neon, Supabase, etc.)
- Testing: Use separate test database

## Security Considerations

### Data Protection
- Passwords are stored as plain text (consider hashing for production)
- Session data is stored securely in PostgreSQL
- JSON content is validated before storage

### Access Control
- Admin authentication required for sensitive operations
- Session-based access control
- Environment variable protection for database credentials

## Monitoring and Maintenance

### Database Health
- Monitor connection pool usage
- Track query performance
- Set up alerts for database errors

### Cleanup Tasks
- Automatic session cleanup based on expiration
- Consider implementing data retention policies
- Regular database maintenance and optimization