# Retail Core Settlement Attributes (v2.1)

## Overview

The **RetailSettlement** schema defines the structure for retail settlement discharge records in the Beckn protocol v2.1. This schema is intentionally minimal because retail settlements are typically handled by payment gateways, allowing for a clean and efficient representation of settlement transactions without duplicating payment processor logic.

## Container

- **Container Name:** `settlementAttributes`
- **Status:** NEW in v2.1 (did not exist in v2.0)

## Namespace

- **Prefix:** `rcsa`
- **URI:** `https://schema.beckn.io/RetailSettlement#`

## Key Features

### 1. **Settlement Discharge Details**
- `method`: How the settlement was processed (ONLINE, BANK_TRANSFER, COD_COLLECTION, ESCROW_RELEASE)
- `settledAt`: ISO 8601 timestamp of when settlement occurred
- `settledAmount`: Amount that was discharged
- `currency`: ISO 4217 currency code

All four fields are required.

### 2. **Payment Gateway Information**
Optional object containing integration details with payment processors:
- **name**: Payment gateway provider (e.g., Razorpay, PayPal, Stripe)
- **transactionId**: Unique transaction identifier from the gateway
- **url**: Link to transaction details or receipt in the gateway

### 3. **Reconciliation Tracking**
- `reconciliationId`: Unique identifier for multi-party settlement reconciliation and batch processing

## Settlement Methods

The schema supports four settlement discharge methods:

| Method | Description |
|--------|-------------|
| **ONLINE** | Real-time online settlement through payment gateway (immediate credit) |
| **BANK_TRANSFER** | Settlement via bank transfer to merchant account (T+1 or longer) |
| **COD_COLLECTION** | Collection of cash on delivery with settlement to seller |
| **ESCROW_RELEASE** | Release of funds held in escrow account for dispute resolution |

## Schema Definition

### Root Object: `RetailSettlement`

```yaml
type: object
required:
  - method
  - settledAt
  - settledAmount
  - currency
properties:
  gateway:
    type: object
    properties:
      name: string
      transactionId: string
      url: string (format: uri)
  method:
    type: string
    enum: [ONLINE, BANK_TRANSFER, COD_COLLECTION, ESCROW_RELEASE]
  settledAt:
    type: string
    format: date-time
  settledAmount:
    type: number
  currency:
    type: string
    pattern: '^[A-Z]{3}$'
  reconciliationId:
    type: string
```

## Usage Examples

### Example 1: Online Settlement via Payment Gateway

```json
{
  "settlementAttributes": {
    "method": "ONLINE",
    "settledAt": "2026-03-25T14:30:00Z",
    "settledAmount": 1150.00,
    "currency": "INR",
    "gateway": {
      "name": "Razorpay",
      "transactionId": "txn_1A2B3C4D5E6F",
      "url": "https://dashboard.razorpay.com/payments/txn_1A2B3C4D5E6F"
    },
    "reconciliationId": "recon_2026_03_25_batch_001"
  }
}
```

### Example 2: Bank Transfer Settlement

```json
{
  "settlementAttributes": {
    "method": "BANK_TRANSFER",
    "settledAt": "2026-03-25T09:00:00Z",
    "settledAmount": 5000.00,
    "currency": "INR",
    "reconciliationId": "recon_2026_03_25_batch_002"
  }
}
```

### Example 3: COD Collection Settlement

```json
{
  "settlementAttributes": {
    "method": "COD_COLLECTION",
    "settledAt": "2026-03-25T16:45:00Z",
    "settledAmount": 2500.00,
    "currency": "INR",
    "reconciliationId": "recon_2026_03_25_batch_003"
  }
}
```

### Example 4: Escrow Release Settlement

```json
{
  "settlementAttributes": {
    "method": "ESCROW_RELEASE",
    "settledAt": "2026-03-25T11:20:00Z",
    "settledAmount": 750.00,
    "currency": "INR",
    "gateway": {
      "name": "Escrow Partner",
      "transactionId": "escrow_ref_123456",
      "url": "https://escrow.example.com/release/escrow_ref_123456"
    },
    "reconciliationId": "recon_2026_03_25_batch_004"
  }
}
```

## Files

- **attributes.yaml**: OpenAPI 3.1.1 schema definition with JSON-LD annotations
- **context.jsonld**: JSON-LD context for semantic interoperability
- **vocab.jsonld**: RDF vocabulary definitions with labels and comments
- **profile.json**: Configuration profile for the schema
- **renderer.json**: HTML rendering templates and styling
- **README.md**: This documentation file

## Semantic Mapping

All properties are mapped to the RDF namespace with appropriate semantic annotations:

| Property | RDF URI | Type |
|----------|---------|------|
| gateway | rcsa:gateway | rcsa:Gateway |
| method | rcsa:method | xsd:string |
| settledAt | rcsa:settledAt | xsd:dateTime |
| settledAmount | rcsa:settledAmount | xsd:decimal |
| currency | rcsa:currency | xsd:string |
| reconciliationId | rcsa:reconciliationId | xsd:string |

### Gateway Object

| Property | RDF URI | Type |
|----------|---------|------|
| name | rcsa:name | xsd:string |
| transactionId | rcsa:transactionId | xsd:string |
| url | rcsa:url | xsd:anyURI |

## Rendering

The schema provides two HTML templates:

1. **html**: Compact view suitable for settlement confirmations and transaction receipts
2. **html_detail**: Detailed view with gateway information, reconciliation details, and full transaction traceability

Both templates use Handlebars syntax and include CSS styling optimized for financial/settlement records with green color scheme indicating successful settlement.

## Design Rationale

This schema is intentionally minimal because:

1. **Payment Gateway Abstraction**: Most retail payment processing is handled by dedicated payment gateways (Razorpay, PayPal, Stripe, etc.), which maintain their own comprehensive settlement records.

2. **Separation of Concerns**: Settlement discharge is a distinct operation from consideration (pricing) and payment processing, allowing for clean architectural boundaries.

3. **Retail Focus**: Retail transactions typically have straightforward 1-to-1 settlement (single payment, single merchant), unlike complex B2B scenarios with multiple parties and split payments.

4. **Gateway Integration**: The `gateway` object allows referencing the authoritative payment processor record without duplicating their settlement logic.

## Compatibility

- **Beckn Protocol Version:** 2.0
- **Semantic Model:** Generalised
- **OpenAPI Version:** 3.1.1

## References

- Core Beckn Context: https://schema.beckn.io/core/v2/context.jsonld#generalised
- Core Beckn Vocabulary: https://schema.beckn.io/core/v2/vocab.jsonld
- Beckn Standard: https://beckn.org

## License

CC-BY-3.0

## Changelog

- **v2.1.0** (2026-03-25): Initial release of RetailSettlement schema
