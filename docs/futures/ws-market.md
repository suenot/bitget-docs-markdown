# WebSocket Market Data API

## Overview

The WebSocket Market Data API provides real-time market data for futures contracts, including tickers, order book updates, trades, and candlestick data.

## Connection

### WebSocket Endpoint

```
wss://ws.bitget.com/mix/v1/stream
```

### Connection Lifecycle

1. **Connect**
```javascript
const ws = new WebSocket('wss://ws.bitget.com/mix/v1/stream');
```

2. **Subscribe**
```javascript
ws.send(JSON.stringify({
    op: "subscribe",
    args: [{
        channel: "ticker",
        instId: "BTCUSDT"
    }]
}));
```

3. **Heartbeat**
```javascript
setInterval(() => {
    ws.send(JSON.stringify({
        op: "ping"
    }));
}, 20000);
```

## Available Channels

### Ticker Channel

Subscribe to 24-hour ticker data.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "ticker",
        "instId": "BTCUSDT"
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "ticker",
        "instId": "BTCUSDT"
    },
    "data": [{
        "instId": "BTCUSDT",
        "last": "30000.50",
        "lastSz": "0.123",
        "askPx": "30001.00",
        "askSz": "0.456",
        "bidPx": "30000.00",
        "bidSz": "0.789",
        "open24h": "29000.00",
        "high24h": "31000.00",
        "low24h": "28500.00",
        "vol24h": "1234.567",
        "volCcy24h": "36789123.45",
        "ts": 1621441751927
    }]
}
```

### Order Book Channel

Subscribe to order book data.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "books",
        "instId": "BTCUSDT"
    }]
}
```

#### Snapshot Message
```json
{
    "action": "snapshot",
    "arg": {
        "channel": "books",
        "instId": "BTCUSDT"
    },
    "data": [{
        "asks": [
            ["30001.00", "0.456"],  // [price, size]
            ["30001.50", "0.789"]
        ],
        "bids": [
            ["30000.50", "0.123"],
            ["30000.00", "0.456"]
        ],
        "ts": 1621441751927
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "books",
        "instId": "BTCUSDT"
    },
    "data": [{
        "asks": [
            ["30001.00", "0.567"]  // Updated size
        ],
        "bids": [
            ["30000.50", "0"]  // Size 0 means price level removed
        ],
        "ts": 1621441751927
    }]
}
```

### Trades Channel

Subscribe to real-time trades.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "trades",
        "instId": "BTCUSDT"
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "trades",
        "instId": "BTCUSDT"
    },
    "data": [{
        "instId": "BTCUSDT",
        "tradeId": "123456789",
        "px": "30000.50",
        "sz": "0.123",
        "side": "buy",
        "ts": 1621441751927
    }]
}
```

### Candlestick Channel

Subscribe to candlestick data.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "candle1m",  // Available intervals: 1m,5m,15m,30m,1h,4h,12h,1d,1w
        "instId": "BTCUSDT"
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "candle1m",
        "instId": "BTCUSDT"
    },
    "data": [[
        1621441751927,  // timestamp
        "30000.00",     // open
        "30100.00",     // high
        "29900.00",     // low
        "30050.00",     // close
        "123.456",      // volume
        "3698547.89"    // quote volume
    ]]
}
```

### Mark Price Channel

Subscribe to mark price updates.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "mark_price",
        "instId": "BTCUSDT"
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "mark_price",
        "instId": "BTCUSDT"
    },
    "data": [{
        "instId": "BTCUSDT",
        "markPx": "30001.00",
        "indexPx": "30002.00",
        "fundingRate": "0.0001",
        "nextFundingTime": 1621441751927,
        "ts": 1621441751927
    }]
}
```

## Error Handling

### Error Response Format
```json
{
    "event": "error",
    "code": "50001",
    "msg": "Invalid subscription"
}
```

### Common Error Codes

| Code   | Message | Description |
|--------|---------|-------------|
| 50001  | Invalid subscription | Channel or parameters invalid |
| 50002  | Connection limit exceeded | Too many connections |
| 50003  | Rate limit exceeded | Too many messages |
| 50004  | Invalid message | Message format error |

## Best Practices

1. **Order Book Management**
```javascript
class OrderBookManager {
    constructor() {
        this.orderBook = {
            asks: new Map(),
            bids: new Map()
        };
        this.lastUpdateId = 0;
    }

