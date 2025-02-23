# PHP SDK for Bitget API v3

## Requirements

- PHP 7.2 or higher
- Composer
- PHP cURL extension
- PHP JSON extension

## Installation

1. Download the SDK
```bash
git clone https://github.com/BitgetLimited/v3-bitget-api-sdk.git
cd v3-bitget-api-sdk/bitget-php-sdk-api
```

2. Install via Composer
```bash
composer require bitget/bitget-php-sdk-api
```

## Configuration

1. [Create API Keys](https://www.bitget.com/account/newapi) if you don't have them yet

2. Configure your API credentials:
```php
$apiKey = 'your_api_key';
$secretKey = 'your_secret_key';
$passphrase = 'your_passphrase';
```

## Usage Examples

### REST API Example

```php
<?php

require_once 'vendor/autoload.php';

use Bitget\API\BitgetClient;
use Bitget\API\Exception\BitgetAPIException;

// Initialize client
$client = new BitgetClient([
    'apiKey' => 'your_api_key',
    'secretKey' => 'your_secret_key',
    'passphrase' => 'your_passphrase'
]);

try {
    // Place an order
    $orderParams = [
        'symbol' => 'BTCUSDT',
        'side' => 'buy',
        'orderType' => 'limit',
        'force' => 'gtc',
        'price' => '30000',
        'quantity' => '0.001'
    ];
    
    $response = $client->spot()->placeOrder($orderParams);
    echo "Order placed: " . json_encode($response, JSON_PRETTY_PRINT) . "\n";

    // Get account information
    $accountInfo = $client->spot()->getAccountInfo();
    echo "Account info: " . json_encode($accountInfo, JSON_PRETTY_PRINT) . "\n";

    // Get market data
    $ticker = $client->spot()->getTicker('BTCUSDT');
    echo "Ticker: " . json_encode($ticker, JSON_PRETTY_PRINT) . "\n";

} catch (BitgetAPIException $e) {
    echo "API Error: " . $e->getMessage() . " (Code: " . $e->getCode() . ")\n";
} catch (Exception $e) {
    echo "Error: " . $e->getMessage() . "\n";
}
```

### WebSocket API Example

```php
<?php

require_once 'vendor/autoload.php';

use Bitget\API\WebSocket\BitgetWebSocketClient;

class WebSocketExample {
    private $wsClient;

    public function __construct($apiKey, $secretKey, $passphrase) {
        $this->wsClient = new BitgetWebSocketClient([
            'apiKey' => $apiKey,
            'secretKey' => $secretKey,
            'passphrase' => $passphrase
        ]);

        // Set message handler
        $this->wsClient->onMessage(function($message) {
            echo "Received message: " . $message . "\n";
        });

        // Set error handler
        $this->wsClient->onError(function($error) {
            echo "WebSocket error: " . $error . "\n";
        });

        // Set close handler
        $this->wsClient->onClose(function() {
            echo "WebSocket connection closed\n";
        });
    }

    public function subscribe() {
        $channels = [
            [
                'instType' => 'SPOT',
                'channel' => 'ticker',
                'instId' => 'BTCUSDT'
            ]
        ];

        $this->wsClient->subscribe($channels);
        $this->wsClient->connect();
    }
}

// Usage
$example = new WebSocketExample($apiKey, $secretKey, $passphrase);
$example->subscribe();
```

## Error Handling

```php
try {
    $result = $client->spot()->someMethod();
} catch (BitgetAPIException $e) {
    switch ($e->getCode()) {
        case '00003':
            // Rate limit exceeded
            echo "Rate limit exceeded, implementing backoff...\n";
            sleep(1);
            break;
        case '10001':
            // Authentication error
            echo "Authentication failed, check credentials\n";
            break;
        default:
            echo "API Error " . $e->getCode() . ": " . $e->getMessage() . "\n";
    }
} catch (Exception $e) {
    echo "General error: " . $e->getMessage() . "\n";
}
```

## Available API Methods

### Spot Trading

```php
// Market Data
$client->spot()->getTicker($symbol);
$client->spot()->getOrderBook($symbol);
$client->spot()->getTrades($symbol);
$client->spot()->getKlines($symbol, $interval);

// Trading
$client->spot()->placeOrder($params);
$client->spot()->cancelOrder($params);
$client->spot()->getOrder($orderId);
$client->spot()->getOpenOrders($symbol);
$client->spot()->getOrderHistory($params);

// Account
$client->spot()->getAccountInfo();
$client->spot()->getAccountBalance();
$client->spot()->getAccountHistory($params);
```

### Futures Trading

```php
// Market Data
$client->futures()->getTicker($symbol);
$client->futures()->getOrderBook($symbol);
$client->futures()->getTrades($symbol);
$client->futures()->getKlines($symbol, $interval);

// Trading
$client->futures()->placeOrder($params);
$client->futures()->cancelOrder($params);
$client->futures()->getOrder($orderId);
$client->futures()->getOpenOrders($symbol);
$client->futures()->getOrderHistory($params);

// Account
$client->futures()->getAccountInfo();
$client->futures()->getPositions();
$client->futures()->getAccountHistory($params);
```

## WebSocket Channels

### Public Channels

```php
// Market Data Subscriptions
$channels = [
    ['instType' => 'SPOT', 'channel' => 'ticker', 'instId' => 'BTCUSDT'],
    ['instType' => 'SPOT', 'channel' => 'books', 'instId' => 'BTCUSDT'],
    ['instType' => 'SPOT', 'channel' => 'trade', 'instId' => 'BTCUSDT'],
    ['instType' => 'SPOT', 'channel' => 'candle1m', 'instId' => 'BTCUSDT']
];
```

### Private Channels

```php
// Account and Order Updates
$channels = [
    ['instType' => 'SPOT', 'channel' => 'account'],
    ['instType' => 'SPOT', 'channel' => 'orders']
];
```

## Rate Limiting

The SDK implements automatic rate limiting. However, you should still be aware of the API limits:

- REST API: 20 requests per second for public endpoints
- REST API: 30 requests per second for private endpoints
- WebSocket: 50 connections per IP for public data
- WebSocket: 20 connections per API key for private data

## Best Practices

1. **Connection Pool Management**
```php
class ConnectionPool {
    private static $instance = null;
    private $connections = [];
    
    public static function getInstance() {
        if (self::$instance === null) {
            self::$instance = new self();
        }
        return self::$instance;
    }
    
    public function getConnection() {
        // Implement connection pooling logic
    }
}
```

2. **WebSocket Reconnection**
```php
class ReconnectingWebSocket {
    private const MAX_RETRIES = 5;
    private const RETRY_DELAY = 5;
    
    public function setupReconnection($wsClient) {
        $wsClient->onClose(function() use ($wsClient) {
            $retries = 0;
            while ($retries < self::MAX_RETRIES) {
                try {
                    $wsClient->connect();
                    return;
                } catch (Exception $e) {
                    $retries++;
                    sleep(self::RETRY_DELAY);
                }
            }
        });
    }
}
```

3. **Batch Operations**
```php
function cancelMultipleOrders($client, $orderIds) {
    try {
        $response = $client->spot()->batchCancelOrders([
            'symbol' => 'BTCUSDT',
            'orderIds' => $orderIds
        ]);
        echo "Orders cancelled: " . json_encode($response, JSON_PRETTY_PRINT) . "\n";
    } catch (BitgetAPIException $e) {
        handleError($e);
    }
}
```

## Additional Resources

- [Official API Documentation](https://bitgetlimited.github.io/apidoc/en/spot/)
- [API Status Page](https://www.bitget.com/pagestatus)
- [Telegram Support](https://t.me/bitgetOpenapi)
