# Spot Account API

## Get Account Information

Get basic information about your spot account.

### HTTP Request

`GET /api/spot/v1/account/getInfo`

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
        "accountType": "spot",
        "accountLevel": "1",
        "makerFeeRate": "0.001",
        "takerFeeRate": "0.001",
        "status": "normal"
    }
}
```

## Get Account Balance

Get the balance of all assets in your spot account.

### HTTP Request

`GET /api/spot/v1/account/assets`

### Parameters

None

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "coinId": "BTC",
        "available": "1.23456789",
        "frozen": "0.12345678",
        "locked": "0",
        "btcValue": "1.23456789",
        "usdValue": "45678.12"
    }]
}
```

## Get Account History

Get the history of account transactions.

### HTTP Request

`GET /api/spot/v1/account/history`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| coinId    | string | No       | Filter by coin |
| startTime | long   | No       | Start time in milliseconds |
| endTime   | long   | No       | End time in milliseconds |
| type      | string | No       | Transaction type: deposit, withdraw, transfer |
| limit     | int    | No       | Number of records (default: 100, max: 500) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": [{
        "id": "123456789",
        "coinId": "BTC",
        "type": "deposit",
        "amount": "1.23456789",
        "status": "success",
        "createTime": 1621441751927,
        "updateTime": 1621441751927,
        "txId": "0x1234567890abcdef"
    }]
}
```

## Get Deposit Address

Get the deposit address for a specific coin.

### HTTP Request

`GET /api/spot/v1/account/depositAddress`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| coinId    | string | Yes      | Coin ID |
| network   | string | No       | Network type (e.g., ERC20, TRC20) |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "coinId": "BTC",
        "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa",
        "memo": "",
        "chain": "BTC",
        "needTag": false
    }
}
```

## Get Deposit History

Get the history of deposits.

### HTTP Request

`GET /api/spot/v1/account/deposits`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| coinId    | string | No       | Filter by coin |
| status    | string | No       | Status: pending, success, failed |
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
        "id": "123456789",
        "coinId": "BTC",
        "amount": "1.23456789",
        "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa",
        "txId": "0x1234567890abcdef",
        "network": "BTC",
        "status": "success",
        "confirmations": 6,
        "createTime": 1621441751927,
        "updateTime": 1621441751927
    }]
}
```

## Get Withdrawal History

Get the history of withdrawals.

### HTTP Request

`GET /api/spot/v1/account/withdrawals`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| coinId    | string | No       | Filter by coin |
| status    | string | No       | Status: pending, success, failed |
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
        "id": "123456789",
        "coinId": "BTC",
        "amount": "1.23456789",
        "fee": "0.0001",
        "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa",
        "txId": "0x1234567890abcdef",
        "network": "BTC",
        "status": "success",
        "createTime": 1621441751927,
        "updateTime": 1621441751927
    }]
}
```

## Submit Withdrawal Request

Submit a withdrawal request.

### HTTP Request

`POST /api/spot/v1/account/withdraw`

### Parameters

| Parameter | Type   | Required | Description |
|-----------|--------|----------|-------------|
| coinId    | string | Yes      | Coin ID |
| address   | string | Yes      | Withdrawal address |
| amount    | string | Yes      | Withdrawal amount |
| network   | string | No       | Network type (e.g., ERC20, TRC20) |
| memo      | string | No       | Memo/Tag for coins that require it |
| chain     | string | No       | Blockchain network |

### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "id": "123456789",
        "coinId": "BTC",
        "amount": "1.23456789",
        "fee": "0.0001",
        "status": "pending",
        "createTime": 1621441751927
    }
}
```

## Account Settings

### Get Account Settings

Get current account settings.

#### HTTP Request

`GET /api/spot/v1/account/settings`

#### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "apiTrading": true,
        "withdrawals": true,
        "autoLending": false,
        "twoFactorAuth": true
    }
}
```

### Update Account Settings

Update account settings.

#### HTTP Request

`POST /api/spot/v1/account/settings`

#### Parameters

| Parameter     | Type    | Required | Description |
|--------------|---------|----------|-------------|
| apiTrading   | boolean | No       | Enable/disable API trading |
| withdrawals  | boolean | No       | Enable/disable withdrawals |
| autoLending  | boolean | No       | Enable/disable auto-lending |

#### Response

```json
{
    "code": "00000",
    "msg": "success",
    "requestTime": 1621441751927,
    "data": {
        "updated": true
    }
}
```

## Error Codes

| Code   | Message | Description |
|--------|---------|-------------|
| 30001  | Insufficient balance | Account balance is insufficient |
| 30002  | Account frozen | Account is frozen |
| 30003  | Withdrawal disabled | Withdrawals are disabled |
| 30004  | Invalid address | Invalid withdrawal address |
| 30005  | Amount too small | Amount is below minimum limit |
| 30006  | Amount too large | Amount exceeds maximum limit |

## Best Practices

1. **Balance Monitoring**
   - Regularly check account balance
   - Set up alerts for low balance
   - Monitor transaction history

2. **Withdrawal Security**
   - Verify withdrawal addresses
   - Use whitelisted addresses
   - Enable two-factor authentication

3. **Error Handling**
   ```python
   try:
       response = client.getAccountBalance()
       if response['code'] == '00000':
           process_balance(response['data'])
       else:
           handle_error(response)
   except Exception as e:
       log_error(e)
   ```

4. **Rate Limiting**
   - Cache account information
   - Implement exponential backoff
   - Use WebSocket for real-time updates
