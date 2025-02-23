# Rate Limits

## Overview

Bitget implements rate limiting to ensure the stability and fairness of our API services. Rate limits are applied based on:
- API Key
- IP Address
- Endpoint Type

## General Limits

### REST API

| Access Type | Limit Type | Limit |
|------------|------------|-------|
| Public | IP-based | 20 requests per second |
| Private | API Key-based | 30 requests per second |

### WebSocket API

| Access Type | Limit Type | Limit |
|------------|------------|-------|
| Public | Connections per IP | 50 connections |
| Private | Connections per API Key | 20 connections |
| All | Subscriptions per Connection | 240 channels |

## Endpoint-Specific Limits

### Spot Trading

| Endpoint | Method | Limit | Period |
|----------|--------|-------|--------|
| Order placement | POST | 100 | 1 second |
| Order cancellation | POST | 100 | 1 second |
| Order query | GET | 60 | 1 second |
| Trade history | GET | 10 | 1 second |

### Futures Trading

| Endpoint | Method | Limit | Period |
|----------|--------|-------|--------|
| Order placement | POST | 100 | 1 second |
| Order cancellation | POST | 100 | 1 second |
| Position query | GET | 60 | 1 second |
| Position history | GET | 10 | 1 second |

### Market Data

| Endpoint | Method | Limit | Period |
|----------|--------|-------|--------|
| Ticker | GET | 50 | 1 second |
| Order book | GET | 30 | 1 second |
| Kline/Candlestick | GET | 20 | 1 second |
| Recent trades | GET | 20 | 1 second |

## Rate Limit Headers

The API returns the following headers with rate limit information:

```
X-RateLimit-Limit: Maximum requests allowed
X-RateLimit-Remaining: Remaining requests in the current period
X-RateLimit-Reset: Time until the rate limit resets (Unix timestamp)
```

Example:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 86
X-RateLimit-Reset: 1621441751
```

## Rate Limit Error Response

When rate limit is exceeded, the API returns:

```json
{
    "code": "00003",
    "msg": "Rate limit exceeded",
    "requestTime": 1621441751927
}
```

## Best Practices

### 1. Implement Rate Limiting in Your Application

```python
import time
from collections import deque

class RateLimiter:
    def __init__(self, max_requests, period):
        self.max_requests = max_requests
        self.period = period
        self.requests = deque()

    def can_make_request(self):
        now = time.time()
        
        # Remove old requests
        while self.requests and self.requests[0] <= now - self.period:
            self.requests.popleft()

        # Check if we can make a new request
        if len(self.requests) < self.max_requests:
            self.requests.append(now)
            return True
        return False

# Usage
limiter = RateLimiter(max_requests=20, period=1)
if limiter.can_make_request():
    # Make API request
    pass
else:
    # Wait or handle rate limit
    pass
```

### 2. Use WebSocket for Real-Time Data

Instead of polling REST endpoints, use WebSocket for:
- Market data updates
- Order updates
- Position updates
- Account updates

```python
# WebSocket subscription example
ws_client.subscribe([{
    "instType": "SPOT",
    "channel": "ticker",
    "instId": "BTCUSDT"
}])
```

### 3. Implement Retry Logic with Backoff

```python
import time
from functools import wraps

def retry_with_backoff(retries=3, backoff_in_seconds=1):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            x = 0
            while True:
                try:
                    return func(*args, **kwargs)
                except RateLimitException as e:
                    if x == retries:
                        raise e
                    sleep = (backoff_in_seconds * 2 ** x)
                    time.sleep(sleep)
                    x += 1
        return wrapper
    return decorator

@retry_with_backoff(retries=3)
def make_api_request():
    # API request code here
    pass
```

### 4. Batch Operations

When possible, use batch operations:
- Place multiple orders in one request
- Cancel multiple orders in one request
- Query multiple symbols in one request

### 5. Cache Frequently Accessed Data

Cache data that doesn't need to be real-time:
- Trading pairs information
- Market statistics
- Historical data

```python
from functools import lru_cache
from datetime import datetime, timedelta

@lru_cache(maxsize=100)
def get_trading_pairs():
    # API request for trading pairs
    pass

# Cache will be valid for 1 hour
def get_cached_data(cache_key, ttl_seconds=3600):
    now = datetime.now()
    if cache_key in cache:
        if cache[cache_key]['timestamp'] + timedelta(seconds=ttl_seconds) > now:
            return cache[cache_key]['data']
    # Get fresh data if cache missing or expired
    data = get_fresh_data()
    cache[cache_key] = {
        'data': data,
        'timestamp': now
    }
    return data
```

## Monitoring and Alerts

1. Monitor rate limit headers
2. Set up alerts for rate limit warnings
3. Track rate limit usage patterns
4. Adjust request rates based on monitoring

## Common Issues and Solutions

### High Rate Limit Usage

1. **Problem**: Frequent polling of market data
   **Solution**: Use WebSocket API

2. **Problem**: Individual order operations
   **Solution**: Use batch operations

3. **Problem**: Redundant data requests
   **Solution**: Implement caching

### Rate Limit Errors

1. **Problem**: Sudden spike in requests
   **Solution**: Implement request queuing

2. **Problem**: Multiple instances without coordination
   **Solution**: Use centralized rate limiting

3. **Problem**: Inefficient retry logic
   **Solution**: Implement exponential backoff
