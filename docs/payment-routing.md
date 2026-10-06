# Payment Routing - Core Routing Guide

## How Payment Routing Works

The Routing API analyzes your payment details and recommends the best provider based on:
- Success rates
- Transaction fees
- Processing times
- Geographic coverage
- Compliance requirements

## Basic Flow

```
Payment Request
     ↓
[Routing Engine]
     ↓
Provider Analysis
     ↓
Recommendation
     ↓
Process Payment
```

## Routing a Payment

### JavaScript Example

```javascript
const result = await client.routePayment({
  amount: 10000,        // $100.00 in cents
  currency: 'USD',
  destination: 'US',
  paymentMethod: 'card'
});

console.log(result.recommendedProvider); // 'stripe'
console.log(result.estimatedFee);       // 250 cents
```

### Python Example

```python
result = client.route_payment(
    amount=10000,
    currency='USD',
    destination='US',
    payment_method='card'
)

print(result.recommended_provider)  # 'stripe'
print(result.estimated_fee)         # 250
```

## Understanding the Response

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

- **recommendedProvider**: Primary provider to use
- **alternatives**: Fallback providers if primary fails
- **estimatedFee**: Transaction fee in cents
- **estimatedTime**: How long the transaction takes
- **status**: Whether routing is ready to proceed

## Payment Methods

- `card` - Credit/debit cards
- `bank_transfer` - ACH, SEPA, etc.
- `digital_wallet` - Apple Pay, Google Pay
- `crypto` - Cryptocurrency payments

## Currencies Supported

Over 150 currencies including:
- USD, EUR, GBP, JPY, CHF
- CNY, INR, MXN, BRL, ZAR
- And more...

## Best Practices

1. Always use recommended provider first
2. Have fallback logic for alternatives
3. Log routing decisions
4. Monitor provider performance
5. Test with sandbox before production

## Fallback Handling

```javascript
async function processPayment(paymentDetails) {
  const routing = await client.routePayment(paymentDetails);
  
  let providers = [routing.recommendedProvider, ...routing.alternatives];
  
  for (const provider of providers) {
    try {
      return await processWithProvider(provider, paymentDetails);
    } catch (err) {
      console.log(`${provider} failed, trying next...`);
    }
  }
  
  throw new Error('All providers failed');
}
```
