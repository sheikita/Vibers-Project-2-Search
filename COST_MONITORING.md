# Cost Monitoring Guide

## Overview

Sheikita's Search Page includes comprehensive cost monitoring to track API usage, expenses, and performance metrics across all external services.

## Features

### Real-Time Tracking
- **API Usage Monitoring**: Track every API call with timestamps
- **Cost Calculation**: Accurate cost tracking for all services
- **Performance Metrics**: Response time and success rate monitoring
- **Automated Alerts**: Threshold-based notifications

### Supported Services
- **OpenAI**: Token usage and cost tracking
- **YouTube API**: Quota usage monitoring
- **RSS Feeds**: Request tracking
- **Search Operations**: Internal operation costs

## Admin Dashboard Access

### Authentication Required
Cost monitoring is admin-only to protect sensitive financial data.

#### Initial Setup
1. Visit `/admin-login`
2. Click "Initialize Admin User"
3. Default credentials: `admin` / `admin123`
4. Change credentials after first login

#### Accessing Dashboard
1. Login at `/admin-login`
2. Navigate to `/cost-dashboard`
3. Session expires after 24 hours

### Dashboard Components

#### Summary Section
- **Total Cost**: Cumulative spending across all services
- **Total Requests**: Number of API calls made
- **Average Daily Cost**: Cost per day over selected period
- **Cost Trend**: Percentage change from previous period

#### Service Breakdown
- **OpenAI**: Token usage and costs
- **YouTube**: API quota usage
- **RSS Feeds**: Request counts
- **Search**: Internal operations

#### Daily Summaries
- **Cost per Day**: Historical daily spending
- **Request Volume**: API calls per day
- **Success Rate**: Percentage of successful requests
- **Average Response Time**: Performance metrics

## Cost Calculation

### OpenAI Pricing
```typescript
// GPT-4o pricing (as of 2025)
const INPUT_COST_PER_1K_TOKENS = 0.0025;  // $0.0025 per 1K input tokens
const OUTPUT_COST_PER_1K_TOKENS = 0.01;   // $0.01 per 1K output tokens

function calculateOpenAICost(inputTokens: number, outputTokens: number): number {
  const inputCost = (inputTokens / 1000) * INPUT_COST_PER_1K_TOKENS;
  const outputCost = (outputTokens / 1000) * OUTPUT_COST_PER_1K_TOKENS;
  return inputCost + outputCost;
}
```

### YouTube API Costs
YouTube API uses quota units, not direct costs:
- Search operation: 100 quota units
- Daily quota: 10,000 units (free tier)
- Overage: Contact Google for pricing

### RSS Feed Costs
RSS feeds are generally free but tracked for performance:
- Request count
- Response time
- Success rate

## Implementation

### Cost Monitor Service
```typescript
// server/services/costMonitor.ts
class CostMonitor {
  async logAPIUsage(usage: Omit<APIUsage, 'timestamp' | 'requestId'>) {
    const fullUsage: APIUsage = {
      ...usage,
      timestamp: new Date().toISOString(),
      requestId: this.generateRequestId()
    };
    
    // Log to file
    await this.writeToLogFile(fullUsage);
    
    // Update daily summary
    await this.updateDailySummary(fullUsage);
    
    // Check for alerts
    await this.checkCostAlerts();
  }
}
```

### Usage Tracking
```typescript
// Example: Track OpenAI usage
await costMonitor.logAPIUsage({
  service: 'openai',
  operation: 'generate_summary',
  tokensUsed: 150,
  estimatedCost: 0.015,
  category: 'tech',
  success: true,
  responseTime: 1200
});
```

## Data Storage

### Log Files
Cost data is stored in JSON files:
- `logs/api-usage.json`: Individual API calls
- `logs/daily-summary.json`: Daily aggregated data

### Log Structure
```typescript
interface APIUsage {
  timestamp: string;
  service: 'openai' | 'youtube' | 'google_news_rss' | 'substack_rss';
  operation: string;
  tokensUsed?: number;
  estimatedCost: number;
  requestId: string;
  category?: string;
  success: boolean;
  responseTime: number;
}
```

### Daily Summary
```typescript
interface DailySummary {
  date: string;
  totalCost: number;
  totalRequests: number;
  serviceBreakdown: Record<string, { requests: number; cost: number }>;
  averageResponseTime: number;
  successRate: number;
}
```

