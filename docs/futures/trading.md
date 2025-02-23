# Futures Trading API

## Overview

The Futures Trading API allows you to place and manage orders, monitor positions, and view trading history for futures contracts.

## Place Order

Place a new futures order.

### HTTP Request

`POST /api/mix/v1/order/placeOrder`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| side      | string | Yes      | Order side: buy, sell |
| orderType | string | Yes      | Order type: limit, market, post_only, fok, ioc |
| price     | string | No       | Order price (required for limit orders) |
| size      | string | Yes      | Order size |
| leverage  | string | Yes      | Leverage value |
| marginMode| string | Yes      | Margin mode: isolated, cross |
| reduceOnly| boolean| No       | True to reduce position only |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "orderId": "123456789",
        "symbol": "BTCUSDT",
        "side": "buy",
        "orderType": "limit",
        "price": "30000.50",
        "size": "0.123",
        "leverage": "10",
        "marginMode": "isolated",
        "status": "new",
        "createTime": 1621441751927
    }
}
```

## Place Batch Orders

Place multiple futures orders in a single request.

### HTTP Request

`POST /api/mix/v1/order/batchOrders`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| orders    | array  | Yes      | Array of order objects |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "orderId": "123456789",
        "symbol": "BTCUSDT",
        "status": "new"
    }]
}
```

## Cancel Order

Cancel an existing futures order.

### HTTP Request

`POST /api/mix/v1/order/cancel`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| orderId   | string | Yes      | Order ID to cancel |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "orderId": "123456789",
        "symbol": "BTCUSDT",
        "status": "cancelled"
    }
}
```

## Cancel All Orders

Cancel all open orders for a symbol.

### HTTP Request

`POST /api/mix/v1/order/cancelAll`

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
        "success": true,
        "cancelledOrders": ["123456789", "987654321"]
    }
}
```

## Get Order

Get details of a specific order.

### HTTP Request

`GET /api/mix/v1/order/detail`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| orderId   | string | Yes      | Order ID |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "orderId": "123456789",
        "symbol": "BTCUSDT",
        "side": "buy",
        "orderType": "limit",
        "price": "30000.50",
        "size": "0.123",
        "filledSize": "0.100",
        "avgPrice": "30000.00",
        "leverage": "10",
        "marginMode": "isolated",
        "status": "partially_filled",
        "createTime": 1621441751927,
        "updateTime": 1621441751927
    }
}
```

## Get Open Orders

Get list of open orders.

### HTTP Request

`GET /api/mix/v1/order/current`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| limit     | int    | No       | Number of records (default: 100, max: 500) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "orderId": "123456789",
        "symbol": "BTCUSDT",
        "side": "buy",
        "orderType": "limit",
        "price": "30000.50",
        "size": "0.123",
        "filledSize": "0.100",
        "avgPrice": "30000.00",
        "leverage": "10",
        "marginMode": "isolated",
        "status": "partially_filled",
        "createTime": 1621441751927,
        "updateTime": 1621441751927
    }]
}
```

## Get Order History

Get historical orders.

### HTTP Request

`GET /api/mix/v1/order/history`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| startTime | long   | No       | Start time in milliseconds |
| endTime   | long   | No       | End time in milliseconds |
| limit     | int    | No       | Number of records (default: 100, max: 500) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "orderId": "123456789",
        "symbol": "BTCUSDT",
        "side": "buy",
        "orderType": "limit",
        "price": "30000.50",
        "size": "0.123",
        "filledSize": "0.123",
        "avgPrice": "30000.00",
        "leverage": "10",
        "marginMode": "isolated",
        "status": "filled",
        "createTime": 1621441751927,
        "updateTime": 1621441751927
    }]
}
```

## Get Trade History

Get trade execution history.

### HTTP Request

`GET /api/mix/v1/order/fills`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| startTime | long   | No       | Start time in milliseconds |
| endTime   | long   | No       | End time in milliseconds |
| limit     | int    | No       | Number of records (default: 100, max: 500) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "tradeId": "123456789",
        "orderId": "123456789",
        "symbol": "BTCUSDT",
        "side": "buy",
        "price": "30000.00",
        "size": "0.123",
        "fee": "0.00001",
        "feeCurrency": "USDT",
        "role": "maker",
        "createTime": 1621441751927
    }]
}
```

## Error Codes

| Code   | Message | Description |
|--------|---------|-------------|
| 60001  | Invalid order type | Order type not supported |
| 60002  | Invalid size | Order size invalid |
| 60003  | Invalid price | Order price invalid |
| 60004  | Insufficient margin | Not enough margin |
| 60005  | Position limit exceeded | Maximum position size reached |
| 60006  | Order not found | Order ID does not exist |

## Position Management

### Close Position

Close an open position.

#### HTTP Request

`POST /api/mix/v1/position/close`

#### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| positionSide| string | No    | Position side: long, short |

#### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "orderId": "123456789",
        "symbol": "BTCUSDT",
        "status": "new"
    }
}
```

### Set Leverage

Set leverage for a contract.

#### HTTP Request

`POST /api/mix/v1/position/leverage`

#### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| leverage  | string | Yes      | Leverage value |
| marginMode| string | Yes      | Margin mode: isolated, cross |

#### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "symbol": "BTCUSDT",
        "leverage": "10",
        "marginMode": "isolated"
    }
}
```

## Best Practices

1. **Order Management**
```python
class OrderManager:
    def __init__(self, client):
        self.client = client
        self.open_orders = {}
        
    def place_order_with_retry(self, params, max_retries=3):
        for attempt in range(max_retries):
            try:
                response = self.client.placeOrder(params)
                if response['code'] == '00000':
                    self.open_orders[response['data']['orderId']] = params
                    return response['data']
            except Exception as e:
                if attempt == max_retries - 1:
                    raise e
                time.sleep(0.5 * (attempt + 1))
```

2. **Position Risk Management**
```python
def monitor_position_risk(symbol, risk_threshold=0.1):
    while True:
        position = client.getPosition(symbol)
        unrealized_pnl = float(position['unrealizedPnl'])
        margin_balance = float(position['marginBalance'])
        
        risk_ratio = abs(unrealized_pnl) / margin_balance
        if risk_ratio > risk_threshold:
            reduce_position_size(symbol)
        
        time.sleep(5)
```

3. **Order Tracking**
```python
class OrderTracker:
    def __init__(self):
        self.orders = {}
        self.trades = {}
    
    def update_order(self, order_data):
        order_id = order_data['orderId']
        self.orders[order_id] = order_data
        
        if order_data['status'] == 'filled':
            self.process_filled_order(order_id)
    
    def process_filled_order(self, order_id):
        order = self.orders[order_id]
        # Calculate realized PnL, update positions, etc.
```

4. **Smart Order Routing**
```python
def place_large_order(symbol, side, total_size, max_single_order):
    remaining_size = float(total_size)
    orders = []
    
    while remaining_size > 0:
        order_size = min(remaining_size, max_single_order)
        order = place_order(symbol, side, order_size)
        orders.append(order)
        remaining_size -= order_size
        
        # Add random delay to avoid rate limits
        time.sleep(random.uniform(0.1, 0.3))
    
    return orders
```

## Additional Resources

- [Futures Trading Guide](https://www.bitget.com/support/futures-guide)
- [Position Management](https://www.bitget.com/support/position-management)
- [Risk Management](https://www.bitget.com/support/risk-management)
