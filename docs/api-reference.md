# API Reference - All Endpoints

## Base URLs

- **Production**: `https://api.routingapi.com`
- **Sandbox**: `https://sandbox.routingapi.com`

## Payment Routing Endpoints

### Route Payment
```
POST /payments/route
```

**Description**: Route a payment to the optimal provider

**Request**:
```json
{
  "amount": 10000,
  "currency": "USD",
  "destination": "US",
  "paymentMethod": "card",
  "merchantId": "merchant_123"
}
```

**Response**:
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

### Get Payment
```
GET /payments/{paymentId}
```

**Response**:
```json
{
  "id": "pay_123",
  "amount": 10000,
  "currency": "USD",
  "status": "completed",
  "provider": "stripe",
  "createdAt": "2024-01-01T00:00:00Z"
}
```

### List Payments
```
GET /payments?status=completed&limit=10
```

## Compliance Endpoints

### Check Sanctions
```
POST /compliance/check
```

**Request**:
```json
{
  "type": "person",
  "firstName": "John",
  "lastName": "Doe",
  "country": "US"
}
```

**Response**:
```json
{
  "sanctioned": false,
  "confidence": 0.99,
  "lists": ["OFAC", "EU", "UN"],
  "lastChecked": "2024-01-01T00:00:00Z"
}
```

### Get Compliance Status
```
GET /compliance/status/{entityId}
```

## Webhook Endpoints

### Create Webhook
```
POST /webhooks
```

### List Webhooks
```
GET /webhooks
```

### Delete Webhook
```
DELETE /webhooks/{webhookId}
```

## Admin Endpoints

### Create API Key
```
POST /admin/keys
```

### Rotate API Key
```
POST /admin/keys/{keyId}/rotate
```

### Delete API Key
```
DELETE /admin/keys/{keyId}
```
