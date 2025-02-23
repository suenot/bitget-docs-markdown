# Python SDK for Bitget API v3

## Requirements
- Python 3.6+
- WebSocket library (recommended version: 1.4.2+)

## Installation

1. Download the SDK
```bash
git clone https://github.com/BitgetLimited/v3-bitget-api-sdk.git
cd v3-bitget-api-sdk/bitget-python-sdk-api
```

2. Install required packages
```bash
pip install requests
pip install websocket-client
```

## Configuration

1. [Create API Keys](https://www.bitget.com/account/newapi) if you don't have them yet

2. Configure your API credentials:
```python
api_key = "your_api_key"
secret_key = "your_secret_key"
passphrase = "your_passphrase"
```

## Usage Examples

### REST API Example

```python
import bitget.v1.mix.order_api as maxOrderApi
import bitget.bitget_api as baseApi
from bitget.exceptions import BitgetAPIException

# Initialize API client
maxOrderApi = maxOrderApi.OrderApi(apiKey, secretKey, passphrase)

# Place an order
try:
    params = {
        "symbol": "BTCUSDT_UMCBL",
        "marginCoin": "USDT",
        "side": "open_long",
        "orderType": "limit",
        "price": "27012",
        "size": "0.01",
        "timInForceValue": "normal"
    }
    response = maxOrderApi.placeOrder(params)
    print(response)
except BitgetAPIException as e:
    print("error:" + e.message)

# Get market data
baseApi = baseApi.BitgetApi(apiKey, secretKey, passphrase)
try:
    params = {"productType": "umcbl"}
    response = baseApi.get("/api/mix/v1/market/contracts", params)
    print(response)
except BitgetAPIException as e:
    print("error:" + e.message)
```

### WebSocket API Example

```python
import bitget.ws_client as ws

# Initialize WebSocket client
ws_client = ws.BitgetWsClient(apiKey, secretKey, passphrase)

# Define message handler
def handle_message(message):
    print(message)

# Subscribe to channel
ws_client.subscribe([{
    "instType": "SPOT",
    "channel": "ticker",
    "instId": "BTCUSDT"
}], handle_message)

# Start WebSocket connection
ws_client.start()
```

## Available Endpoints

### Spot Trading
- Market Data
- Trading Operations
- Account Information
- Margin Trading

### Futures Trading
- Market Data
- Trading Operations
- Position Management
- Account Information

### Copy Trading
- Futures Copy Trading
- Spot Copy Trading

For detailed API documentation, please refer to the [official Bitget API documentation](https://bitgetlimited.github.io/apidoc/en/spot/).

## Error Handling

The SDK uses `BitgetAPIException` for error handling. Always wrap your API calls in try-catch blocks:

```python
try:
    response = api.some_method()
except BitgetAPIException as e:
    print(f"Error: {e.message}")
```

## Rate Limits

Please be aware of the API rate limits and handle them appropriately in your application. Refer to the [rate limits documentation](../rate-limits.md) for more details.
