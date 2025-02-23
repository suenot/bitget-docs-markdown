# WebSocket Private Data API for Futures

## Overview

The WebSocket Private Data API for Futures provides real-time updates for account information, positions, and orders in futures trading.

## Authentication

### Generate Authentication Parameters

To connect to private channels, you need to generate authentication parameters:

1. Generate timestamp (expires)
2. Generate signature
3. Send authentication message

```python
import time
import hmac
import base64

def generate_auth_params(api_key, api_secret, passphrase):
    # Timestamp for authentication
    timestamp = str(int(time.time()))
    
    # Message to sign
    message = timestamp + 'GET/user/verify'
    
    # Generate signature
    signature = base64.b64encode(
        hmac.new(
            api_secret.encode('utf-8'),
            message.encode('utf-8'),
            digestmod='sha256'
        ).digest()
    ).decode()

    return {
        'op': 'login',
        'args': [{
            'apiKey': api_key,
            'passphrase': passphrase,
            'timestamp': timestamp,
            'sign': signature
        }]
    }
```

### WebSocket Connection

```javascript
const ws = new WebSocket('wss://ws.bitget.com/mix/v1/stream');

// Send authentication message
ws.send(JSON.stringify({
    op: 'login',
    args: [{
        apiKey: 'your_api_key',
        passphrase: 'your_passphrase',
        timestamp: '1621441751927',
        sign: 'generated_signature'
    }]
}));
```

## Available Channels

### Account Updates Channel

Subscribe to account balance and margin updates.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "account",
        "instType": "FUTURES",
        "instId": "default"  // or specific marginCoin
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "account",
        "instType": "FUTURES",
        "instId": "default"
    },
    "data": [{
        "marginCoin": "USDT",
        "available": "1000.00",
        "total": "1500.00",
        "unrealizedPnl": "50.00",
        "marginMode": "cross",
        "marginRatio": "0.05",
        "maintainMargin": "2.00",
        "initialMargin": "10.00",
        "frozenBalance": "450.00",
        "timestamp": 1621441751927
    }]
}
```

### Position Updates Channel

Subscribe to position updates.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "positions",
        "instType": "FUTURES",
        "instId": "BTCUSDT"  // Optional, subscribe to all if not specified
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "positions",
        "instType": "FUTURES",
        "instId": "BTCUSDT"
    },
    "data": [{
        "symbol": "BTCUSDT",
        "positionSide": "long",
        "holdPosition": "0.123",
        "openPrice": "30000.50",
        "markPrice": "31000.00",
        "unrealizedPnl": "123.45",
        "leverage": "10",
        "marginMode": "isolated",
        "isolatedMargin": "500.00",
        "positionMargin": "300.00",
        "maintainMargin": "2.00",
        "marginRate": "0.15",
        "liquidationPrice": "27500.00",
        "timestamp": 1621441751927
    }]
}
```

### Order Updates Channel

Subscribe to order updates.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "orders",
        "instType": "FUTURES",
        "instId": "BTCUSDT"  // Optional, subscribe to all if not specified
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "orders",
        "instType": "FUTURES",
        "instId": "BTCUSDT"
    },
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
        "timestamp": 1621441751927
    }]
}
```

### Execution Updates Channel

Subscribe to trade execution updates.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "execution",
        "instType": "FUTURES",
        "instId": "BTCUSDT"  // Optional, subscribe to all if not specified
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "execution",
        "instType": "FUTURES",
        "instId": "BTCUSDT"
    },
    "data": [{
        "tradeId": "987654321",
        "orderId": "123456789",
        "symbol": "BTCUSDT",
        "side": "buy",
        "price": "30000.00",
        "size": "0.100",
        "fee": "0.00001",
        "feeCurrency": "USDT",
        "role": "maker",
        "timestamp": 1621441751927
    }]
}
```

### Liquidation Warning Channel

Subscribe to liquidation warning notifications.

#### Subscribe Request
```json
{
    "op": "subscribe",
    "args": [{
        "channel": "liquidation",
        "instType": "FUTURES",
        "instId": "BTCUSDT"  // Optional, subscribe to all if not specified
    }]
}
```

#### Update Message
```json
{
    "action": "update",
    "arg": {
        "channel": "liquidation",
        "instType": "FUTURES",
        "instId": "BTCUSDT"
    },
    "data": [{
        "symbol": "BTCUSDT",
        "positionSide": "long",
        "marginMode": "isolated",
        "marginRatio": "0.95",
        "liquidationPrice": "27500.00",
        "currentPrice": "27800.00",
        "warningLevel": "2",
        "timestamp": 1621441751927
    }]
}
```

## Error Handling

### Error Response Format
```json
{
    "event": "error",
    "code": "60001",
    "msg": "Authentication failed"
}
```

### Common Error Codes

| Code   | Message | Description |
|--------|---------|-------------|
| 60001  | Authentication failed | Invalid API key or signature |
| 60002  | Connection limit exceeded | Too many connections |
| 60003  | Rate limit exceeded | Too many messages |
| 60004  | Invalid subscription | Channel or parameters invalid |
| 60005  | Invalid message | Message format error |

## Best Practices

