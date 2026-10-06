# Webhooks - Event Handling

## Overview

Webhooks allow you to receive real-time notifications about payment events.

## Webhook Events

| Event | Trigger | Use Case |
|-------|---------|----------|
| `payment.completed` | Payment successful | Update order status |
| `payment.failed` | Payment failed | Retry or notify customer |
| `payment.refunded` | Refund processed | Update inventory |
| `compliance.blocked` | Compliance check failed | Alert user |
| `payment.disputed` | Chargeback initiated | Investigate |

## Creating a Webhook

```
POST /webhooks
```

**Request**:
```json
{
  "url": "https://example.com/webhooks/routing",
  "events": ["payment.completed", "payment.failed"],
  "active": true
}
```

**Response**:
```json
{
  "id": "wh_123",
  "url": "https://example.com/webhooks/routing",
  "events": ["payment.completed", "payment.failed"],
  "secret": "whsec_abc123xyz789",
  "active": true,
  "createdAt": "2024-01-01T00:00:00Z"
}
```

## Webhook Payload

```json
{
  "id": "evt_123",
  "type": "payment.completed",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "paymentId": "pay_abc123",
    "amount": 10000,
    "currency": "USD",
    "status": "completed",
    "provider": "stripe"
  }
}
```

## Verifying Webhooks

### Signature Verification

Each webhook includes an `X-Routing-Signature` header for verification:

**JavaScript**:
```javascript
const crypto = require('crypto');

function verifyWebhookSignature(body, signature, secret) {
  const hash = crypto
    .createHmac('sha256', secret)
    .update(body)
    .digest('hex');
  
  return hash === signature;
}

app.post('/webhooks/routing', (req, res) => {
  const signature = req.headers['x-routing-signature'];
  const body = req.rawBody; // Raw body as string
  
  if (!verifyWebhookSignature(body, signature, process.env.WEBHOOK_SECRET)) {
    return res.status(401).send('Unauthorized');
  }
  
  const event = req.body;
  handleWebhookEvent(event);
  res.send({ received: true });
});
```

**Python**:
```python
import hmac
import hashlib

def verify_webhook_signature(body, signature, secret):
    hash = hmac.new(
        secret.encode(),
        body,
        hashlib.sha256
    ).hexdigest()
    return hash == signature

@app.route('/webhooks/routing', methods=['POST'])
def webhook():
    signature = request.headers.get('X-Routing-Signature')
    body = request.get_data()
    
    if not verify_webhook_signature(body, signature, os.getenv('WEBHOOK_SECRET')):
        return 'Unauthorized', 401
    
    event = request.json
    handle_webhook_event(event)
    return { 'received': True }
```

## Handling Events

```javascript
function handleWebhookEvent(event) {
  switch (event.type) {
    case 'payment.completed':
      updateOrderStatus(event.data.paymentId, 'paid');
      break;
    
    case 'payment.failed':
      notifyCustomer(event.data.paymentId, 'Payment failed');
      break;
    
    case 'payment.refunded':
      processRefund(event.data.paymentId);
      break;
    
    case 'compliance.blocked':
      blockTransaction(event.data.paymentId);
      break;
  }
}
```

## Best Practices

1. **Verify signatures** - Always verify webhook authenticity
2. **Acknowledge quickly** - Return 200 immediately
3. **Process async** - Handle webhooks in background
4. **Store payloads** - Log all webhooks for debugging
5. **Implement retries** - We retry failed deliveries
6. **Use idempotency** - Handle duplicate deliveries

## Webhook Delivery

- Immediate delivery upon event
- Automatic retries if delivery fails
- Max retry attempts: 5
- Retry backoff: exponential (1s, 10s, 100s, 1000s, 10000s)

## Managing Webhooks

### List Webhooks
```
GET /webhooks
```

### Get Webhook
```
GET /webhooks/{webhookId}
```

### Update Webhook
```
PUT /webhooks/{webhookId}
```

### Delete Webhook
```
DELETE /webhooks/{webhookId}
```

## Testing Webhooks

Send a test event:
```
POST /webhooks/{webhookId}/test
```

This sends a sample `payment.completed` event to verify your setup.

## Troubleshooting

### Not receiving webhooks?

1. Check webhook is active: `GET /webhooks/{id}`
2. Check URL is accessible from internet
3. Verify signature implementation
4. Check server logs
5. Use test endpoint to verify

### Need to debug?

View delivery history:
```
GET /webhooks/{webhookId}/deliveries
```

Returns list of all delivery attempts with timestamps and responses.