## Alerts and Thresholds

### Automated Alerts
The system monitors for:
- **Daily Cost Threshold**: Alert when daily costs exceed $5
- **High Failure Rate**: Alert when failure rate exceeds 10%
- **Performance Issues**: Alert when response time exceeds 5 seconds

### Alert Configuration
```typescript
async checkCostAlerts() {
  const todaySummary = await this.getTodaySummary();
  
  if (todaySummary.totalCost > 5.0) {
    console.warn(`Daily cost threshold exceeded: $${todaySummary.totalCost}`);
  }
  
  if (todaySummary.successRate < 0.9) {
    console.warn(`High failure rate: ${todaySummary.successRate * 100}%`);
  }
}
```

## Optimization Recommendations

### Automatic Recommendations
The system provides optimization suggestions:
- **Caching**: Implement caching for frequently requested categories
- **Batching**: Batch API requests where possible
- **Rate Limiting**: Implement rate limiting to prevent overuse
- **Error Handling**: Improve error handling to reduce failed requests

### Cost Reduction Strategies
1. **Implement Caching**
   - Cache search results for similar queries
   - Cache API responses temporarily
   - Use CDN for static content

2. **Optimize API Usage**
   - Reduce token usage in OpenAI prompts
   - Implement request deduplication
   - Use more efficient API endpoints

3. **Monitor and Alert**
   - Set up cost alerts
   - Monitor usage patterns
   - Identify cost spikes early

## API Endpoints

### Dashboard Data
```http
GET /api/cost/dashboard
Authorization: Bearer <admin_token>
```

### Recent Usage
```http
GET /api/cost/usage?limit=50
Authorization: Bearer <admin_token>
```

### Daily Summary
```http
GET /api/cost/summary?days=7
Authorization: Bearer <admin_token>
```

## Security Considerations

### Admin-Only Access
- All cost monitoring endpoints require admin authentication
- Session-based authentication with 24-hour expiration
- Secure session storage in PostgreSQL

### Data Protection
- Cost data is sensitive financial information
- Logged data includes no personal information
- Regular cleanup of old log files

## Troubleshooting

### Common Issues

#### Missing Cost Data
```bash
# Check log files exist
ls -la logs/

# Check file permissions
chmod 644 logs/api-usage.json
```

#### Authentication Issues
```bash
# Reset admin session
curl -X POST http://localhost:5000/api/admin/logout

# Re-initialize admin user
curl -X POST http://localhost:5000/api/admin/init \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"admin123"}'
```

#### Dashboard Not Loading
1. Check admin authentication
2. Verify log files exist
3. Check server logs for errors
4. Ensure PostgreSQL is running

### Debug Mode
Enable debug logging:
```env
NODE_ENV=development
DEBUG=cost-monitor:*
```

## Maintenance

### Log Rotation
Implement log rotation to prevent disk space issues:
```typescript
// Rotate logs monthly
if (currentMonth !== lastRotationMonth) {
  await this.rotateLogFiles();
}
```

### Data Cleanup
- Archive old usage data
- Cleanup expired sessions
- Optimize log file size

### Performance Monitoring
- Monitor dashboard load times
- Check database query performance
- Optimize JSON file operations

## Future Enhancements

### Planned Features
- **Email Alerts**: Send cost alerts via email
- **Slack Integration**: Post cost alerts to Slack
- **Budget Management**: Set monthly budget limits
- **Detailed Analytics**: Advanced usage analytics
- **Cost Predictions**: Predict future costs based on usage patterns

### Integration Options
- **Stripe**: For payment processing
- **AWS CloudWatch**: For advanced monitoring
- **DataDog**: For comprehensive analytics
- **PagerDuty**: For alert management

## Best Practices

### Cost Management
1. Set reasonable daily/monthly budgets
2. Monitor usage patterns regularly
3. Implement caching strategies
4. Review and optimize API usage

### Security
1. Restrict admin access
2. Use secure session management
3. Regularly rotate admin credentials
4. Monitor for unauthorized access

### Performance
1. Optimize dashboard queries
2. Implement efficient logging
3. Use appropriate data structures
4. Regular maintenance tasks