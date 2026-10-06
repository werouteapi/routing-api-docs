# Quick Start Guide

## Get API Key

1. Visit https://github.com/werouteapi/routing-api-docs
2. Sign up for a free account
3. Go to **Settings → API Keys**
4. Click **Create New Key**
5. Copy your test key (starts with `sk_test_`)
6. Keep it safe!

## Choose Your SDK

Pick your programming language:

- **Node.js**: `npm install routing-api-sdk-nodejs`
- **Python**: `pip install routing-api-sdk-python`
- **Go**: `go get github.com/werouteapi/routing-api-sdk-go`
- **Java**: Add to Maven/Gradle
- **Ruby**: `gem install routing-api-sdk-ruby`
- **PHP**: `composer require werouteapi/routing-api-sdk-php`
- **Rust**: Add to Cargo.toml
- **Swift**: SPM
- **Dart**: Add to pubspec.yaml
- **C#**: NuGet package
- **Perl**: CPAN
- **Elixir**: Hex package

[See all SDKs](https://github.com/werouteapi)

## Build Your First Request

### Node.js

```javascript
const RoutingAPI = require('routing-api-sdk-nodejs');

// Initialize
const client = new RoutingAPI.Client({
  apiKey: process.env.ROUTING_API_KEY
});

// Route a payment
async function main() {
  try {
    const result = await client.routePayment({
      amount: 10000,      // $100.00
      currency: 'USD',
      destination: 'US',
      paymentMethod: 'card'
    });
    
    console.log('Best provider:', result.recommendedProvider);
    console.log('Fee:', result.estimatedFee / 100, 'USD');
  } catch (error) {
    console.error('Error:', error.message);
  }
}

main();
```

### Python

```python
from routing_api_sdk import RoutingAPIClient
import os

# Initialize
client = RoutingAPIClient(api_key=os.getenv('ROUTING_API_KEY'))

# Route a payment
result = client.route_payment(
    amount=10000,           # $100.00
    currency='USD',
    destination='US',
    payment_method='card'
)

print(f'Best provider: {result.recommended_provider}')
print(f'Fee: ${result.estimated_fee / 100}')
```

### cURL

```bash
export ROUTING_API_KEY="sk_test_your_key_here"

curl -X POST https://sandbox.routingapi.com/payments/route \
  -H "Authorization: Bearer $ROUTING_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 10000,
    "currency": "USD",
    "destination": "US",
    "paymentMethod": "card"
  }'
```

## Expected Response

```json
{
  "id": "route_abc123",
  "recommendedProvider": "stripe",
  "alternatives": ["paypal", "square"],
  "estimatedFee": 250,
  "estimatedTime": "instant",
  "status": "ready"
}
```

## Next Steps

1. **Learn Payment Routing** - Read [Payment Routing Guide](payment-routing.md)
2. **Add Compliance Checks** - See [Compliance Guide](compliance.md)
3. **Set Up Webhooks** - Read [Webhooks Guide](webhooks.md)
4. **Go to Production** - Use `sk_live_*` keys
5. **Get Support** - Email support@webundle.org

## Common Tasks

- [Route a payment](payment-routing.md)
- [Check compliance](compliance.md)
- [Handle errors](error-codes.md)
- [Setup webhooks](webhooks.md)
- [Understand rate limits](rate-limiting.md)

## Test with Sandbox

All test API keys start with `sk_test_`:

```bash
curl -X POST https://sandbox.routingapi.com/payments/route \
  -H "Authorization: Bearer sk_test_abc123" \
  ...
```

## Documentation

- [Complete API Reference](api-reference.md)
- [Authentication Guide](authentication.md)
- [Error Codes Reference](error-codes.md)

## Support

- Email: support@webundle.org
- Docs: https://github.com/werouteapi/routing-api-docs
- Status: https://github.com/werouteapi
