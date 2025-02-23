# Bitget API v3 Documentation

## Getting Started
- [Introduction](./docs/introduction.md)
- [Authentication](./docs/authentication.md)
- [Error Codes](./docs/error-codes.md)
- [Rate Limits](./docs/rate-limits.md)

## SDK Examples
- [Python SDK](./docs/sdk/python.md)
- [Node.js SDK](./docs/sdk/nodejs.md)
- [Java SDK](./docs/sdk/java.md)
- [Go SDK](./docs/sdk/go.md)
- [PHP SDK](./docs/sdk/php.md)

## Spot Trading
### REST API
- [Market Data](./docs/spot/market.md)
  - Get Symbols
  - Get Ticker
  - Get Order Book
  - Get Recent Trades
  - Get Candlestick Data
- [Trading](./docs/spot/trading.md)
  - Place Order
  - Cancel Order
  - Get Order Status
  - Get Open Orders
  - Get Order History
- [Account](./docs/spot/account.md)
  - Get Account Balance
  - Get Account History
- [Margin Trading](./docs/spot/margin.md)
  - Get Margin Account
  - Borrow
  - Repay
  - Transfer

### WebSocket API
- [Market Data Stream](./docs/spot/ws-market.md)
  - Subscribe to Ticker
  - Subscribe to Order Book
  - Subscribe to Trades
  - Subscribe to Candlestick Data
- [User Data Stream](./docs/spot/ws-user.md)
  - Account Updates
  - Order Updates

## Futures Trading
### REST API
- [Market Data](./docs/futures/market.md)
  - Get Contracts
  - Get Ticker
  - Get Order Book
  - Get Recent Trades
  - Get Candlestick Data
- [Trading](./docs/futures/trading.md)
  - Place Order
  - Cancel Order
  - Get Position
  - Get Order Status
  - Get Open Orders
- [Account](./docs/futures/account.md)
  - Get Account Info
  - Get Position Risk

### WebSocket API
- [Market Data Stream](./docs/futures/ws-market.md)
  - Subscribe to Ticker
  - Subscribe to Order Book
  - Subscribe to Trades
- [User Data Stream](./docs/futures/ws-user.md)
  - Position Updates
  - Order Updates
  - Account Updates

## Copy Trading
- [Futures Copy Trading](./docs/copy-trading/futures.md)
  - Get Traders List
  - Follow Trader
  - Unfollow Trader
  - Get Copy Trading History
- [Spot Copy Trading](./docs/copy-trading/spot.md)
  - Get Traders List
  - Follow Trader
  - Unfollow Trader
  - Get Copy Trading History

## Additional Resources
- [Official API Documentation](https://bitgetlimited.github.io/apidoc/en/spot/)
- [API Status](https://status.bitget.com/)
- [Bitget Blog](https://blog.bitget.com/)
- [Telegram Group](https://t.me/bitgetOpenapi)
