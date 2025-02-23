# Futures Account API

## Overview

The Futures Account API provides access to account information, including balance details, positions, and risk management settings for futures trading.

## Get Account Information

Get basic information about your futures account.

### HTTP Request

`GET /api/mix/v1/account/info`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| marginCoin| string | Yes      | Margin coin (e.g., USDT) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "marginCoin": "USDT",
        "accountType": "futures",
        "available": "1000.00",
        "total": "1500.00",
        "unrealizedPnl": "50.00",
        "marginMode": "cross",
        "riskLevel": "normal",
        "maintenanceMarginRate": "0.004",
        "marginBalance": "1550.00",
        "initialMarginRate": "0.01"
    }
}
```

## Get Positions

Get current positions information.

### HTTP Request

`GET /api/mix/v1/position/allPosition`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| marginCoin| string | Yes      | Margin coin (e.g., USDT) |
| symbol    | string | No       | Contract symbol |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
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
        "createTime": 1621441751927,
        "updateTime": 1621441751927
    }]
}
```

## Get Account Bills

Get account bill records.

### HTTP Request

`GET /api/mix/v1/account/bills`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| marginCoin| string | Yes      | Margin coin (e.g., USDT) |
| startTime | long   | No       | Start time in milliseconds |
| endTime   | long   | No       | End time in milliseconds |
| type      | string | No       | Bill type: deposit, withdraw, fee, pnl |
| limit     | int    | No       | Number of records (default: 100, max: 500) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "id": "123456789",
        "symbol": "BTCUSDT",
        "type": "realized_pnl",
        "amount": "123.45",
        "fee": "0.12345",
        "createTime": 1621441751927,
        "info": "Close long position"
    }]
}
```

## Set Risk Limit

Set position risk limit.

### HTTP Request

`POST /api/mix/v1/account/setPositionRiskLimit`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| riskLimit | string | Yes      | Risk limit value |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "symbol": "BTCUSDT",
        "riskLimit": "100",
        "maxPositionSize": "10"
    }
}
```

## Set Margin Mode

Set margin mode for futures trading.

### HTTP Request

`POST /api/mix/v1/account/setMarginMode`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| marginCoin| string | Yes      | Margin coin (e.g., USDT) |
| marginMode| string | Yes      | Margin mode: isolated, cross |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "marginCoin": "USDT",
        "marginMode": "isolated"
    }
}
```

## Set Position Mode

Set position mode for futures trading.

### HTTP Request

`POST /api/mix/v1/account/setPositionMode`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| symbol    | string | Yes      | Contract symbol |
| positionMode| string | Yes    | Position mode: hedged, one_way |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "symbol": "BTCUSDT",
        "positionMode": "hedged"
    }
}
```

## Get Risk Limits

Get risk limit configuration.

### HTTP Request

`GET /api/mix/v1/account/riskLimit`

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
    "data": [{
        "riskLimit": "100",
        "maxPositionSize": "10",
        "maintainMargin": "0.004",
        "initialMargin": "0.01",
        "maxLeverage": "100"
    }]
}
```

## Error Codes

| Code   | Message | Description |
|--------|---------|-------------|
| 70001  | Invalid margin mode | Margin mode not supported |
| 70002  | Invalid position mode | Position mode not supported |
| 70003  | Risk limit exceeded | Position size exceeds risk limit |
| 70004  | Insufficient margin | Not enough margin for operation |
| 70005  | Account in liquidation | Account is being liquidated |

## Risk Management

### Position Risk Calculation

```python
def calculate_position_risk(position):
    """
    Calculate position risk metrics
    """
    # Liquidation Price = Entry Price ± (Entry Price × Initial Margin ± Maintenance Margin)
    entry_price = float(position['openPrice'])
    leverage = float(position['leverage'])
    position_size = float(position['holdPosition'])
    
    initial_margin = entry_price * position_size / leverage
    maintenance_margin = initial_margin * float(position['maintainMargin'])
    
    if position['positionSide'] == 'long':
        liquidation_price = entry_price * (1 - 1/leverage + float(position['maintainMargin']))
    else:
        liquidation_price = entry_price * (1 + 1/leverage - float(position['maintainMargin']))
        
    return {
        'liquidation_price': liquidation_price,
        'margin_ratio': float(position['marginRate']),
        'risk_level': get_risk_level(float(position['marginRate']))
    }
```

### Risk Level Management

```python
def monitor_account_risk():
    """
    Monitor and manage account risk
    """
    while True:
        try:
            account = client.getAccountInfo()
            positions = client.getPositions()
            
            # Check overall account risk
            risk_ratio = float(account['total']) / float(account['marginBalance'])
            if risk_ratio < 1.1:  # High risk
                reduce_positions(positions)
            
            # Check individual position risks
            for position in positions:
                risk = calculate_position_risk(position)
                if risk['margin_ratio'] > 0.8:  # High risk position
                    reduce_position_size(position)
                
            time.sleep(5)  # Check every 5 seconds
            
        except Exception as e:
            log_error(f"Risk monitoring error: {e}")
            time.sleep(1)
```

### Position Size Management

```python
def calculate_safe_position_size(symbol, leverage):
    """
    Calculate safe position size based on account balance and risk parameters
    """
    account = client.getAccountInfo()
    available_balance = float(account['available'])
    
    # Use maximum 20% of available balance for any single position
    max_position_value = available_balance * 0.2 * leverage
    
    ticker = client.getTicker(symbol)
    current_price = float(ticker['lastPrice'])
    
    max_position_size = max_position_value / current_price
    return max_position_size
```

### Margin Management

```python
def adjust_position_margin(symbol, side, action):
    """
    Adjust position margin based on market conditions
    """
    position = client.getPosition(symbol, side)
    
    if action == 'increase':
        # Increase margin if position is profitable
        if float(position['unrealizedPnl']) > 0:
            add_margin = float(position['positionMargin']) * 0.1
            client.adjustPositionMargin(symbol, side, add_margin)
            
    elif action == 'decrease':
        # Decrease margin if position is well-protected
        if float(position['marginRate']) < 0.3:
            remove_margin = float(position['positionMargin']) * 0.1
            client.adjustPositionMargin(symbol, side, -remove_margin)
```

## Best Practices

1. **Regular Risk Assessment**
   - Monitor account health metrics
   - Track position exposure
   - Set up alerts for risk thresholds

2. **Position Management**
   - Diversify across multiple contracts
   - Maintain appropriate leverage levels
   - Use stop-loss orders

3. **Margin Management**
   - Maintain adequate margin buffer
   - Regular margin health checks
   - Automated margin adjustments

4. **Account Security**
   - Enable two-factor authentication
   - Use API key restrictions
   - Regular security audits

## Additional Resources

- [Risk Management Guide](https://www.bitget.com/support/risk-management)
- [Margin Modes Explained](https://www.bitget.com/support/margin-modes)
- [Position Modes Guide](https://www.bitget.com/support/position-modes)
