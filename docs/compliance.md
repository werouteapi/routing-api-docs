# Compliance - KYC/AML Checks

## Overview

The Compliance API checks transactions against:
- OFAC (US Office of Foreign Assets Control)
- EU sanctions lists
- UN sanctions
- Local regulatory databases
- AML watchlists

## Sanctions Check

### Check If Entity Is Sanctioned

```
POST /compliance/check
```

**Request**:
```json
{
  "type": "person",
  "firstName": "John",
  "lastName": "Doe",
  "country": "US",
  "dateOfBirth": "1990-01-01"
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

## KYC Verification

### Verify Customer Identity

```
POST /compliance/kyc
```

**Request**:
```json
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@example.com",
  "country": "US",
  "documentType": "passport",
  "documentNumber": "12345678"
}
```

**Response**:
```json
{
  "verified": true,
  "riskLevel": "low",
  "verificationId": "kyc_123",
  "expiresAt": "2025-01-01T00:00:00Z"
}
```

## AML Risk Assessment

### Get AML Risk Level

```
POST /compliance/aml-risk
```

**Request**:
```json
{
  "customerId": "cust_123",
  "transactionAmount": 50000,
  "transactionCount": 100,
  "avgTransactionSize": 500
}
```

**Response**:
```json
{
  "riskLevel": "medium",
  "score": 65,
  "factors": [
    "high_transaction_velocity",
    "round_amount"
  ],
  "recommendation": "review"
}
```

## Compliance Statuses

- **APPROVED** - Clear to process
- **REVIEW** - Manual review required
- **BLOCKED** - Must decline transaction

## Implementation

### Check Before Processing

```javascript
async function processPayment(payment) {
  // Step 1: Check compliance
  const compliance = await client.checkCompliance({
    type: 'person',
    firstName: payment.firstName,
    lastName: payment.lastName,
    country: payment.country
  });
  
  if (compliance.sanctioned) {
    throw new Error('Customer is sanctioned');
  }
  
  // Step 2: Verify KYC if needed
  if (payment.amount > 10000) {
    const kyc = await client.verifyKYC(payment);
    if (!kyc.verified) {
      throw new Error('KYC verification failed');
    }
  }
  
  // Step 3: Process payment
  return await client.routePayment(payment);
}
```

## Compliance Holds

Large transactions may be held for review:

- Transactions > $10,000 USD
- High-risk jurisdictions
- Multiple simultaneous transactions
- Failed compliance checks

## Record Keeping

All compliance checks are logged automatically:

```javascript
const history = await client.getComplianceHistory({
  customerId: 'cust_123',
  limit: 50
});
```

## Best Practices

1. Always check compliance before processing
2. Verify KYC for large transactions
3. Monitor AML risk scores
4. Keep audit logs for regulatory compliance
5. Update customer risk profiles regularly
