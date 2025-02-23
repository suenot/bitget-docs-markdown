# WebSocket Private Data API

## Overview

The WebSocket private data API provides real-time updates for account information, orders, and trades. This connection requires authentication and provides personalized data streams.

## Authentication

1. Generate authentication parameters:
```python
timestamp = int(time.time() * 1000)
message = f"{timestamp}GET/user/verify"
signature = hmac.new(
    secretKey.encode('utf-8'),
    message.encode('utf-8'),
    hashlib.sha256
).hexdigest()
```

2. Send login request:
```json
{
    "op": "login",
    "args": [{
        "apiKey": "your_api_key",
        "passphrase": "your_passphrase",
        "timestamp": "1621441751927",
        "sign": "generated_signature"
    }]
}
```

## Connection

### WebSocket Endpoint

```
wss://ws.bitget.com/spot/v1/stream
```

### Connection Lifecycle

1. **Connect**
```javascript
const ws = new WebSocket('wss://ws.bitget.com/spot/v1/stream');
```

2. **Login**
```javascript
ws.onopen = () => {
    // Send login request
    ws.send(JSON.stringify({
        op: "login",
        args: [{
            apiKey: "your_api_key",
            passphrase: "your_passphrase",
            timestamp: Date.now(),
            sign: "generated_signature"
        }]
    }));
};
```

3. **Subscribe**
```javascript
ws.send(JSON.stringify({
    op: "subscribe",
    args: [{
        channel: "account",
        instType: "SPOT"
    }]
}));
```

4. **Heartbeat**
```javascript
setInterval(() => {
    ws.send(JSON.stringify({
        op: "ping"
    }));
}, 20000);
```

## Available Channels

### Account Updates

Subscribe to account balance updates.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "account",
        "instType": "SPOT"
    }]
}
```

#### Update Message
```json
{
    "action": "snapshot",
    "arg": {
        "channel": "account",
        "instType": "SPOT"
    },
    "data": [{
        "coinId": "BTC",
        "available": "1.23456789",
        "frozen": "0.12345678",
        "locked": "0",
        "btcValue": "1.23456789",
        "usdValue": "45678.12",
        "timestamp": 1621441751927
    }]
}
```

### Order Updates

Subscribe to order status updates.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "orders",
        "instType": "SPOT",
        "instId": "BTCUSDT"  // Optional
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "orders",
        "instType": "SPOT",
        "instId": "BTCUSDT"
    },
    "data": [{
        "orderId": "123456789",
        "symbol": "BTCUSDT",
        "side": "buy",
        "orderType": "limit",
        "price": "30000",
        "quantity": "0.001",
        "remainingQuantity": "0.0005",
        "status": "partially_filled",
        "timestamp": 1621441751927
    }]
}
```

### Trade Updates

Subscribe to trade execution updates.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "trades",
        "instType": "SPOT",
        "instId": "BTCUSDT"  // Optional
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "trades",
        "instType": "SPOT",
        "instId": "BTCUSDT"
    },
    "data": [{
        "tradeId": "123456789",
        "orderId": "123456789",
        "symbol": "BTCUSDT",
        "side": "buy",
        "price": "30000",
        "quantity": "0.0005",
        "fee": "0.00001",
        "feeCurrency": "BTC",
        "timestamp": 1621441751927
    }]
}
```

### Position Updates (Margin)

Subscribe to margin position updates.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "positions",
        "instType": "MARGIN",
        "instId": "BTCUSDT"  // Optional
    }]
}
```

#### Update Message
```json
{
    "action": "snapshot",
    "arg": {
        "channel": "positions",
        "instType": "MARGIN",
        "instId": "BTCUSDT"
    },
    "data": [{
        "symbol": "BTCUSDT",
        "positionAmt": "0.5",
        "entryPrice": "30000",
        "markPrice": "31000",
        "unRealizedProfit": "500",
        "liquidationPrice": "25000",
        "leverage": "3",
        "marginType": "isolated",
        "timestamp": 1621441751927
    }]
}
```

