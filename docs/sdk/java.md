# Java SDK for Bitget API v3

## Requirements

- Java 8 or higher
- Maven or Gradle

## Installation

1. Download the SDK
```bash
git clone https://github.com/BitgetLimited/v3-bitget-api-sdk.git
cd v3-bitget-api-sdk/bitget-java-sdk-api
```

2. Add dependency to your project

For Maven:
```xml
<dependency>
    <groupId>com.bitget</groupId>
    <artifactId>bitget-java-sdk-api</artifactId>
    <version>3.0.0</version>
</dependency>
```

For Gradle:
```groovy
implementation 'com.bitget:bitget-java-sdk-api:3.0.0'
```

## Configuration

1. [Create API Keys](https://www.bitget.com/account/newapi) if you don't have them yet

2. Configure your API credentials:
```java
String apiKey = "your_api_key";
String secretKey = "your_secret_key";
String passphrase = "your_passphrase";
```

## Usage Examples

### REST API Example

```java
import com.bitget.api.client.BitgetClient;
import com.bitget.api.model.spot.*;

public class BitgetExample {
    private static final String API_KEY = "your_api_key";
    private static final String SECRET_KEY = "your_secret_key";
    private static final String PASSPHRASE = "your_passphrase";

    public static void main(String[] args) {
        // Initialize client
        BitgetClient client = new BitgetClient(API_KEY, SECRET_KEY, PASSPHRASE);

        try {
            // Place an order
            PlaceOrderRequest orderRequest = PlaceOrderRequest.builder()
                .symbol("BTCUSDT")
                .side("buy")
                .orderType("limit")
                .force("gtc")
                .price("30000")
                .quantity("0.001")
                .build();

            PlaceOrderResponse order = client.spot().placeOrder(orderRequest);
            System.out.println("Order placed: " + order);

            // Get account information
            AccountInfo accountInfo = client.spot().getAccountInfo();
            System.out.println("Account info: " + accountInfo);

            // Get market data
            Ticker ticker = client.spot().getTicker("BTCUSDT");
            System.out.println("Ticker: " + ticker);

        } catch (BitgetApiException e) {
            System.err.println("API Error: " + e.getCode() + " - " + e.getMessage());
        } catch (Exception e) {
            System.err.println("Error: " + e.getMessage());
        }
    }
}
```

### WebSocket API Example

```java
import com.bitget.api.ws.BitgetWebSocketClient;
import com.bitget.api.ws.BitgetWebSocketListener;

public class WebSocketExample {
    public static void main(String[] args) {
        BitgetWebSocketClient wsClient = new BitgetWebSocketClient(API_KEY, SECRET_KEY, PASSPHRASE);

        // Create custom listener
        BitgetWebSocketListener listener = new BitgetWebSocketListener() {
            @Override
            public void onMessage(String message) {
                System.out.println("Received message: " + message);
            }

            @Override
            public void onFailure(Throwable t) {
                System.err.println("WebSocket error: " + t.getMessage());
            }

            @Override
            public void onClosing() {
                System.out.println("WebSocket closing");
            }
        };

        // Subscribe to channels
        List<SubscriptionRequest> subscriptions = Arrays.asList(
            new SubscriptionRequest("SPOT", "ticker", "BTCUSDT")
        );

        wsClient.subscribe(subscriptions, listener);
    }
}
```

## Error Handling

```java
try {
    // API call
    PlaceOrderResponse response = client.spot().placeOrder(request);
} catch (BitgetApiException e) {
    switch (e.getCode()) {
        case "00003":
            // Rate limit exceeded
            System.err.println("Rate limit exceeded, implementing backoff...");
            Thread.sleep(1000);
            break;
        case "10001":
            // Authentication error
            System.err.println("Authentication failed, check credentials");
            break;
        default:
            System.err.println("API Error: " + e.getCode() + " - " + e.getMessage());
    }
} catch (Exception e) {
    System.err.println("General error: " + e.getMessage());
}
```

## Available API Methods

### Spot Trading

```java
// Market Data
client.spot().getTicker(symbol);
client.spot().getOrderBook(symbol);
client.spot().getTrades(symbol);
client.spot().getKlines(symbol, interval);

// Trading
client.spot().placeOrder(request);
client.spot().cancelOrder(request);
client.spot().getOrder(orderId);
client.spot().getOpenOrders(symbol);
client.spot().getOrderHistory(request);

// Account
client.spot().getAccountInfo();
client.spot().getAccountBalance();
client.spot().getAccountHistory(request);
```

### Futures Trading

```java
// Market Data
client.futures().getTicker(symbol);
client.futures().getOrderBook(symbol);
client.futures().getTrades(symbol);
client.futures().getKlines(symbol, interval);

// Trading
client.futures().placeOrder(request);
client.futures().cancelOrder(request);
client.futures().getOrder(orderId);
client.futures().getOpenOrders(symbol);
client.futures().getOrderHistory(request);

// Account
client.futures().getAccountInfo();
client.futures().getPositions();
client.futures().getAccountHistory(request);
```

## WebSocket Channels

### Public Channels

```java
// Market Data Subscriptions
List<SubscriptionRequest> subscriptions = Arrays.asList(
    new SubscriptionRequest("SPOT", "ticker", "BTCUSDT"),
    new SubscriptionRequest("SPOT", "books", "BTCUSDT"),
    new SubscriptionRequest("SPOT", "trade", "BTCUSDT"),
    new SubscriptionRequest("SPOT", "candle1m", "BTCUSDT")
);
```

### Private Channels

```java
// Account and Order Updates
List<SubscriptionRequest> subscriptions = Arrays.asList(
    new SubscriptionRequest("SPOT", "account", null),
    new SubscriptionRequest("SPOT", "orders", null)
);
```

## Rate Limiting

The SDK implements automatic rate limiting. However, you should still be aware of the API limits:

- REST API: 20 requests per second for public endpoints
- REST API: 30 requests per second for private endpoints
- WebSocket: 50 connections per IP for public data
- WebSocket: 20 connections per API key for private data

## Best Practices

1. **Connection Pool Management**
```java
public class ConnectionPool {
    private static final OkHttpClient client = new OkHttpClient.Builder()
        .connectTimeout(30, TimeUnit.SECONDS)
        .readTimeout(30, TimeUnit.SECONDS)
        .writeTimeout(30, TimeUnit.SECONDS)
        .build();

    public static OkHttpClient getClient() {
        return client;
    }
}
```

2. **WebSocket Reconnection**
```java
public class ReconnectingWebSocketClient {
    private static final int MAX_RETRY_COUNT = 5;
    private static final long RETRY_DELAY_MS = 5000;

    private void setupReconnection(BitgetWebSocketClient wsClient) {
        wsClient.setReconnectOnFailure(true);
        wsClient.setReconnectInterval(RETRY_DELAY_MS);
        wsClient.setMaxReconnectAttempts(MAX_RETRY_COUNT);
    }
}
```

3. **Batch Operations**
```java
public void cancelMultipleOrders(List<String> orderIds) {
    try {
        BatchCancelOrderRequest request = BatchCancelOrderRequest.builder()
            .symbol("BTCUSDT")
            .orderIds(orderIds)
            .build();
        BatchCancelOrderResponse response = client.spot().batchCancelOrders(request);
        System.out.println("Orders cancelled: " + response);
    } catch (BitgetApiException e) {
        handleError(e);
    }
}
```

## Additional Resources

- [Official API Documentation](https://bitgetlimited.github.io/apidoc/en/spot/)
- [API Status Page](https://www.bitget.com/pagestatus)
- [Telegram Support](https://t.me/bitgetOpenapi)
