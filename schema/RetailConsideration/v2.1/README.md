# Retail Core Consideration Attributes (v2.1)

## Overview

The **RetailConsideration** schema defines the structure for retail monetary consideration attributes in the Beckn protocol v2.1. This schema is intentionally minimal because retail transactions are overwhelmingly monetary in nature, allowing for a focused and efficient representation of price, payment methods, and charge breakdown information.

## Container

- **Container Name:** `considerationAttributes`
- **Status:** NEW in v2.1 (did not exist in v2.0)

## Namespace

- **Prefix:** `rccna`
- **URI:** `https://schema.beckn.io/RetailConsideration#`

## Key Features

### 1. **Monetary Amount**
- `currency`: ISO 4217 currency code (e.g., INR, USD, EUR)
- `totalAmount`: Total consideration amount in the specified currency
- Both fields are required

### 2. **Payment Methods**
Array of supported payment methods:
- **PREPAID**: Payment made in advance
- **COD**: Cash on Delivery
- **CREDIT**: Credit card payment
- **DEBIT**: Debit card payment
- **UPI**: Unified Payments Interface (India)
- **WALLET**: Digital wallet
- **NET_BANKING**: Online banking
- **BNPL**: Buy Now Pay Later

### 3. **Charge Breakup**
Detailed breakdown of all charges and adjustments:
- **title**: Description of the charge
- **amount**: Monetary amount
- **type**: Category of charge
  - `BASE_PRICE`: Original item price
  - `TAX`: Tax charges
  - `DISCOUNT`: Price reduction
  - `DELIVERY_CHARGE`: Shipping/delivery cost
  - `PACKING_CHARGE`: Packaging cost
  - `CONVENIENCE_FEE`: Service fee
  - `OTHER`: Miscellaneous charges

### 4. **Loyalty Points (Non-Monetary)**
Optional loyalty program consideration:
- **programId**: Unique identifier of the loyalty program
- **pointsUsed**: Number of loyalty points redeemed
- **monetaryEquivalent**: Cash value equivalent of the points

## Schema Definition

### Root Object: `RetailConsideration`

```yaml
type: object
required:
  - currency
  - totalAmount
properties:
  paymentMethods:
    type: array
    items: enum [PREPAID, COD, CREDIT, DEBIT, UPI, WALLET, NET_BANKING, BNPL]
  currency:
    type: string
    pattern: '^[A-Z]{3}$'
  breakup:
    type: array
    items:
      type: object
      properties:
        title: string
        amount: number
        type: enum [BASE_PRICE, TAX, DISCOUNT, DELIVERY_CHARGE, PACKING_CHARGE, CONVENIENCE_FEE, OTHER]
  totalAmount:
    type: number
  loyaltyPointsApplied:
    type: object
    properties:
      programId: string
      pointsUsed: integer
      monetaryEquivalent: number
```

## Usage Example

```json
{
  "considerationAttributes": {
    "currency": "INR",
    "totalAmount": 1150.00,
    "paymentMethods": ["PREPAID", "CREDIT", "DEBIT", "UPI"],
    "breakup": [
      {
        "title": "Base Price",
        "amount": 1000.00,
        "type": "BASE_PRICE"
      },
      {
        "title": "GST",
        "amount": 100.00,
        "type": "TAX"
      },
      {
        "title": "Delivery",
        "amount": 50.00,
        "type": "DELIVERY_CHARGE"
      }
    ],
    "loyaltyPointsApplied": {
      "programId": "loyalty-gold-2024",
      "pointsUsed": 200,
      "monetaryEquivalent": 20.00
    }
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
| paymentMethods | rccna:paymentMethods | rdf:list |
| currency | rccna:currency | xsd:string |
| breakup | rccna:breakup | rdf:list |
| totalAmount | rccna:totalAmount | xsd:decimal |
| loyaltyPointsApplied | rccna:loyaltyPointsApplied | rccna:LoyaltyPointsApplied |

## Rendering

The schema provides two HTML templates:

1. **html**: Compact view suitable for transaction summaries
2. **html_detail**: Detailed view with complete charge breakup and loyalty information

Both templates use Handlebars syntax and include CSS styling for professional presentation.

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

- **v2.1.0** (2026-03-25): Initial release of RetailConsideration schema