    handleSnapshot(data) {
        // Clear existing order book
        this.orderBook.asks.clear();
        this.orderBook.bids.clear();

        // Process snapshot
        data.asks.forEach(([price, size]) => {
            this.orderBook.asks.set(price, size);
        });
        data.bids.forEach(([price, size]) => {
            this.orderBook.bids.set(price, size);
        });

        this.lastUpdateId = data.ts;
    }

    handleUpdate(data) {
        if (data.ts <= this.lastUpdateId) {
            return; // Skip old updates
        }

        // Process updates
        data.asks.forEach(([price, size]) => {
            if (size === "0") {
                this.orderBook.asks.delete(price);
            } else {
                this.orderBook.asks.set(price, size);
            }
        });

        data.bids.forEach(([price, size]) => {
            if (size === "0") {
                this.orderBook.bids.delete(price);
            } else {
                this.orderBook.bids.set(price, size);
            }
        });

        this.lastUpdateId = data.ts;
    }

    getTopOfBook() {
        const asks = Array.from(this.orderBook.asks.entries())
            .sort((a, b) => parseFloat(a[0]) - parseFloat(b[0]));
        const bids = Array.from(this.orderBook.bids.entries())
            .sort((a, b) => parseFloat(b[0]) - parseFloat(a[0]));

        return {
            bestAsk: asks[0],
            bestBid: bids[0]
        };
    }
}
```

2. **Connection Management**
```javascript
class WebSocketManager {
    constructor() {
        this.ws = null;
        this.pingInterval = null;
        this.reconnectAttempts = 0;
        this.maxReconnectAttempts = 5;
        this.subscriptions = new Set();
    }

    connect() {
        this.ws = new WebSocket('wss://ws.bitget.com/mix/v1/stream');
        
        this.ws.onopen = () => {
            this.reconnectAttempts = 0;
            this.startHeartbeat();
            this.resubscribe();
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

    subscribe(channel, instId) {
        const subscription = JSON.stringify({ channel, instId });
        if (!this.subscriptions.has(subscription)) {
            this.subscriptions.add(subscription);
            if (this.ws.readyState === WebSocket.OPEN) {
                this.ws.send(JSON.stringify({
                    op: "subscribe",
                    args: [{ channel, instId }]
                }));
            }
        }
    }

    resubscribe() {
        this.subscriptions.forEach(sub => {
            const { channel, instId } = JSON.parse(sub);
            this.ws.send(JSON.stringify({
                op: "subscribe",
                args: [{ channel, instId }]
            }));
        });
    }
}
```

3. **Data Processing**
```javascript
class MarketDataProcessor {
    constructor() {
        this.orderBookManager = new OrderBookManager();
        this.trades = [];
        this.maxTradesHistory = 1000;
    }

    processTicker(data) {
        // Process and store ticker data
        this.lastTicker = data;
        this.calculateMetrics();
    }

    processTrades(trades) {
        // Add new trades to history
        this.trades.push(...trades);
        if (this.trades.length > this.maxTradesHistory) {
            this.trades = this.trades.slice(-this.maxTradesHistory);
        }
        this.analyzeTradeFlow();
    }

    calculateMetrics() {
        if (!this.lastTicker) return;
        
        const priceChange = parseFloat(this.lastTicker.last) - 
                           parseFloat(this.lastTicker.open24h);
        const priceChangePercent = (priceChange / parseFloat(this.lastTicker.open24h)) * 100;
        
        return {
            priceChange,
            priceChangePercent,
            volume24h: parseFloat(this.lastTicker.vol24h),
            volatility: this.calculateVolatility()
        };
    }
}
```

## Additional Resources

- [WebSocket API Documentation](https://bitgetlimited.github.io/apidoc/en/mix/#websocket-market-data)
- [Rate Limits](https://bitgetlimited.github.io/apidoc/en/mix/#rate-limits)
- [Error Codes](https://bitgetlimited.github.io/apidoc/en/mix/#error-codes)
