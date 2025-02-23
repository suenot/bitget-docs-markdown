# Spot Trading API

## Place Order

Place a new order for spot trading.

### HTTP Request

`POST /api/spot/v1/trade/orders`

### Parameters

| Parameter     | Type   | Required | Description |
|--------------|--------|----------|-------------|
| symbol       | string | Yes      | Trading pair symbol |
| side         | string | Yes      | Order side: "buy" or "sell" |
| orderType    | string | Yes      | Order type: "limit", "market" |
| force        | string | Yes      | Time in force: "gtc"(Good Till Cancel), "ioc"(Immediate or Cancel), "fok"(Fill or Kill) |
| price        | string | Yes*     | Order price. Required for limit orders |
| quantity     | string | Yes      | Order quantity |
| clientOrderId| string | No       | Client-supplied order ID |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "orderId": "123456789",
        "clientOrderId": "myOrder123",
        "symbol": "BTCUSDT",
        "status": "new"
    }
}
```

## Cancel Order

Cancel an existing order.

### HTTP Request

`POST /api/spot/v1/trade/cancel-order`

### Parameters

| Parameter    | Type   | Required | Description |
|-------------|--------|----------|-------------|
| symbol      | string | Yes      | Trading pair symbol |
| orderId     | string | Yes*     | Order ID (*Either orderId or clientOrderId must be provided) |
| clientOrderId| string | Yes*     | Client order ID |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "orderId": "123456789",
        "clientOrderId": "myOrder123",
        "symbol": "BTCUSDT",
        "status": "cancelled"
    }
}
```

## Get Order Status

Get detailed information about a specific order.

### HTTP Request

`GET /api/spot/v1/trade/order`

### Parameters

| Parameter    | Type   | Required | Description |
|-------------|--------|----------|-------------|
| symbol      | string | Yes      | Trading pair symbol |
| orderId     | string | Yes*     | Order ID (*Either orderId or clientOrderId must be provided) |
| clientOrderId| string | Yes*     | Client order ID |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "orderId": "123456789",
        "clientOrderId": "myOrder123",
        "symbol": "BTCUSDT",
        "price": "39000.5",
        "quantity": "0.1",
        "executedQty": "0.05",
        "executedPrice": "39000.5",
        "side": "buy",
        "status": "partially_filled",
        "orderType": "limit",
        "force": "gtc",
        "totalQuota": "3900.05",
        "fee": "3.9",
        "feeCoin": "USDT",
        "createTime": 1621441751927,
        "updateTime": 1621441751927
    }
}
```

## Get Open Orders

Get all open orders for a symbol.

### HTTP Request

`GET /api/spot/v1/trade/open-orders`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Trading pair symbol |
| limit     | int    | No       | Number of orders (default: 100, max: 1000) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "orderId": "123456789",
        "clientOrderId": "myOrder123",
        "symbol": "BTCUSDT",
        "price": "39000.5",
        "quantity": "0.1",
        "executedQty": "0.05",
        "executedPrice": "39000.5",
        "side": "buy",
        "status": "partially_filled",
        "orderType": "limit",
        "force": "gtc",
        "totalQuota": "3900.05",
        "fee": "3.9",
        "feeCoin": "USDT",
        "createTime": 1621441751927,
        "updateTime": 1621441751927
    }]
}
```

## Get Order History

Get historical orders for a symbol.

### HTTP Request

`GET /api/spot/v1/trade/history`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Trading pair symbol |
| startTime | long   | No       | Start time in milliseconds |
| endTime   | long   | No       | End time in milliseconds |
| limit     | int    | No       | Number of orders (default: 100, max: 1000) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "orderId": "123456789",
        "clientOrderId": "myOrder123",
        "symbol": "BTCUSDT",
        "price": "39000.5",
        "quantity": "0.1",
        "executedQty": "0.1",
        "executedPrice": "39000.5",
        "side": "buy",
        "status": "filled",
        "orderType": "limit",
        "force": "gtc",
        "totalQuota": "3900.05",
        "fee": "3.9",
        "feeCoin": "USDT",
        "createTime": 1621441751927,
        "updateTime": 1621441751927
    }]
}
```
