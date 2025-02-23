# Spot Market Data API

## Get Symbols

Get detailed information about all trading pairs.

### HTTP Request

`GET /api/spot/v1/public/products`

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "symbol": "BTCUSDT",
        "symbolName": "BTC-USDT",
        "baseCoin": "BTC",
        "quoteCoin": "USDT",
        "minTradeAmount": "0.0001",
        "maxTradeAmount": "100",
        "takerFeeRate": "0.001",
        "makerFeeRate": "0.001",
        "priceScale": 2,
        "quantityScale": 6,
        "status": "online"
    }]
}
```

## Get Ticker

Get latest price snapshot, best bid/ask price, and 24h trading volume.

### HTTP Request

`GET /api/spot/v1/market/ticker`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Trading pair symbol |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "symbol": "BTCUSDT",
        "high24h": "40000",
        "low24h": "38000",
        "close": "39000",
        "quoteVol": "1000000",
        "baseVol": "26.5",
        "usdtVol": "1000000",
        "bestBid": "38999",
        "bestAsk": "39001"
    }
}
```

## Get Order Book

Get order book data for a symbol.

### HTTP Request

`GET /api/spot/v1/market/depth`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Trading pair symbol |
| limit     | int    | No       | Depth size (default: 100, max: 200) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "asks": [
            ["39001.5", "0.5"],
            ["39002.0", "0.8"]
        ],
        "bids": [
            ["39000.5", "0.3"],
            ["39000.0", "1.2"]
        ],
        "timestamp": 1621441751927
    }
}
```

## Get Recent Trades

Get recent trades for a symbol.

### HTTP Request

`GET /api/spot/v1/market/fills`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Trading pair symbol |
| limit     | int    | No       | Number of trades (default: 100, max: 1000) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "price": "39000.5",
        "size": "0.1",
        "side": "buy",
        "timestamp": 1621441751927
    }]
}
```

## Get Candlestick Data

Get historical candlestick data for a symbol.

### HTTP Request

`GET /api/spot/v1/market/candles`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Trading pair symbol |
| period    | string | Yes      | Candlestick period ("1min", "5min", "15min", "30min", "1h", "4h", "12h", "1day", "1week") |
| limit     | int    | No       | Number of candles (default: 100, max: 1000) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
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
