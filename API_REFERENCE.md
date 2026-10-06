# Routing API Reference

## Base URL

- Production: `https://api.routingapi.com`
- Sandbox: `https://sandbox.routingapi.com`

## Authentication

All requests require an API key in the Authorization header:

```
Authorization: Bearer YOUR_API_KEY
```

## Endpoints

### Payment Routing

#### Route a Payment
```
POST /payments/route
```

Request:
```json
{
  "amount": 10000,
  "currency": "USD",
  "destination": "US",
  "paymentMethod": "card",
  "merchantId": "merchant_123"
}
```

Response:
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

### Compliance

#### Check Sanctions
```
POST /compliance/check
```

Request:
```json
{
  "type": "person",
  "firstName": "John",
  "lastName": "Doe",
  "country": "US"
}
```

Response:
```json
{
  "sanctioned": false,
  "confidence": 0.99,
  "lists": ["OFAC", "EU", "UN"],
  "lastChecked": "2024-01-01T00:00:00Z"
}
```

### Webhooks

#### Create Webhook
```
POST /webhooks
```

Request:
```json
{
  "url": "https://example.com/webhooks",
  "events": ["payment.completed", "payment.failed"],
  "active": true
}
```

Response:
```json
{
  "id": "wh_123",
  "url": "https://example.com/webhooks",
  "events": ["payment.completed", "payment.failed"],
  "secret": "whsec_abc123",
  "active": true
}
```

## Error Codes

- `400` - Bad Request
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `429` - Rate Limited
- `500` - Server Error

## Rate Limiting

- 100 requests per minute for standard tier
- Headers:
  - `X-RateLimit-Limit`
  - `X-RateLimit-Remaining`
  - `X-RateLimit-Reset`
