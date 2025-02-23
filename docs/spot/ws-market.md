# WebSocket Market Data Stream

## Overview

WebSocket is a protocol providing full-duplex communication channels over a single TCP connection. Using WebSocket API, you can receive real-time market data updates.

## Connection

### WebSocket Endpoint

```
wss://ws.bitget.com/spot/v1/stream
```

### Connection Management

- The client should send ping frame every 20 seconds
- Server will respond with pong frame
- Connection will be closed if no ping is received for 30 seconds

## Subscription

After establishing a connection, send a subscription message:

```json
{
    "op": "subscribe",
    "args": [{
        "instType": "SPOT",
        "channel": "ticker",
        "instId": "BTCUSDT"
    }]
}
```

### Available Channels

1. Ticker
2. Order Book
3. Trades
4. Candlestick Data

## Ticker Channel

Real-time price updates for a symbol.

### Subscribe

```json
{
    "op": "subscribe",
    "args": [{
        "instType": "SPOT",
        "channel": "ticker",
        "instId": "BTCUSDT"
    }]
}
```

### Response

```json
{
    "action": "snapshot",
    "arg": {
        "instType": "SPOT",
        "channel": "ticker",
        "instId": "BTCUSDT"
    },
    "data": [{
        "symbol": "BTCUSDT",
        "high24h": "40000",
        "low24h": "38000",
        "close": "39000",
        "quoteVol": "1000000",
        "baseVol": "26.5",
        "usdtVol": "1000000",
        "bestBid": "38999",
        "bestAsk": "39001",
        "timestamp": 1621441751927
    }]
}
```

## Order Book Channel

Real-time order book updates.

### Subscribe

```json
{
    "op": "subscribe",
    "args": [{
        "instType": "SPOT",
        "channel": "books",
        "instId": "BTCUSDT"
    }]
}
```

### Response

First message is a snapshot:

```json
{
    "action": "snapshot",
    "arg": {
        "instType": "SPOT",
        "channel": "books",
        "instId": "BTCUSDT"
    },
    "data": [{
        "asks": [
            ["39001.5", "0.5"],
            ["39002.0", "0.8"]
        ],
        "bids": [
            ["39000.5", "0.3"],
            ["39000.0", "1.2"]
        ],
        "timestamp": 1621441751927
    }]
}
```

Subsequent messages are updates:

```json
{
    "action": "update",
    "arg": {
        "instType": "SPOT",
        "channel": "books",
        "instId": "BTCUSDT"
    },
    "data": [{
        "asks": [
            ["39001.5", "0.6"]
        ],
        "bids": [
            ["39000.5", "0.4"]
        ],
        "timestamp": 1621441751928
    }]
}
```

## Trades Channel

Real-time trade updates.

### Subscribe

```json
{
    "op": "subscribe",
    "args": [{
        "instType": "SPOT",
        "channel": "trade",
        "instId": "BTCUSDT"
    }]
}
```

### Response

```json
{
    "action": "update",
    "arg": {
        "instType": "SPOT",
        "channel": "trade",
        "instId": "BTCUSDT"
    },
    "data": [{
        "price": "39000.5",
        "size": "0.1",
        "side": "buy",
        "timestamp": 1621441751927
    }]
}
```

## Candlestick Channel

Real-time candlestick data updates.

### Subscribe

```json
{
    "op": "subscribe",
    "args": [{
        "instType": "SPOT",
        "channel": "candle1m",
        "instId": "BTCUSDT"
    }]
}
```

Available intervals: 1m, 5m, 15m, 30m, 1H, 4H, 12H, 1D, 1W

### Response

```json
{
    "action": "update",
    "arg": {
        "instType": "SPOT",
        "channel": "candle1m",
        "instId": "BTCUSDT"
    },
    "data": [[
        1621441751927, // timestamp
        "39000.5",     // open
        "39100.0",     // high
        "38900.0",     // low
        "39050.0",     // close
        "100.5",       // volume
        "3920000.0"    // quote volume
    ]]
}
```

## Error Handling

### Error Response Format

```json
{
    "event": "error",
    "code": 30001,
    "msg": "Invalid subscription"
}
```

### Common Error Codes

| Code  | Description |
|-------|-------------|
| 30001 | Invalid subscription request |
| 30002 | Invalid symbol |
| 30003 | Connection limit exceeded |
| 30004 | Rate limit exceeded |
| 30005 | Internal error |

## Best Practices

1. Implement heartbeat mechanism
2. Handle reconnection with exponential backoff
3. Maintain a local order book copy
4. Process order book updates sequentially
5. Validate sequence numbers for order book updates
6. Handle error messages appropriately
