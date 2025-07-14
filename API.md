# API Documentation

## Overview

This document provides detailed information about the API endpoints available in Sheikita's Search Page application.

## Base URL

Development: `http://localhost:5000`
Production: `https://your-domain.com`

## Authentication

### Admin Authentication
Admin endpoints require authentication via session-based tokens.

#### Login
```http
POST /api/admin/login
Content-Type: application/json

{
  "username": "admin",
  "password": "admin123"
}
```

**Response:**
```json
{
  "message": "Login successful",
  "sessionId": "abc123...",
  "userId": 1
}
```

#### Logout
```http
POST /api/admin/logout
Authorization: Bearer <session_token>
```

## Search Endpoints

### Create Search
Aggregates content from multiple sources based on category.

```http
POST /api/search
Content-Type: application/json

{
  "category": "tech"
}
```

**Parameters:**
- `category` (required): One of "ai", "bollywood", "pets", "sports", "tech"

**Response:**
```json
{
  "id": 1,
  "content": {
    "youtube": {
      "title": "Video Title",
      "url": "https://youtube.com/watch?v=...",
      "description": "Video description",
      "thumbnail": "https://i.ytimg.com/...",
      "publishedAt": "2025-07-13T12:00:00Z",
      "channelTitle": "Channel Name"
    },
    "googleNewsRss": [
      {
        "title": "Article Title",
        "url": "https://news.google.com/...",
        "description": "Article description",
        "publishedAt": "2025-07-13T12:00:00Z",
        "source": "News Source"
      }
    ],
    "substackArticles": [
      {
        "title": "Article Title",
        "url": "https://example.substack.com/...",
        "description": "Article description",
        "author": "Author Name",
        "source": "Publication",
        "publishedAt": "2025-07-13T12:00:00Z"
      }
    ]
  },
  "summary": "AI-generated 65-word summary",
  "slackScheduled": false
}
```

### Get Search Result
Retrieves a specific search result by ID.

```http
GET /api/search/:id
```

**Parameters:**
- `id` (required): Search result ID

**Response:**
Same as Create Search response.

## Slack Integration

### Schedule Slack Post
Schedules a Slack message for a search result with 60-second delay.

```http
POST /api/slack/schedule/:id
Content-Type: application/json
```

**Parameters:**
- `id` (required): Search result ID

**Response:**
```json
{
  "message": "Slack post scheduled successfully"
}
```

**Error Response:**
```json
{
  "message": "Slack post already scheduled"
}
```

### Test Slack Connection
Tests the Slack webhook connection.

```http
POST /api/test/slack
Content-Type: application/json
```

**Response:**
```json
{
  "success": true,
  "message": "Successfully posted to Slack"
}
```

## Admin Endpoints

### Initialize Admin User
Creates the first admin user (only works if no admin exists).

```http
POST /api/admin/init
Content-Type: application/json

{
  "username": "admin",
  "password": "admin123"
}
```

**Response:**
```json
{
  "message": "Admin user created successfully",
  "userId": 1
}
```

### Cost Dashboard
All cost monitoring endpoints require admin authentication.

```http
GET /api/cost/dashboard
Authorization: Bearer <session_token>
```

**Response:**
```json
{
  "summary": {
    "totalCost": "$2.45",
    "totalRequests": 150,
    "averageDailyCost": "$0.35",
    "period": "7 days",
    "costTrend": "+15%"
  },
  "dailySummaries": [
    {
      "date": "2025-07-13",
      "totalCost": 0.35,
      "totalRequests": 25,
      "serviceBreakdown": {
        "openai": { "requests": 15, "cost": 0.30 },
        "youtube": { "requests": 10, "cost": 0.05 }
      },
      "averageResponseTime": 1200,
      "successRate": 0.96
    }
  ],
  "serviceBreakdown": {
    "openai": { "requests": 100, "cost": 2.10 },
    "youtube": { "requests": 50, "cost": 0.35 }
  },
  "recentUsage": [
    {
      "timestamp": "2025-07-13T12:00:00Z",
      "service": "openai",
      "operation": "generate_summary",
      "tokensUsed": 150,
      "estimatedCost": 0.015,
      "requestId": "req_123",
      "category": "tech",
      "success": true,
      "responseTime": 1200
    }
  ],
  "recommendations": [
    "Consider implementing caching for frequently requested categories",
    "Monitor daily costs - approaching $3 threshold"
  ]
}
```

## Test Endpoints

### Test OpenAI Connection
```http
POST /api/test/openai
```

**Response:**
```json
{
  "success": true,
  "message": "OpenAI connection successful"
}
```

### Test YouTube Connection
```http
POST /api/test/youtube
```

**Response:**
```json
{
  "success": true,
  "message": "YouTube API connection successful"
}
```

### Test Substack Connection
```http
POST /api/test/substack
```

**Response:**
```json
{
  "success": true,
  "message": "Substack RSS feeds accessible"
}
```

## Error Handling

### Standard Error Response
```json
{
  "error": "Error message",
  "message": "Detailed error description"
}
```

### Common HTTP Status Codes
- `200` - Success
- `400` - Bad Request (invalid parameters)
- `401` - Unauthorized (authentication required)
- `404` - Not Found (resource doesn't exist)
- `500` - Internal Server Error

## Rate Limiting

API endpoints are rate-limited to prevent abuse:
- Search endpoints: 10 requests per minute
- Test endpoints: 5 requests per minute
- Admin endpoints: 20 requests per minute

## Data Validation

All API endpoints use Zod schemas for request validation. Invalid requests will return a 400 error with validation details.

## CORS Configuration

The API supports CORS for all origins in development. In production, configure allowed origins in the server configuration.