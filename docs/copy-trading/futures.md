# Copy Trading Futures API

## Overview

The Copy Trading Futures API allows users to copy trades from professional traders automatically. This includes following traders, managing copy settings, and viewing copy trading performance.

## Trader Information

### Get Trader List

Get list of available traders to follow.

#### HTTP Request

`GET /api/mix/v1/copytrading/traders`

#### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| pageSize  | int    | No       | Page size (default: 20, max: 100) |
| pageNo    | int    | No       | Page number (starts from 1) |
| sortBy    | string | No       | Sort field: profit, followers |
| sortType  | string | No       | Sort type: asc, desc |

#### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "totalPage": 5,
        "totalNum": 100,
        "currentPage": 1,
        "pageSize": 20,
        "list": [{
            "traderId": "123456789",
            "nickName": "CryptoMaster",
            "followersCount": 1000,
            "totalProfit": "150.45",
            "monthlyProfit": "12.34",
            "winRate": "0.65",
            "description": "Professional crypto trader",
            "tradingDays": 365,
            "riskLevel": "medium",
            "status": "active"
        }]
    }
}
```

### Get Trader Details

Get detailed information about a specific trader.

#### HTTP Request

`GET /api/mix/v1/copytrading/trader/detail`

#### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| traderId  | string | Yes      | Trader ID |

#### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "traderId": "123456789",
        "nickName": "CryptoMaster",
        "followersCount": 1000,
        "totalProfit": "150.45",
        "monthlyProfit": "12.34",
        "winRate": "0.65",
        "description": "Professional crypto trader",
        "tradingDays": 365,
        "riskLevel": "medium",
        "status": "active",
        "statistics": {
            "totalTrades": 1000,
            "winningTrades": 650,
            "averageHoldingTime": "2.5",
            "maxDrawdown": "15.5",
            "sharpeRatio": "2.1"
        },
        "positions": [{
            "symbol": "BTCUSDT",
            "side": "long",
            "size": "0.1",
            "entryPrice": "30000.50",
            "leverage": "10",
            "unrealizedPnl": "123.45"
        }]
    }
}
```

## Copy Trading Management

### Start Copy Trading

Start copying trades from a trader.

#### HTTP Request

`POST /api/mix/v1/copytrading/follow`

#### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| traderId  | string | Yes      | Trader ID to follow |
| copyAmount| string | Yes      | Amount to use for copy trading |
| leverage  | string | Yes      | Copy trading leverage |
| marginMode| string | Yes      | Margin mode: isolated, cross |

#### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "followId": "987654321",
        "traderId": "123456789",
        "copyAmount": "1000",
        "leverage": "10",
        "marginMode": "isolated",
        "status": "active"
    }
}
```

### Stop Copy Trading

Stop copying trades from a trader.

#### HTTP Request

`POST /api/mix/v1/copytrading/stop`

#### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| followId  | string | Yes      | Follow ID |

#### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "followId": "987654321",
        "status": "stopped"
    }
}
```

### Update Copy Settings

Update copy trading settings.

#### HTTP Request

`POST /api/mix/v1/copytrading/updateSettings`

#### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| followId  | string | Yes      | Follow ID |
| copyAmount| string | No       | New copy amount |
| leverage  | string | No       | New leverage |
| takeProfit| string | No       | Take profit percentage |
| stopLoss  | string | No       | Stop loss percentage |

#### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "followId": "987654321",
        "copyAmount": "1500",
        "leverage": "10",
        "takeProfit": "50",
        "stopLoss": "20",
        "status": "active"
    }
}
```

## Copy Trading Performance

### Get Copy Trading History

Get history of copied trades.

#### HTTP Request

`GET /api/mix/v1/copytrading/history`

#### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| followId  | string | Yes      | Follow ID |
| startTime | long   | No       | Start time in milliseconds |
| endTime   | long   | No       | End time in milliseconds |
| pageSize  | int    | No       | Page size (default: 20, max: 100) |
| pageNo    | int    | No       | Page number (starts from 1) |

#### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "totalPage": 5,
        "totalNum": 100,
        "currentPage": 1,
        "pageSize": 20,
        "list": [{
            "orderId": "123456789",
            "symbol": "BTCUSDT",
            "side": "buy",
            "size": "0.1",
            "entryPrice": "30000.50",
            "exitPrice": "31000.50",
            "profit": "100.00",
            "leverage": "10",
            "status": "closed",
            "createTime": 1621441751927,
            "closeTime": 1621441851927
        }]
    }
}
```

### Get Current Positions

Get current copy trading positions.

#### HTTP Request

`GET /api/mix/v1/copytrading/positions`

#### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| followId  | string | Yes      | Follow ID |

#### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "symbol": "BTCUSDT",
        "side": "long",
        "size": "0.1",
        "entryPrice": "30000.50",
        "markPrice": "31000.50",
        "unrealizedPnl": "100.00",
        "leverage": "10",
        "marginMode": "isolated",
        "createTime": 1621441751927
    }]
}
```

## Error Codes

| Code   | Message | Description |
|--------|---------|-------------|
| 80001  | Invalid trader | Trader not found or inactive |
| 80002  | Invalid follow settings | Copy settings invalid |
| 80003  | Insufficient balance | Not enough balance for copy trading |
| 80004  | Copy limit exceeded | Maximum copy trading limit reached |
| 80005  | Already following | Already following this trader |

## Best Practices

### Risk Management

```python
def calculate_copy_amount(account_balance, risk_percentage=5):
    """
    Calculate safe copy amount based on account balance and risk percentage
    """
    max_copy_amount = float(account_balance) * (risk_percentage / 100)
    return str(max_copy_amount)

def set_risk_parameters(follow_id):
    """
    Set risk management parameters for copy trading
    """
    # Set take profit at 50% and stop loss at 20%
    client.updateCopySettings({
        'followId': follow_id,
        'takeProfit': '50',
        'stopLoss': '20'
    })

def monitor_copy_performance(follow_id):
    """
    Monitor copy trading performance and adjust if needed
    """
    while True:
        try:
            positions = client.getCopyPositions(follow_id)
            
            for position in positions:
                unrealized_pnl = float(position['unrealizedPnl'])
                entry_price = float(position['entryPrice'])
                current_price = float(position['markPrice'])
                
                # Calculate current PnL percentage
                pnl_percentage = (unrealized_pnl / (entry_price * float(position['size']))) * 100
                
                # Adjust risk parameters based on performance
                if pnl_percentage < -10:
                    reduce_position_risk(follow_id)
                elif pnl_percentage > 30:
                    lock_in_profits(follow_id)
                    
            time.sleep(60)  # Check every minute
            
        except Exception as e:
            log_error(f"Performance monitoring error: {e}")
            time.sleep(5)
```

### Trader Selection

```python
def analyze_trader_performance(trader_id):
    """
    Analyze trader's performance metrics
    """
    trader = client.getTraderDetails(trader_id)
    
    # Calculate risk-adjusted return
    sharpe_ratio = float(trader['statistics']['sharpeRatio'])
    max_drawdown = float(trader['statistics']['maxDrawdown'])
    win_rate = float(trader['winRate'])
    
    # Score trader based on multiple factors
    score = calculate_trader_score(sharpe_ratio, max_drawdown, win_rate)
    
    return {
        'trader_id': trader_id,
        'score': score,
        'risk_level': trader['riskLevel'],
        'recommended_copy_amount': calculate_recommended_copy_amount(score)
    }

def find_best_traders(min_trading_days=30, min_win_rate=0.6):
    """
    Find the best traders based on criteria
    """
    traders = client.getTraderList({
        'sortBy': 'profit',
        'sortType': 'desc',
        'pageSize': 100
    })
    
    qualified_traders = []
    for trader in traders['list']:
        if (int(trader['tradingDays']) >= min_trading_days and
            float(trader['winRate']) >= min_win_rate):
            performance = analyze_trader_performance(trader['traderId'])
            qualified_traders.append(performance)
    
    return sorted(qualified_traders, key=lambda x: x['score'], reverse=True)
```

### Portfolio Management

```python
def diversify_copy_portfolio(total_amount, max_traders=5):
    """
    Distribute copy trading amount across multiple traders
    """
    best_traders = find_best_traders()
    amount_per_trader = float(total_amount) / min(len(best_traders), max_traders)
    
    portfolio = []
    for i, trader in enumerate(best_traders):
        if i >= max_traders:
            break
            
        # Start copy trading with calculated amount
        follow_response = client.startCopyTrading({
            'traderId': trader['trader_id'],
            'copyAmount': str(amount_per_trader),
            'leverage': '10',
            'marginMode': 'isolated'
        })
        
        portfolio.append({
            'trader_id': trader['trader_id'],
            'follow_id': follow_response['followId'],
            'amount': amount_per_trader
        })
    
    return portfolio
```

## Additional Resources

- [Copy Trading Guide](https://www.bitget.com/support/copy-trading-guide)
- [Risk Management in Copy Trading](https://www.bitget.com/support/copy-trading-risk)
- [Trader Selection Guide](https://www.bitget.com/support/choosing-traders)
