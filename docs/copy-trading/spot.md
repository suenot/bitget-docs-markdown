# Copy Trading Spot API

## Overview

The Copy Trading Spot API enables users to automatically copy spot trading strategies from professional traders. This includes following traders, managing copy settings, and monitoring copy trading performance.

## Trader Information

### Get Trader List

Get list of available spot traders to follow.

#### HTTP Request

`GET /api/spot/v1/copytrading/traders`

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
            "nickName": "SpotMaster",
            "followersCount": 500,
            "totalProfit": "75.45",
            "monthlyProfit": "8.34",
            "winRate": "0.70",
            "description": "Spot trading specialist",
            "tradingDays": 180,
            "riskLevel": "low",
            "status": "active"
        }]
    }
}
```

### Get Trader Details

Get detailed information about a specific spot trader.

#### HTTP Request

`GET /api/spot/v1/copytrading/trader/detail`

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
        "nickName": "SpotMaster",
        "followersCount": 500,
        "totalProfit": "75.45",
        "monthlyProfit": "8.34",
        "winRate": "0.70",
        "description": "Spot trading specialist",
        "tradingDays": 180,
        "riskLevel": "low",
        "status": "active",
        "statistics": {
            "totalTrades": 500,
            "winningTrades": 350,
            "averageHoldingTime": "5.2",
            "maxDrawdown": "8.5",
            "sharpeRatio": "2.5"
        },
        "holdings": [{
            "symbol": "BTC",
            "amount": "0.05",
            "averagePrice": "30000.50",
            "currentPrice": "31000.00",
            "unrealizedPnl": "50.00"
        }]
    }
}
```

## Copy Trading Management

### Start Copy Trading

Start copying trades from a spot trader.

#### HTTP Request

`POST /api/spot/v1/copytrading/follow`

#### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| traderId  | string | Yes      | Trader ID to follow |
| copyAmount| string | Yes      | Amount to use for copy trading |
| quoteCoin | string | Yes      | Quote currency (e.g., USDT) |
| copyMode  | string | Yes      | Copy mode: fixed_amount, fixed_ratio |

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
        "quoteCoin": "USDT",
        "copyMode": "fixed_amount",
        "status": "active"
    }
}
```

### Stop Copy Trading

Stop copying trades from a spot trader.

#### HTTP Request

`POST /api/spot/v1/copytrading/stop`

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

Update spot copy trading settings.

#### HTTP Request

`POST /api/spot/v1/copytrading/updateSettings`

#### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| followId  | string | Yes      | Follow ID |
| copyAmount| string | No       | New copy amount |
| copyMode  | string | No       | New copy mode |
| maxPositions| int  | No       | Maximum number of positions |
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
        "copyMode": "fixed_amount",
        "maxPositions": 10,
        "stopLoss": "10",
        "status": "active"
    }
}
```

## Copy Trading Performance

### Get Copy Trading History

Get history of copied spot trades.

#### HTTP Request

`GET /api/spot/v1/copytrading/history`

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
            "symbol": "BTC/USDT",
            "side": "buy",
            "amount": "0.01",
            "price": "30000.50",
            "value": "300.00",
            "fee": "0.30",
            "feeCoin": "USDT",
            "status": "filled",
            "createTime": 1621441751927,
            "updateTime": 1621441851927
        }]
    }
}
```

### Get Current Holdings

Get current copy trading holdings.

#### HTTP Request

`GET /api/spot/v1/copytrading/holdings`

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
        "symbol": "BTC",
        "amount": "0.01",
        "averagePrice": "30000.50",
        "currentPrice": "31000.00",
        "value": "310.00",
        "unrealizedPnl": "10.00",
        "createTime": 1621441751927
    }]
}
```

## Error Codes

| Code   | Message | Description |
|--------|---------|-------------|
| 90001  | Invalid trader | Spot trader not found or inactive |
| 90002  | Invalid copy settings | Copy settings invalid |
| 90003  | Insufficient balance | Not enough balance for copy trading |
| 90004  | Copy limit exceeded | Maximum copy trading limit reached |
| 90005  | Already following | Already following this trader |

## Best Practices

### Portfolio Allocation

```python
def calculate_portfolio_allocation(total_amount, risk_level='medium'):
    """
    Calculate optimal portfolio allocation for spot copy trading
    """
    allocation = {
        'low': {
            'max_per_trader': 0.2,  # 20% max per trader
            'max_traders': 8,
            'min_trading_days': 90
        },
        'medium': {
            'max_per_trader': 0.3,  # 30% max per trader
            'max_traders': 5,
            'min_trading_days': 60
        },
        'high': {
            'max_per_trader': 0.4,  # 40% max per trader
            'max_traders': 3,
            'min_trading_days': 30
        }
    }
    
    risk_params = allocation[risk_level]
    max_amount_per_trader = float(total_amount) * risk_params['max_per_trader']
    
    return {
        'max_amount_per_trader': str(max_amount_per_trader),
        'recommended_traders': risk_params['max_traders'],
        'min_trading_days': risk_params['min_trading_days']
    }
```

