# Node.js SDK for Bitget API v3

## Requirements

- Node.js version 12 or higher
- npm (Node Package Manager)

## Installation

1. Download the SDK
```bash
git clone https://github.com/BitgetLimited/v3-bitget-api-sdk.git
cd v3-bitget-api-sdk/bitget-node-sdk-api
```

2. Install dependencies
```bash
npm install
```

## Configuration

1. [Create API Keys](https://www.bitget.com/account/newapi) if you don't have them yet

2. Configure your API credentials:
```javascript
const apiKey = 'your_api_key';
const secretKey = 'your_secret_key';
const passphrase = 'your_passphrase';
```

## Usage Examples

### REST API Example

```javascript
const { SpotClient } = require('bitget-api');

// Initialize client
const client = new SpotClient({
    apiKey: 'your_api_key',
    secretKey: 'your_secret_key',
    passphrase: 'your_passphrase'
});

// Place an order
async function placeOrder() {
    try {
        const order = await client.submitOrder({
            symbol: 'BTCUSDT',
            side: 'buy',
            orderType: 'limit',
            force: 'gtc',
            price: '30000',
            quantity: '0.001'
        });
        console.log('Order placed:', order);
    } catch (error) {
        console.error('Error placing order:', error);
    }
}

// Get account information
async function getAccountInfo() {
    try {
        const account = await client.getAccountInfo();
        console.log('Account info:', account);
    } catch (error) {
        console.error('Error getting account info:', error);
    }
}

// Get market data
async function getMarketData() {
    try {
        const ticker = await client.getTicker('BTCUSDT');
        console.log('Ticker:', ticker);
    } catch (error) {
        console.error('Error getting ticker:', error);
    }
}
```

### WebSocket API Example

```javascript
const { WebsocketClient } = require('bitget-api');

// Initialize WebSocket client
const wsClient = new WebsocketClient({
    apiKey: 'your_api_key',
    secretKey: 'your_secret_key',
    passphrase: 'your_passphrase'
});

// Subscribe to ticker updates
wsClient.subscribe([{
    instType: 'SPOT',
    channel: 'ticker',
    instId: 'BTCUSDT'
}]);

// Handle incoming messages
wsClient.on('message', (data) => {
    console.log('Received:', data);
});

// Handle errors
wsClient.on('error', (error) => {
    console.error('WebSocket error:', error);
});

// Handle connection close
wsClient.on('close', () => {
    console.log('WebSocket connection closed');
});
```

## Error Handling

```javascript
try {
    const result = await client.someMethod();
} catch (error) {
    if (error.code) {
        // API error
        console.error(`API Error ${error.code}: ${error.message}`);
    } else {
        // Network or other error
        console.error('Error:', error.message);
    }
}
```

## Available API Methods

### Spot Trading

```javascript
// Market Data
client.getTicker(symbol);
client.getOrderBook(symbol);
client.getTrades(symbol);
client.getKlines(symbol, interval);

// Trading
client.submitOrder(params);
client.cancelOrder(params);
client.getOrder(params);
client.getOpenOrders(symbol);
client.getOrderHistory(params);

// Account
client.getAccountInfo();
client.getAccountBalance();
client.getAccountHistory(params);
```

### Futures Trading

```javascript
// Market Data
client.getFuturesTicker(symbol);
client.getFuturesOrderBook(symbol);
client.getFuturesTrades(symbol);
client.getFuturesKlines(symbol, interval);

// Trading
client.submitFuturesOrder(params);
client.cancelFuturesOrder(params);
client.getFuturesOrder(params);
client.getFuturesOpenOrders(symbol);
client.getFuturesOrderHistory(params);

// Account
client.getFuturesAccountInfo();
client.getFuturesPositions();
client.getFuturesAccountHistory(params);
```

## WebSocket Channels

### Public Channels

```javascript
// Market Data
wsClient.subscribe([
    { instType: 'SPOT', channel: 'ticker', instId: 'BTCUSDT' },
    { instType: 'SPOT', channel: 'books', instId: 'BTCUSDT' },
    { instType: 'SPOT', channel: 'trade', instId: 'BTCUSDT' },
    { instType: 'SPOT', channel: 'candle1m', instId: 'BTCUSDT' }
]);
```

### Private Channels

```javascript
// Account and Order Updates
wsClient.subscribe([
    { instType: 'SPOT', channel: 'account' },
    { instType: 'SPOT', channel: 'orders' }
]);
```

## Rate Limiting

The SDK implements automatic rate limiting. However, you should still be aware of the API limits:

- REST API: 20 requests per second for public endpoints
- REST API: 30 requests per second for private endpoints
- WebSocket: 50 connections per IP for public data
- WebSocket: 20 connections per API key for private data

## Best Practices

1. **Error Handling**
```javascript
function handleError(error) {
    if (error.code === '00003') {
        // Rate limit exceeded
        console.error('Rate limit exceeded, implementing backoff...');
        // Implement retry logic
    } else if (error.code === '10001') {
        // Authentication error
        console.error('Authentication failed, check credentials');
    } else {
        console.error('Unknown error:', error);
    }
}
```

2. **WebSocket Reconnection**
```javascript
function setupWebSocket() {
    wsClient.on('close', () => {
        console.log('WebSocket disconnected, reconnecting...');
        setTimeout(() => {
            wsClient.connect();
        }, 5000);
    });
}
```

3. **Batch Operations**
```javascript
// Cancel multiple orders
async function cancelMultipleOrders(orders) {
    try {
        const result = await client.cancelBatchOrders(orders);
        console.log('Orders cancelled:', result);
    } catch (error) {
        handleError(error);
    }
}
```

## Additional Resources

- [Official API Documentation](https://bitgetlimited.github.io/apidoc/en/spot/)
- [API Status Page](https://www.bitget.com/pagestatus)
- [Telegram Support](https://t.me/bitgetOpenapi)
