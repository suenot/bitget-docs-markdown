# Margin Trading API

## Overview

Margin trading allows you to borrow funds to increase your trading position, providing the potential for higher profits but also carrying higher risks.

## Get Margin Account Information

Get basic information about your margin account.

### HTTP Request

`GET /api/spot/v1/margin/account`

### Parameters

None

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "userId": "123456789",
        "marginLevel": "2.5",
        "totalAssetInBTC": "1.23456789",
        "totalLiabilityInBTC": "0.5",
        "totalNetAssetInBTC": "0.73456789",
        "status": "normal"
    }
}
```

## Get Margin Asset Information

Get detailed information about margin assets.

### HTTP Request

`GET /api/spot/v1/margin/assets`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| coinId    | string | No       | Filter by coin |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "coinId": "BTC",
        "available": "1.23456789",
        "borrowed": "0.5",
        "interest": "0.0001",
        "totalAsset": "1.73456789",
        "btcValue": "1.73456789",
        "usdValue": "45678.12",
        "maxBorrowable": "2.0",
        "liquidationPrice": "25000.00"
    }]
}
```

## Borrow Funds

Borrow funds for margin trading.

### HTTP Request

`POST /api/spot/v1/margin/borrow`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| coinId    | string | Yes      | Coin to borrow |
| amount    | string | Yes      | Amount to borrow |
| term      | int    | No       | Loan term in days (default: 7) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "orderId": "123456789",
        "coinId": "BTC",
        "amount": "0.5",
        "interest": "0.0001",
        "term": 7,
        "status": "success",
        "createTime": 1621441751927
    }
}
```

## Repay Loan

Repay borrowed funds.

### HTTP Request

`POST /api/spot/v1/margin/repay`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| coinId    | string | Yes      | Coin to repay |
| amount    | string | Yes      | Amount to repay |
| orderId   | string | No       | Specific loan order to repay |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "repayId": "123456789",
        "orderId": "123456789",
        "coinId": "BTC",
        "amount": "0.5",
        "interest": "0.0001",
        "status": "success",
        "createTime": 1621441751927
    }
}
```

## Get Loan History

Get history of borrowed funds.

### HTTP Request

`GET /api/spot/v1/margin/loans`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| coinId    | string | No       | Filter by coin |
| status    | string | No       | Status: ongoing, repaid |
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
        "coinId": "BTC",
        "amount": "0.5",
        "interest": "0.0001",
        "term": 7,
        "status": "ongoing",
        "createTime": 1621441751927,
        "updateTime": 1621441751927
    }]
}
```

## Get Interest History

Get history of interest payments.

### HTTP Request

`GET /api/spot/v1/margin/interest`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| coinId    | string | No       | Filter by coin |
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
        "coinId": "BTC",
        "interest": "0.0001",
        "interestRate": "0.0005",
        "createTime": 1621441751927
    }]
}
```

## Get Liquidation History

Get history of liquidations.

### HTTP Request

`GET /api/spot/v1/margin/liquidations`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| coinId    | string | No       | Filter by coin |
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
        "coinId": "BTC",
        "amount": "0.5",
        "liquidationPrice": "25000.00",
        "status": "completed",
        "createTime": 1621441751927
    }]
}
```

## Error Codes

| Code   | Message | Description |
|--------|---------|-------------|
| 40001  | Insufficient margin level | Margin level is too low |
| 40002  | Max borrowing limit reached | Cannot borrow more |
| 40003  | Insufficient collateral | Not enough collateral |
| 40004  | Liquidation in progress | Account is being liquidated |
| 40005  | Amount too small | Amount is below minimum limit |
| 40006  | Amount too large | Amount exceeds maximum limit |

## Risk Management

### Margin Level Calculation

```
Margin Level = Total Assets / Total Liabilities

Where:
- Total Assets = Sum of all asset values in BTC
- Total Liabilities = Sum of borrowed funds + interest in BTC
```

### Liquidation Process

1. Warning levels:
   - Initial Margin Level: 1.5
   - Maintenance Margin Level: 1.1
   - Liquidation Level: 1.0

2. Actions at each level:
   - Initial Margin Level: Cannot borrow more
   - Maintenance Margin Level: Warning notification
   - Liquidation Level: Position liquidation begins

## Best Practices

1. **Risk Management**
   ```python
   def check_margin_level():
       account = client.getMarginAccount()
       margin_level = float(account['data']['marginLevel'])
       
       if margin_level < 1.5:
           notify_low_margin_level()
       if margin_level < 1.1:
           start_risk_reduction()
   ```

2. **Borrowing Strategy**
   ```python
   def calculate_safe_borrow_amount(coin_id):
       asset = client.getMarginAsset(coin_id)
       max_borrowable = float(asset['maxBorrowable'])
       
       # Only borrow up to 70% of max to maintain safety margin
       safe_amount = max_borrowable * 0.7
       return safe_amount
   ```

3. **Interest Management**
   ```python
   def monitor_interest_costs():
       loans = client.getLoans(status='ongoing')
       total_interest = sum(float(loan['interest']) for loan in loans)
       
       if total_interest > INTEREST_THRESHOLD:
           consider_loan_repayment()
   ```

4. **Liquidation Prevention**
   ```python
   def monitor_liquidation_risk():
       assets = client.getMarginAssets()
       
       for asset in assets:
           current_price = get_market_price(asset['coinId'])
           distance_to_liquidation = (
               float(current_price) - float(asset['liquidationPrice'])
           ) / float(current_price)
           
           if distance_to_liquidation < 0.2:  # 20% buffer
               reduce_position_risk(asset['coinId'])
   ```

## Additional Resources

- [Margin Trading Guide](https://www.bitget.com/support/margin-trading)
- [Risk Management Guide](https://www.bitget.com/support/risk-management)
- [Fee Structure](https://www.bitget.com/support/margin-fees)
