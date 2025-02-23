# Go SDK for Bitget API v3

## Requirements

- Go 1.13 or higher

## Installation

1. Download the SDK
```bash
git clone https://github.com/BitgetLimited/v3-bitget-api-sdk.git
cd v3-bitget-api-sdk/bitget-golang-sdk-api
```

2. Install the package
```bash
go get github.com/bitget/bitget-golang-sdk-api
```

## Configuration

1. [Create API Keys](https://www.bitget.com/account/newapi) if you don't have them yet

2. Configure your API credentials:
```go
const (
    apiKey     = "your_api_key"
    secretKey  = "your_secret_key"
    passphrase = "your_passphrase"
)
```

## Usage Examples

### REST API Example

```go
package main

import (
    "fmt"
    "github.com/bitget/bitget-golang-sdk-api/pkg/client"
    "github.com/bitget/bitget-golang-sdk-api/pkg/model/spot"
)

func main() {
    // Initialize client
    client := client.NewSpotClient(apiKey, secretKey, passphrase)

    // Place an order
    func placeOrder() {
        orderRequest := spot.PlaceOrderRequest{
            Symbol:    "BTCUSDT",
            Side:      "buy",
            OrderType: "limit",
            Force:     "gtc",
            Price:     "30000",
            Quantity:  "0.001",
        }

        response, err := client.PlaceOrder(orderRequest)
        if err != nil {
            fmt.Printf("Error placing order: %v\n", err)
            return
        }
        fmt.Printf("Order placed: %+v\n", response)
    }

    // Get account information
    func getAccountInfo() {
        accountInfo, err := client.GetAccountInfo()
        if err != nil {
            fmt.Printf("Error getting account info: %v\n", err)
            return
        }
        fmt.Printf("Account info: %+v\n", accountInfo)
    }

    // Get market data
    func getMarketData() {
        ticker, err := client.GetTicker("BTCUSDT")
        if err != nil {
            fmt.Printf("Error getting ticker: %v\n", err)
            return
        }
        fmt.Printf("Ticker: %+v\n", ticker)
    }
}
```

### WebSocket API Example

```go
package main

import (
    "fmt"
    "github.com/bitget/bitget-golang-sdk-api/pkg/client/ws"
    "github.com/bitget/bitget-golang-sdk-api/pkg/model/ws"
)

func main() {
    // Initialize WebSocket client
    wsClient := ws.NewWebSocketClient(apiKey, secretKey, passphrase)

    // Define message handler
    messageHandler := func(message string) {
        fmt.Printf("Received message: %s\n", message)
    }

    // Subscribe to channels
    subscriptions := []ws.SubscriptionRequest{
        {
            InstType: "SPOT",
            Channel:  "ticker",
            InstId:   "BTCUSDT",
        },
    }

    wsClient.Subscribe(subscriptions, messageHandler)

    // Keep the connection alive
    select {}
}
```

## Error Handling

```go
func handleError(err error) {
    if apiErr, ok := err.(*ApiError); ok {
        switch apiErr.Code {
        case "00003":
            // Rate limit exceeded
            fmt.Println("Rate limit exceeded, implementing backoff...")
            time.Sleep(time.Second)
        case "10001":
            // Authentication error
            fmt.Println("Authentication failed, check credentials")
        default:
            fmt.Printf("API Error %s: %s\n", apiErr.Code, apiErr.Message)
        }
    } else {
        fmt.Printf("General error: %v\n", err)
    }
}
```

## Available API Methods

### Spot Trading

```go
// Market Data
client.GetTicker(symbol string)
client.GetOrderBook(symbol string)
client.GetTrades(symbol string)
client.GetKlines(symbol, interval string)

// Trading
client.PlaceOrder(request PlaceOrderRequest)
client.CancelOrder(request CancelOrderRequest)
client.GetOrder(orderId string)
client.GetOpenOrders(symbol string)
client.GetOrderHistory(request OrderHistoryRequest)

// Account
client.GetAccountInfo()
client.GetAccountBalance()
client.GetAccountHistory(request AccountHistoryRequest)
```

### Futures Trading

```go
// Market Data
client.GetFuturesTicker(symbol string)
client.GetFuturesOrderBook(symbol string)
client.GetFuturesTrades(symbol string)
client.GetFuturesKlines(symbol, interval string)

// Trading
client.PlaceFuturesOrder(request PlaceFuturesOrderRequest)
client.CancelFuturesOrder(request CancelFuturesOrderRequest)
client.GetFuturesOrder(orderId string)
client.GetFuturesOpenOrders(symbol string)
client.GetFuturesOrderHistory(request OrderHistoryRequest)

// Account
client.GetFuturesAccountInfo()
client.GetFuturesPositions()
client.GetFuturesAccountHistory(request AccountHistoryRequest)
```

## WebSocket Channels

### Public Channels

```go
// Market Data Subscriptions
subscriptions := []ws.SubscriptionRequest{
    {InstType: "SPOT", Channel: "ticker", InstId: "BTCUSDT"},
    {InstType: "SPOT", Channel: "books", InstId: "BTCUSDT"},
    {InstType: "SPOT", Channel: "trade", InstId: "BTCUSDT"},
    {InstType: "SPOT", Channel: "candle1m", InstId: "BTCUSDT"},
}
```

### Private Channels

```go
// Account and Order Updates
subscriptions := []ws.SubscriptionRequest{
    {InstType: "SPOT", Channel: "account", InstId: ""},
    {InstType: "SPOT", Channel: "orders", InstId: ""},
}
```

## Rate Limiting

The SDK implements automatic rate limiting. However, you should still be aware of the API limits:

- REST API: 20 requests per second for public endpoints
- REST API: 30 requests per second for private endpoints
- WebSocket: 50 connections per IP for public data
- WebSocket: 20 connections per API key for private data

## Best Practices

1. **Connection Pool Management**
```go
type ConnectionPool struct {
    client *http.Client
    mu     sync.Mutex
}

func NewConnectionPool() *ConnectionPool {
    return &ConnectionPool{
        client: &http.Client{
            Timeout: time.Second * 30,
            Transport: &http.Transport{
                MaxIdleConns:        100,
                MaxIdleConnsPerHost: 100,
                IdleConnTimeout:     time.Second * 90,
            },
        },
    }
}
```

2. **WebSocket Reconnection**
```go
func setupReconnection(wsClient *ws.WebSocketClient) {
    const maxRetries = 5
    const retryDelay = time.Second * 5

    wsClient.OnDisconnect(func() {
        for i := 0; i < maxRetries; i++ {
            if err := wsClient.Connect(); err == nil {
                return
            }
            time.Sleep(retryDelay)
        }
    })
}
```

3. **Batch Operations**
```go
func cancelMultipleOrders(client *client.SpotClient, orderIds []string) {
    request := spot.BatchCancelOrderRequest{
        Symbol:   "BTCUSDT",
        OrderIds: orderIds,
    }
    
    response, err := client.BatchCancelOrders(request)
    if err != nil {
        handleError(err)
        return
    }
    fmt.Printf("Orders cancelled: %+v\n", response)
}
```

## Additional Resources

- [Official API Documentation](https://bitgetlimited.github.io/apidoc/en/spot/)
- [API Status Page](https://www.bitget.com/pagestatus)
- [Telegram Support](https://t.me/bitgetOpenapi)
