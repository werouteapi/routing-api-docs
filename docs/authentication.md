# Authentication - API Key Setup

## Getting Your API Key

1. Sign up at https://dashboard.webundle.org
2. Go to **Settings → API Keys**
3. Click **Create New Key**
4. Copy your key (starts with `sk_` for secret keys or `pk_` for public keys)
5. Store securely (never commit to git!)

## Using Your API Key

### Bearer Token (Recommended)

```bash
curl -H "Authorization: Bearer sk_live_abc123" \
  https://api.webundle.org/payments/route
```

### Environment Variable

```bash
export ROUTING_API_KEY="sk_live_abc123"
```

### In Code

**JavaScript**:
```javascript
const RoutingAPI = require('routing-api-sdk-nodejs');
const client = new RoutingAPI.Client({
  apiKey: process.env.ROUTING_API_KEY
});
```

**Python**:
```python
from routing_api_sdk import RoutingAPIClient

client = RoutingAPIClient(api_key=os.getenv('ROUTING_API_KEY'))
```

**cURL**:
```bash
curl -X POST https://api.webundle.org/payments/route \
  -H "Authorization: Bearer $ROUTING_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"amount": 10000, "currency": "USD", "destination": "US"}'
```

## Key Types

- **Secret Keys** (`sk_*`) - Never share, use server-side only
- **Public Keys** (`pk_*`) - Safe to use in frontend
- **Test Keys** (`sk_test_*`) - Use for sandbox testing

## Security Best Practices

1. Never hardcode API keys
2. Use environment variables
3. Rotate keys regularly
4. Use different keys for test and production
5. Revoke compromised keys immediately

## Testing Your Setup

```bash
curl -X GET https://api.webundle.org/status \
  -H "Authorization: Bearer sk_test_abc123"
```

Should return:
```json
{
  "status": "ok",
  "authenticated": true
}
```
