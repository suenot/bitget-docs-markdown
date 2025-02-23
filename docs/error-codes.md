# Error Codes

## Response Format

When an error occurs, the API will return a response in the following format:

```json
{
    "code": "40001",
    "msg": "Invalid parameter",
    "requestTime": 1621441751927
}
```

## HTTP Status Codes

| Status Code | Description |
|------------|-------------|
| 200 | Success |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 429 | Too Many Requests |
| 500 | Internal Server Error |

## API Error Codes

### General Errors (00000-09999)

| Code | Message | Description | Solution |
|------|---------|-------------|----------|
| 00000 | success | Request successful | N/A |
| 00001 | System error | Internal system error | Try again later or contact support |
| 00002 | System maintenance | System under maintenance | Check system status page |
| 00003 | Rate limit exceeded | Too many requests | Reduce request frequency |

### Authentication Errors (10000-19999)

| Code | Message | Description | Solution |
|------|---------|-------------|----------|
| 10001 | Invalid API key | API key is invalid or expired | Check your API key |
| 10002 | Invalid signature | Request signature verification failed | Check signature calculation |
| 10003 | Invalid timestamp | Request timestamp is invalid | Sync your system time |
| 10004 | Invalid passphrase | API key passphrase is incorrect | Check your passphrase |
| 10005 | IP not allowed | IP address not in whitelist | Add IP to whitelist |

### Request Validation Errors (20000-29999)

| Code | Message | Description | Solution |
|------|---------|-------------|----------|
| 20001 | Missing parameter | Required parameter is missing | Check request parameters |
| 20002 | Invalid parameter | Parameter value is invalid | Check parameter values |
| 20003 | Invalid request body | Request body format is invalid | Check request body format |
| 20004 | Invalid content type | Invalid content-type header | Set correct content-type |

### Account Errors (30000-39999)

| Code | Message | Description | Solution |
|------|---------|-------------|----------|
| 30001 | Insufficient balance | Account balance insufficient | Add funds to account |
| 30002 | Account frozen | Account is frozen | Contact support |
| 30003 | Account not found | Account does not exist | Check account ID |
| 30004 | Invalid account type | Account type is invalid | Check account type |

### Trading Errors (40000-49999)

| Code | Message | Description | Solution |
|------|---------|-------------|----------|
| 40001 | Invalid symbol | Trading pair does not exist | Check trading pair |
| 40002 | Invalid order type | Order type is invalid | Check order type |
| 40003 | Invalid side | Order side is invalid | Check order side |
| 40004 | Invalid quantity | Order quantity is invalid | Check quantity rules |
| 40005 | Invalid price | Order price is invalid | Check price rules |
| 40006 | Insufficient funds | Insufficient funds for order | Check account balance |
| 40007 | Order not found | Order does not exist | Check order ID |
| 40008 | Market closed | Market is closed | Check market status |
| 40009 | Order canceled | Order has been canceled | N/A |

### WebSocket Errors (50000-59999)

| Code | Message | Description | Solution |
|------|---------|-------------|----------|
| 50001 | Invalid subscription | Invalid subscription request | Check subscription format |
| 50002 | Connection limit | Too many connections | Reduce connections |
| 50003 | Authentication failed | WebSocket authentication failed | Check credentials |
| 50004 | Invalid channel | Channel does not exist | Check channel name |

### Copy Trading Errors (60000-69999)

| Code | Message | Description | Solution |
|------|---------|-------------|----------|
| 60001 | Invalid trader | Trader does not exist | Check trader ID |
| 60002 | Copy trading disabled | Copy trading is disabled | Enable copy trading |
| 60003 | Invalid copy ratio | Copy ratio is invalid | Check copy ratio |
| 60004 | Already following | Already following this trader | N/A |

## Error Handling Best Practices

1. **Always Check Response Code**
   ```python
   if response["code"] != "00000":
       handle_error(response)
   ```

2. **Implement Retry Logic**
   - Use exponential backoff for retries
   - Only retry on specific error codes
   - Set maximum retry attempts

   ```python
   def retry_request(func, max_retries=3):
       retries = 0
       while retries < max_retries:
           try:
               response = func()
               if response["code"] == "00000":
                   return response
               elif response["code"] in ["00001", "00002"]:  # Retryable errors
                   time.sleep(2 ** retries)
                   retries += 1
               else:
                   raise Exception(f"Error: {response['msg']}")
           except Exception as e:
               if retries == max_retries - 1:
                   raise
               retries += 1
               time.sleep(2 ** retries)
   ```

3. **Handle Rate Limits**
   - Track request timestamps
   - Implement request queuing
   - Use WebSocket for real-time data

4. **Log Errors**
   ```python
   def handle_error(response):
       logger.error(f"API Error: {response['code']} - {response['msg']}")
       # Additional error handling logic
   ```

5. **Validate Input**
   - Validate parameters before sending requests
   - Check parameter types and ranges
   - Format parameters correctly

## Common Troubleshooting

1. **Authentication Issues**
   - Verify API key and secret
   - Check system time synchronization
   - Verify IP whitelist settings

2. **Rate Limit Issues**
   - Implement rate limiting in your application
   - Use WebSocket for real-time data
   - Optimize request frequency

3. **Order Issues**
   - Check account balance
   - Verify order parameters
   - Check market status

4. **WebSocket Issues**
   - Implement proper reconnection logic
   - Handle connection timeouts
   - Process messages sequentially
