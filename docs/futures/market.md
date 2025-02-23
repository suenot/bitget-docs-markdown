# Futures Market API

## Overview

The Futures Market API provides access to market data for futures contracts, including contract information, order book data, recent trades, and candlestick data.

## Get Contracts Information

Get detailed information about available futures contracts.

### HTTP Request

`GET /api/mix/v1/market/contracts`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| productType | string | No | Contract type: USDT-FUTURES, COIN-FUTURES |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "symbol": "BTCUSDT",
        "contractType": "perpetual",
        "baseAsset": "BTC",
        "quoteAsset": "USDT",
        "settlementAsset": "USDT",
        "launchTime": 1621441751927,
        "deliveryTime": 0,
        "minLeverage": "1",
        "maxLeverage": "100",
        "priceScale": 2,
        "volumeScale": 3,
        "priceUnit": "0.5",
        "volumeUnit": "0.001",
        "minVolume": "0.001",
        "maxVolume": "1000",
        "maxHoldVolume": "1000000",
        "maintainMarginRate": "0.004",
        "makeRate": "-0.00025",
        "takeRate": "0.00075",
        "fundingInterval": 28800,
        "status": "online"
    }]
}
```

## Get Ticker

Get 24-hour price change statistics for a contract.

### HTTP Request

`GET /api/mix/v1/market/ticker`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "symbol": "BTCUSDT",
        "lastPrice": "30000.50",
        "priceChange24h": "1500.25",
        "priceChangePercent24h": "5.25",
        "highPrice24h": "31000.00",
        "lowPrice24h": "29000.00",
        "volume24h": "1234.567",
        "quoteVolume24h": "36789123.45",
        "openPrice24h": "28500.25",
        "markPrice": "30001.00",
        "indexPrice": "30002.00",
        "fundingRate": "0.0001",
        "nextFundingTime": 1621441751927
    }
}
```

## Get Order Book

Get order book data for a contract.

### HTTP Request

`GET /api/mix/v1/market/depth`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| limit     | int    | No       | Depth limit (5, 10, 20, 50, 100, default: 100) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "symbol": "BTCUSDT",
        "timestamp": 1621441751927,
        "bids": [
            ["30000.50", "0.123"],  // [price, quantity]
            ["30000.00", "0.456"]
        ],
        "asks": [
            ["30001.00", "0.789"],
            ["30001.50", "0.321"]
        ]
    }
}
```

## Get Recent Trades

Get recent trades for a contract.

### HTTP Request

`GET /api/mix/v1/market/trades`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| limit     | int    | No       | Number of trades (default: 100, max: 1000) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "symbol": "BTCUSDT",
        "price": "30000.50",
        "quantity": "0.123",
        "side": "buy",
        "timestamp": 1621441751927
    }]
}
```

## Get Candlestick Data

Get candlestick/kline data for a contract.

### HTTP Request

`GET /api/mix/v1/market/candles`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| interval  | string | Yes      | Kline interval (1m,5m,15m,30m,1h,4h,12h,1d,1w) |
| startTime | long   | No       | Start time in milliseconds |
| endTime   | long   | No       | End time in milliseconds |
| limit     | int    | No       | Number of candles (default: 100, max: 1000) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "timestamp": 1621441751927,
        "open": "30000.00",
        "high": "30100.00",
        "low": "29900.00",
        "close": "30050.00",
        "volume": "123.456",
        "quoteVolume": "3698547.89"
    }]
}
```

## Get Funding Rate History

Get historical funding rates for a contract.

### HTTP Request

`GET /api/mix/v1/market/funding-rate`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| startTime | long   | No       | Start time in milliseconds |
| endTime   | long   | No       | End time in milliseconds |
| limit     | int    | No       | Number of records (default: 100, max: 1000) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "symbol": "BTCUSDT",
        "fundingRate": "0.0001",
        "fundingTime": 1621441751927
    }]
}
```

## Get Index Price

Get index price for a contract.

### HTTP Request

`GET /api/mix/v1/market/index-price`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "symbol": "BTCUSDT",
        "indexPrice": "30002.00",
        "components": [{
            "exchange": "Binance",
            "price": "30001.50",
            "weight": "0.333"
        }]
    }
}
```

## Get Mark Price

Get mark price for a contract.

### HTTP Request

`GET /api/mix/v1/market/mark-price`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "symbol": "BTCUSDT",
        "markPrice": "30001.00",
        "indexPrice": "30002.00",
        "lastFundingRate": "0.0001",
        "nextFundingTime": 1621441751927
    }
}
```

## Error Codes

| Code   | Message | Description |
|--------|---------|-------------|
| 50001  | Invalid symbol | Contract symbol does not exist |
| 50002  | Invalid interval | Kline interval not supported |
| 50003  | Invalid limit | Limit exceeds maximum allowed |
| 50004  | Invalid time range | Time range is invalid |

## Best Practices

1. **Rate Limiting**
```python
def get_market_data_with_rate_limit():
    rate_limiter = RateLimiter(
        max_requests=10,
        time_window=1  # 1 second
    )
    
    if rate_limiter.can_make_request():
        return client.getMarketData()
    else:
        time.sleep(0.1)  # Wait 100ms
        return get_market_data_with_rate_limit()
```

2. **Order Book Management**
```python
class OrderBookManager:
    def __init__(self):
        self.order_book = {}
        self.last_update = 0
    
    def update(self, data):
        if data['timestamp'] <= self.last_update:
            return  # Skip old updates
            
        self.last_update = data['timestamp']
        self.order_book = {
            'bids': {price: qty for price, qty in data['bids']},
            'asks': {price: qty for price, qty in data['asks']}
        }
    
    def get_best_bid_ask(self):
        return {
            'bid': max(self.order_book['bids'].keys()),
            'ask': min(self.order_book['asks'].keys())
        }
```

3. **Price Monitoring**
```python
def monitor_price_changes(symbol, threshold=0.01):
    last_price = float(client.getTicker(symbol)['lastPrice'])
    
    while True:
        current_price = float(client.getTicker(symbol)['lastPrice'])
        price_change = abs(current_price - last_price) / last_price
        
        if price_change > threshold:
            notify_price_change(symbol, last_price, current_price)
            
        last_price = current_price
        time.sleep(1)
```

4. **Data Caching**
```python
class MarketDataCache:
    def __init__(self, ttl=60):  # 60 seconds TTL
        self.cache = {}
        self.ttl = ttl
    
    def get_or_fetch(self, key, fetch_func):
        if key in self.cache:
            data, timestamp = self.cache[key]
            if time.time() - timestamp < self.ttl:
                return data
                
        data = fetch_func()
        self.cache[key] = (data, time.time())
        return data
```

## Additional Resources

- [Futures Trading Guide](https://www.bitget.com/support/futures-guide)
- [Contract Specifications](https://www.bitget.com/support/contract-specs)
- [Fee Structure](https://www.bitget.com/support/futures-fees)