### Connection Management
```python
class FuturesWebSocketClient:
    def __init__(self, api_key, api_secret, passphrase):
        self.api_key = api_key
        self.api_secret = api_secret
        self.passphrase = passphrase
        self.ws = None
        self.subscriptions = set()
        self.reconnect_attempts = 0
        self.max_reconnect_attempts = 5
        
    def connect(self):
        self.ws = websocket.WebSocketApp(
            'wss://ws.bitget.com/mix/v1/stream',
            on_open=self.on_open,
            on_message=self.on_message,
            on_error=self.on_error,
            on_close=self.on_close
        )
        
    def on_open(self, ws):
        self.reconnect_attempts = 0
        self.authenticate()
        self.resubscribe()
        self.start_heartbeat()
        
    def authenticate(self):
        auth_params = generate_auth_params(
            self.api_key,
            self.api_secret,
            self.passphrase
        )
        self.ws.send(json.dumps(auth_params))
        
    def resubscribe(self):
        for subscription in self.subscriptions:
            self.ws.send(json.dumps(subscription))
            
    def start_heartbeat(self):
        def send_ping():
            while self.ws.sock and self.ws.sock.connected:
                self.ws.send(json.dumps({"op": "ping"}))
                time.sleep(20)
        
        threading.Thread(target=send_ping).start()
```

### Position Monitoring
```python
class PositionMonitor:
    def __init__(self):
        self.positions = {}
        self.risk_limits = {
            'margin_ratio_threshold': 0.8,
            'unrealized_pnl_threshold': -1000
        }
        
    def process_position_update(self, position_data):
        symbol = position_data['symbol']
        self.positions[symbol] = position_data
        self.check_position_risk(symbol)
        
    def check_position_risk(self, symbol):
        position = self.positions[symbol]
        margin_ratio = float(position['marginRate'])
        unrealized_pnl = float(position['unrealizedPnl'])
        
        if margin_ratio > self.risk_limits['margin_ratio_threshold']:
            self.handle_high_margin_ratio(symbol, position)
            
        if unrealized_pnl < self.risk_limits['unrealized_pnl_threshold']:
            self.handle_large_loss(symbol, position)
            
    def handle_high_margin_ratio(self, symbol, position):
        # Implement risk reduction strategy
        logger.warning(f"High margin ratio for {symbol}: {position['marginRate']}")
        # Consider reducing position size or adding margin
        
    def handle_large_loss(self, symbol, position):
        logger.warning(f"Large unrealized loss for {symbol}: {position['unrealizedPnl']}")
        # Consider implementing stop loss
```

### Order Management
```python
class OrderManager:
    def __init__(self):
        self.orders = {}
        self.executions = {}
        
    def process_order_update(self, order_data):
        order_id = order_data['orderId']
        self.orders[order_id] = order_data
        self.update_order_status(order_id)
        
    def process_execution_update(self, execution_data):
        trade_id = execution_data['tradeId']
        order_id = execution_data['orderId']
        
        self.executions[trade_id] = execution_data
        if order_id in self.orders:
            self.update_order_fills(order_id, execution_data)
            
    def update_order_status(self, order_id):
        order = self.orders[order_id]
        if order['status'] == 'filled':
            self.handle_order_filled(order)
        elif order['status'] == 'cancelled':
            self.handle_order_cancelled(order)
            
    def update_order_fills(self, order_id, execution):
        order = self.orders[order_id]
        # Update filled size and average price
        filled_size = float(order['filledSize'])
        new_fill = float(execution['size'])
        total_filled = filled_size + new_fill
        
        # Calculate new average price
        avg_price = (float(order['avgPrice']) * filled_size + 
                    float(execution['price']) * new_fill) / total_filled
                    
        order['filledSize'] = str(total_filled)
        order['avgPrice'] = str(avg_price)
```

### Account Monitoring
```python
class AccountMonitor:
    def __init__(self):
        self.account_data = {}
        self.risk_metrics = {
            'margin_ratio_limit': 0.9,
            'available_balance_minimum': 100
        }
        
    def process_account_update(self, account_data):
        self.account_data = account_data
        self.check_account_risk()
        
    def check_account_risk(self):
        margin_ratio = float(self.account_data['marginRatio'])
        available_balance = float(self.account_data['available'])
        
        if margin_ratio > self.risk_metrics['margin_ratio_limit']:
            self.handle_high_account_risk()
            
        if available_balance < self.risk_metrics['available_balance_minimum']:
            self.handle_low_balance()
            
    def calculate_account_metrics(self):
        return {
            'total_equity': float(self.account_data['total']),
            'used_margin': float(self.account_data['total']) - float(self.account_data['available']),
            'margin_ratio': float(self.account_data['marginRatio']),
            'unrealized_pnl': float(self.account_data['unrealizedPnl'])
        }
```

## Additional Resources

- [WebSocket API Documentation](https://bitgetlimited.github.io/apidoc/en/mix/#websocket-private)
- [Rate Limits](https://bitgetlimited.github.io/apidoc/en/mix/#rate-limits)
- [Error Codes](https://bitgetlimited.github.io/apidoc/en/mix/#error-codes)