## Error Handling

### Error Response Format
```json
{
    "event": "error",
    "code": "40001",
    "msg": "Invalid signature"
}
```

### Common Error Codes

| Code   | Message | Description |
|--------|---------|-------------|
| 40001  | Invalid signature | Authentication failed |
| 40002  | Connection limit exceeded | Too many connections |
| 40003  | Invalid subscription | Channel or parameters invalid |
| 40004  | Rate limit exceeded | Too many messages |
| 40005  | Not logged in | Authentication required |

## Best Practices

1. **Connection Management**
```javascript
class WebSocketManager {
    constructor() {
        this.ws = null;
        this.pingInterval = null;
        this.reconnectAttempts = 0;
        this.maxReconnectAttempts = 5;
    }

    connect() {
        this.ws = new WebSocket('wss://ws.bitget.com/spot/v1/stream');
        
        this.ws.onopen = () => {
            this.reconnectAttempts = 0;
            this.login();
            this.startHeartbeat();
        };

        this.ws.onclose = () => {
            this.handleDisconnect();
        };

        this.ws.onerror = (error) => {
            console.error('WebSocket error:', error);
        };
    }

    handleDisconnect() {
        if (this.reconnectAttempts < this.maxReconnectAttempts) {
            setTimeout(() => {
                this.reconnectAttempts++;
                this.connect();
            }, 5000 * Math.pow(2, this.reconnectAttempts));
        }
    }

    startHeartbeat() {
        this.pingInterval = setInterval(() => {
            if (this.ws.readyState === WebSocket.OPEN) {
                this.ws.send(JSON.stringify({ op: "ping" }));
            }
        }, 20000);
    }
}
```

2. **Subscription Management**
```javascript
class SubscriptionManager {
    constructor(ws) {
        this.ws = ws;
        this.subscriptions = new Set();
    }

    subscribe(channel, instType, instId = null) {
        const subscription = {
            channel,
            instType,
            ...(instId && { instId })
        };

        if (!this.subscriptions.has(JSON.stringify(subscription))) {
            this.ws.send(JSON.stringify({
                op: "subscribe",
                args: [subscription]
            }));
            this.subscriptions.add(JSON.stringify(subscription));
        }
    }

    resubscribeAll() {
        this.subscriptions.forEach(sub => {
            this.ws.send(JSON.stringify({
                op: "subscribe",
                args: [JSON.parse(sub)]
            }));
        });
    }
}
```

3. **Message Processing**
```javascript
class MessageProcessor {
    processMessage(message) {
        try {
            const data = JSON.parse(message);
            
            switch (data.action) {
                case 'snapshot':
                    this.handleSnapshot(data);
                    break;
                case 'update':
                    this.handleUpdate(data);
                    break;
                case 'error':
                    this.handleError(data);
                    break;
            }
        } catch (error) {
            console.error('Message processing error:', error);
        }
    }

    handleSnapshot(data) {
        // Initialize or reset state with snapshot data
    }

    handleUpdate(data) {
        // Update existing state with incremental changes
    }

    handleError(data) {
        // Handle error conditions
        console.error(`WebSocket error: ${data.msg} (${data.code})`);
    }
}
```

4. **Rate Limiting**
```javascript
class RateLimiter {
    constructor(limit, interval) {
        this.limit = limit;
        this.interval = interval;
        this.requests = [];
    }

    canMakeRequest() {
        const now = Date.now();
        this.requests = this.requests.filter(
            time => time > now - this.interval
        );
        
        if (this.requests.length < this.limit) {
            this.requests.push(now);
            return true;
        }
        return false;
    }
}
```

## Additional Resources

- [WebSocket API Documentation](https://bitgetlimited.github.io/apidoc/en/spot/#websocket-api)
- [Error Code Reference](https://bitgetlimited.github.io/apidoc/en/spot/#error-codes)
- [Rate Limits](https://bitgetlimited.github.io/apidoc/en/spot/#rate-limits)
