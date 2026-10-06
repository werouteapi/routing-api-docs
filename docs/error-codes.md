# Error Codes - Error Reference

## HTTP Status Codes

| Code | Name | Meaning |
|------|------|---------|
| 200 | OK | Success |
| 201 | Created | Resource created |
| 400 | Bad Request | Invalid input |
| 401 | Unauthorized | Invalid/missing API key |
| 403 | Forbidden | Access denied |
| 404 | Not Found | Resource not found |
| 409 | Conflict | Resource already exists |
| 429 | Too Many Requests | Rate limited |
| 500 | Server Error | Internal error |
| 503 | Service Unavailable | Maintenance/downtime |

## Error Response Format

```json
{
  "error": {
    "code": "invalid_request",
    "message": "Missing required field: currency",
    "param": "currency",
    "type": "invalid_request_error"
  }
}
```

## Common Error Codes

### Authentication Errors

| Code | Description | Solution |
|------|-------------|----------|
| `invalid_api_key` | API key is invalid | Check your API key |
| `expired_api_key` | API key has expired | Generate new key |
| `missing_auth_header` | No Authorization header | Add Authorization header |

### Request Errors

| Code | Description | Solution |
|------|-------------|----------|
| `invalid_request` | Malformed request | Check JSON syntax |
| `missing_required_param` | Required field missing | Add missing field |
| `invalid_param_type` | Parameter has wrong type | Use correct data type |
| `invalid_amount` | Amount is invalid | Use positive integer |
| `invalid_currency` | Currency not supported | Use supported currency |

### Payment Errors

| Code | Description | Solution |
|------|-------------|----------|
| `payment_failed` | Payment processing failed | Check provider response |
| `insufficient_funds` | Customer has no funds | Ask customer to check balance |
| `card_declined` | Card was declined | Try different payment method |
| `duplicate_transaction` | Transaction already processed | Check transaction history |

### Compliance Errors

| Code | Description | Solution |
|------|-------------|----------|
| `sanctioned_entity` | Entity is sanctioned | Cannot process this payment |
| `kyc_required` | KYC verification needed | Complete KYC process |
| `aml_blocked` | AML check blocked | Contact support |

### Rate Limit Errors

| Code | Description | Solution |
|------|-------------|----------|
| `rate_limit_exceeded` | Too many requests | Wait and retry |
| `burst_limit_exceeded` | Too many requests at once | Spread requests over time |

## Handling Errors

### JavaScript

```javascript
try {
  const result = await client.routePayment({
    amount: 10000,
    currency: 'USD',
    destination: 'US'
  });
} catch (error) {
  if (error.code === 'invalid_currency') {
    console.log('Please use a valid currency');
  } else if (error.code === 'rate_limit_exceeded') {
    console.log('Too many requests, retry later');
  } else {
    console.log('Error:', error.message);
  }
}
```

### Python

```python
try:
    result = client.route_payment(
        amount=10000,
        currency='USD',
        destination='US'
    )
except RoutingAPIError as e:
    if e.code == 'invalid_currency':
        print('Please use a valid currency')
    elif e.code == 'rate_limit_exceeded':
        print('Too many requests, retry later')
    else:
        print(f'Error: {e.message}')
```

## Retry Logic

```javascript
async function retryRequest(fn, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      // Retry on rate limit or server error
      if (error.code === 'rate_limit_exceeded' || error.status >= 500) {
        if (i < maxRetries - 1) {
          await new Promise(r => setTimeout(r, 1000 * (i + 1)));
          continue;
        }
      }
      throw error;
    }
  }
}
```

## Getting Help

If you encounter an error:

1. Check the error code in this reference
2. Review the error message
3. Check the suggested solution
4. If still stuck, email: support@webundle.org