### Trader Selection Strategy

```python
def evaluate_spot_trader(trader_id):
    """
    Evaluate spot trader's performance and suitability
    """
    trader = client.getTraderDetails(trader_id)
    
    # Calculate consistency score
    monthly_profit = float(trader['monthlyProfit'])
    total_profit = float(trader['totalProfit'])
    trading_days = int(trader['tradingDays'])
    
    consistency = calculate_consistency_score(monthly_profit, total_profit, trading_days)
    
    # Calculate risk score
    max_drawdown = float(trader['statistics']['maxDrawdown'])
    win_rate = float(trader['winRate'])
    
    risk_score = calculate_risk_score(max_drawdown, win_rate)
    
    return {
        'trader_id': trader_id,
        'consistency_score': consistency,
        'risk_score': risk_score,
        'overall_rating': (consistency + risk_score) / 2
    }

def find_suitable_traders(risk_preference='medium'):
    """
    Find suitable traders based on risk preference
    """
    risk_filters = {
        'low': {'min_win_rate': 0.7, 'max_drawdown': 10},
        'medium': {'min_win_rate': 0.6, 'max_drawdown': 15},
        'high': {'min_win_rate': 0.5, 'max_drawdown': 20}
    }
    
    filters = risk_filters[risk_preference]
    
    traders = client.getTraderList({
        'sortBy': 'profit',
        'sortType': 'desc',
        'pageSize': 100
    })
    
    suitable_traders = []
    for trader in traders['list']:
        if (float(trader['winRate']) >= filters['min_win_rate'] and
            float(trader['statistics']['maxDrawdown']) <= filters['max_drawdown']):
            evaluation = evaluate_spot_trader(trader['traderId'])
            suitable_traders.append(evaluation)
    
    return sorted(suitable_traders, key=lambda x: x['overall_rating'], reverse=True)
```

### Risk Management

```python
def monitor_copy_performance(follow_id, risk_threshold=0.1):
    """
    Monitor and manage copy trading risk
    """
    while True:
        try:
            holdings = client.getCopyHoldings(follow_id)
            total_value = sum(float(holding['value']) for holding in holdings)
            total_pnl = sum(float(holding['unrealizedPnl']) for holding in holdings)
            
            # Calculate current risk exposure
            risk_exposure = abs(total_pnl) / total_value
            
            if risk_exposure > risk_threshold:
                # Implement risk reduction strategy
                reduce_risk_exposure(follow_id, holdings)
            
            time.sleep(300)  # Check every 5 minutes
            
        except Exception as e:
            log_error(f"Performance monitoring error: {e}")
            time.sleep(60)

def reduce_risk_exposure(follow_id, holdings):
    """
    Implement risk reduction strategy
    """
    # Sort holdings by unrealized PnL
    sorted_holdings = sorted(holdings, 
                           key=lambda x: float(x['unrealizedPnl']))
    
    # Close worst performing positions first
    for holding in sorted_holdings:
        if float(holding['unrealizedPnl']) < 0:
            client.closePosition(follow_id, holding['symbol'])
```

### Copy Trading Optimization

```python
def optimize_copy_settings(follow_id, performance_history):
    """
    Optimize copy trading settings based on performance
    """
    # Analyze past performance
    win_rate = calculate_win_rate(performance_history)
    avg_profit = calculate_average_profit(performance_history)
    max_drawdown = calculate_max_drawdown(performance_history)
    
    # Adjust settings based on performance metrics
    if win_rate > 0.7 and max_drawdown < 10:
        # Increase position size for good performance
        increase_copy_amount(follow_id)
    elif win_rate < 0.4 or max_drawdown > 20:
        # Reduce risk for poor performance
        reduce_copy_amount(follow_id)
    
    # Update stop loss based on volatility
    volatility = calculate_volatility(performance_history)
    update_stop_loss(follow_id, volatility)
```

## Additional Resources

- [Spot Copy Trading Guide](https://www.bitget.com/support/spot-copy-guide)
- [Risk Management in Spot Copy Trading](https://www.bitget.com/support/spot-copy-risk)
- [Choosing Spot Traders](https://www.bitget.com/support/spot-trader-selection)
