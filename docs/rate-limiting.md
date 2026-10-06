# Rate Limiting - Rate Limits

## Overview

The Routing API uses rate limiting to ensure fair usage and maintain service quality.

## Limits by Tier

| Tier | Requests/Min | Requests/Day | Burst |
|------|-------------|-------------|-------|
| Starter | 60 | 10,000 | 100 |
| Growth | 300 | 100,000 | 500 |
| Enterprise | Unlimited | Unlimited | Unlimited |

## Rate Limit Headers

All responses include rate limit information:

```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 55
X-RateLimit-Reset: 1609459200
```

- **Limit**: Requests allowed per minute
- **Remaining**: Requests left in current window
- **Reset**: Unix timestamp when limit resets

## Handling Rate Limits

When you exceed the limit, you'll get:

```
HTTP 429 Too Many Requests

{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "You have exceeded your rate limit. Please retry after 60 seconds."
  }
}
```

## Implementing Backoff

### JavaScript

```javascript
async function makeRequest(fn, maxRetries = 5) {
  for (let attempt = 0; attempt < maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      if (error.status === 429) {
        const resetTime = parseInt(error.headers['x-ratelimit-reset']);
        const waitTime = (resetTime * 1000) - Date.now();
        
        if (attempt < maxRetries - 1) {
          console.log(`Rate limited. Waiting ${waitTime}ms...`);
          await new Promise(r => setTimeout(r, waitTime));
          continue;
        }
      }
      throw error;
    }
  }
}

// Usage
const result = await makeRequest(() => client.routePayment({
  amount: 10000,
  currency: 'USD',
  destination: 'US'
}));
```

### Python

```python
import time

def make_request(fn, max_retries=5):
    for attempt in range(max_retries):
        try:
            return fn()
        except RoutingAPIError as e:
            if e.status == 429:
                reset_time = int(e.headers.get('x-ratelimit-reset', 0))
                wait_time = (reset_time * 1000 - time.time() * 1000) / 1000
                
                if attempt < max_retries - 1:
                    print(f'Rate limited. Waiting {wait_time}s...')
                    time.sleep(wait_time)
                    continue
            
            raise
    
    return None

# Usage
result = make_request(lambda: client.route_payment(
    amount=10000,
    currency='USD',
    destination='US'
))
```

## Best Practices

1. **Cache results** - Don't re-route the same payment
2. **Batch requests** - Combine multiple operations
3. **Implement exponential backoff** - Start with 1s, double each retry
4. **Monitor headers** - Check remaining requests
5. **Upgrade tier** - If frequently hitting limits

## Caching Strategy

```javascript
const cache = new Map();

async function routePaymentWithCache(paymentDetails) {
  const key = JSON.stringify(paymentDetails);
  
  if (cache.has(key)) {
    return cache.get(key);
  }
  
  const result = await client.routePayment(paymentDetails);
  
  // Cache for 1 hour
  cache.set(key, result);
  setTimeout(() => cache.delete(key), 60 * 60 * 1000);
  
  return result;
}
```

## Burst Limit

In addition to per-minute limits, there's a burst limit:

- You can exceed per-minute limit for short bursts
- Burst limit is about 2-3x your per-minute limit
- If you exceed burst, you get rate limited

## Status Endpoint

Check your rate limit status anytime:

```
GET /status
```

Response:
```json
{
  "status": "ok",
  "rateLimit": {
    "limit": 60,
    "remaining": 55,
    "resetAt": "2024-01-01T00:01:00Z"
  }
}
```

## Support

If you need higher limits:

1. Upgrade your plan
2. Contact support@webundle.org
3. Request rate limit increase

We offer enterprise plans with custom limits.
