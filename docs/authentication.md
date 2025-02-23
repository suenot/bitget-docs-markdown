# Authentication

## API Key Creation

1. Log in to your Bitget account
2. Navigate to [API Management](https://www.bitget.com/account/newapi)
3. Click "Create API Key"
4. Select the permissions you need:
   - Read
   - Trade
   - Withdraw
5. Complete the security verification
6. Save your API Key, Secret Key, and Passphrase securely

## API Key Components

- **API Key**: Your public API key
- **Secret Key**: Your private key for signing requests
- **Passphrase**: Additional password for API access

## Request Signing

All private REST API endpoints require signing the request with your API credentials. The signature should be calculated as follows:

1. Create timestamp string
2. Create the signature string:
   - For GET requests: timestamp + method + requestPath + queryString
   - For POST requests: timestamp + method + requestPath + body
3. Sign the string using HMAC-SHA256 with your Secret Key
4. Add headers:
   - ACCESS-KEY: Your API Key
   - ACCESS-SIGN: The calculated signature
   - ACCESS-TIMESTAMP: Current timestamp
   - ACCESS-PASSPHRASE: Your API Passphrase

### Example (Python)

```python
import time
import hmac
import base64
import requests

def sign_request(timestamp, method, request_path, body):
    message = str(timestamp) + method.upper() + request_path + (body or '')
    mac = hmac.new(
        bytes(secret_key, encoding='utf8'),
        bytes(message, encoding='utf-8'),
        digestmod='sha256'
    )
    d = mac.digest()
    return base64.b64encode(d).decode()

# Request example
timestamp = str(int(time.time() * 1000))
method = 'GET'
request_path = '/api/spot/v1/account/getInfo'
body = ''

sign = sign_request(timestamp, method, request_path, body)

headers = {
    'ACCESS-KEY': api_key,
    'ACCESS-SIGN': sign,
    'ACCESS-TIMESTAMP': timestamp,
    'ACCESS-PASSPHRASE': passphrase,
    'Content-Type': 'application/json'
}

response = requests.get(
    'https://api.bitget.com' + request_path,
    headers=headers
)
```

## Security Recommendations

1. Never share your API credentials
2. Use IP whitelisting when possible
3. Only enable the permissions you need
4. Regularly rotate your API keys
5. Monitor your API key usage
6. Store API credentials securely
7. Use environment variables for API credentials in your code

## WebSocket Authentication

WebSocket connections requiring private data need to be authenticated. The process is similar to REST API authentication:

1. Generate signature as described above
2. Send authentication message after connecting:

```python
{
    "op": "login",
    "args": [{
        "apiKey": "your_api_key",
        "passphrase": "your_passphrase",
        "timestamp": "timestamp",
        "sign": "calculated_signature"
    }]
}
```

## Error Codes

For authentication-related error codes and troubleshooting, see [Error Codes](./error-codes.md).
