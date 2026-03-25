# RetailCommitment Schema Pack v2.1

## Overview

RetailCommitment is a Beckn v2.1 schema pack that declares **per-commitment line-item details** for retail contracts. This is a **NEW schema introduced in v2.1** that did not exist in v2.0.

In v2, per-commitment fields (resourceId, lineId, offerId, quantity, price, specialInstructions) were awkwardly scattered across domain-specific items like FoodAndBeverageItem. In v2.1, they are consolidated into a dedicated, domain-agnostic CommitmentAttributes schema that can attach to any Commitment entity.

The schema enables providers and buyers to specify:
- **Line-item references** within contracts
- **Resource and offer associations** for traceability
- **Quantity and unit specifications** (EA, KG, L, etc.)
- **Per-line pricing** details
- **Special handling instructions** (item-level buyer notes)

## Attachment Points

This schema attaches to the following Beckn v2.0 core entities via the `commitmentAttributes` container:

- **Commitment**: Per-commitment execution details
- **LineItem**: Cart line item attributes
- **CartItem**: Shopping cart item specifications
- **OrderItem** (legacy): Item-level order details

## Schema Structure

### Core Container
```
commitmentAttributes: CommitmentAttributes
```

### Primary Properties

| Property | Type | Required | Notes |
|----------|------|----------|-------|
| `lineId` | String | ✓ | Unique line reference within contract |
| `resourceId` | String | ✓ | Reference to Resource (product/service) |
| `offerId` | String | | Reference to Offer under which commitment was made |
| `quantity` | Object | ✓ | Quantity with unit code and numeric amount |
| `price` | PriceSpecification | | Line-item pricing details |
| `specialInstructions` | String | | Per-item buyer notes (e.g., "no onions") |

### quantity Structure

```yaml
unitCode: string              # Unit of measurement (EA, KG, L, M, BOX, etc.)
unitQuantity: number          # Numeric quantity (must be >= 0)
```

### price Structure

References Beckn core `PriceSpecification`:
```yaml
currency: string              # ISO 4217 currency code (e.g., INR, USD)
amount: string                # Numeric price amount
estimatedValue: string        # Estimated value range (optional)
maximumValue: string          # Maximum value cap (optional)
```

### Standard Unit Codes (Common)

| Code | Description | Use Case |
|------|-------------|----------|
| EA | Each (unit count) | Items, products, meals |
| KG | Kilogram (weight) | Groceries, bulk items |
| L | Liter (volume) | Beverages, liquids |
| M | Meter (length) | Textiles, construction |
| M2 | Square meter | Flooring, land services |
| M3 | Cubic meter | Bulk materials |
| BOX | Box (container) | Packaging unit |
| BAG | Bag (container) | Packaging unit |
| DOZEN | Dozen (12 units) | Eggs, bakery |
| PAIR | Pair (2 units) | Shoes, socks |
| SET | Set (collection) | Bundles, kits |

## Design Rationale

### Consolidation from v2 Domain-Specific Models

**Problem in v2**: Commitment line-item attributes were embedded in domain-specific resource types:
- FoodAndBeverageItem: `{resourceId, lineId, offerId, quantity, price}`
- HomeAndKitchenItem: similar scattered fields
- Other resource types: inconsistent structures

This made it difficult to:
- Build generic cart/order management systems
- Validate and manipulate line items uniformly
- Query or filter by line-level criteria

**Solution in v2.1**: Extract these fields into a dedicated, domain-agnostic CommitmentAttributes schema that:
- Attaches to any Commitment/LineItem/CartItem
- Provides consistent structure across all domains
- Enables uniform processing and validation
- Maintains backward compatibility (old fields in resource types still valid for v2.0 systems)

### Field Semantics

**lineId**: Unique identifier within the parent contract. Examples:
- "LINE001", "ITEM_1", "line-uuid-1234"
- Used for line-by-line order tracking, amendments, and cancellations

**resourceId**: Reference to the Resource (catalog item) being committed. Enables:
- Linking back to inventory/catalog for stock checks
- Fetching resource metadata (description, images, reviews)
- Audit trails and traceability

**offerId**: Reference to the specific Offer under which the commitment was made. Enables:
- Offer-specific pricing verification
- Promotion tracking (which offer was used)
- Time-bound offer validation (did offer expire?)
- Multi-offer scenarios (different items from different offers)

**quantity**: Separate unitCode and unitQuantity provide precision:
- `unitCode: "KG", unitQuantity: 2.5` (2.5 kg)
- `unitCode: "BOX", unitQuantity: 10` (10 boxes)
- Supports decimal quantities (useful for produce, liquids)

**price**: References core PriceSpecification:
- `currency: "INR", amount: "500"` (base price)
- `estimatedValue: "485-515"` (range for variable items)
- `maximumValue: "550"` (cap for open-ended services)

**specialInstructions**: Per-item buyer notes:
- Food: "no onions", "extra hot", "gluten-free"
- Delivery: "side entrance", "before 5pm"
- General: "gift wrap", "fragile", "keep separate"

## Non-Goals

This schema does **not**:
- Store commitment fulfillment state (see Fulfillment)
- Define inventory management (see Resource schema)
- Specify pricing algorithms or discount rules (those belong in Offer)
- Model payment allocation across line items (see Payment)
- Track order modifications/amendments (see Contract state)
- Enforce tax calculations or compliance (provider responsibility)

## Upstream Candidates

Future enhancement opportunities for v2.2+:

1. **Tax Breakdown**: `taxDetails: {taxType: enum, taxRate: number, taxAmount: number}`
2. **Discount Application**: `discounts: array of {discountId, discountType, amount, appliedAt}`
3. **Substitution Rules**: `substitutionAllowed: boolean, preferredSubstitutes: array<resourceId>`
4. **Allergen Warnings**: `allergens: array, allergenWarningRequired: boolean`
5. **Packaging Preferences**: `packagingType: enum, packagingSeparation: boolean`
6. **Warranty/Guarantee**: `warrantyId: string, warrantyTerms: {duration, coverage}`
7. **Serial/Batch Tracking**: `serialNumber: string, batchId: string, mfgDate: date`
8. **Expiry/Shelf-Life**: `expiryDate: date-time, shelfLife: duration`
9. **Compliance Flags**: `customsDeclarable: boolean, hsCode: string`
10. **Return/Exchange**: `returnable: boolean, returnWindow: duration`

## Beckn Integration Notes

- **Core Schema References**: Uses $ref to PriceSpecification from `../../v2.0.0/beckn.yaml`
- **Context/Vocab**: Imports generalized context and vocab from Beckn core v2
- **Protocol Version**: Targets Beckn protocol v2.0 with v2.1 semantic extensions
- **Backward Compatibility**: Not directly compatible with v2 (which didn't have this schema); however, v2 systems can continue using domain-specific fields as fallback

## Migration Guide (v2 → v2.1)

For systems migrating from v2 to v2.1:

### Before (v2)
```json
{
  "foodAndBeverageItem": {
    "resourceId": "res-123",
    "lineId": "line-1",
    "offerId": "offer-456",
    "quantity": {
      "unitCode": "EA",
      "unitQuantity": 2
    },
    "price": { ... }
  }
}
```

### After (v2.1)
```json
{
  "commitment": {
    "commitmentAttributes": {
      "resourceId": "res-123",
      "lineId": "line-1",
      "offerId": "offer-456",
      "quantity": {
        "unitCode": "EA",
        "unitQuantity": 2
      },
      "price": { ... }
    }
  }
}
```

### Coexistence Strategy
- v2.1 recommends using commitmentAttributes
- v2 systems continue using resource-specific fields
- API implementations should accept both during transition period (v2.1-v2.2)

## Version History

- **v2.1.0** (Current) – Initial release; NEW schema consolidating commitment details from v2 domain-specific models
- **Not in v2.0.0** – Fields were scattered across FoodAndBeverageItem, HomeAndKitchenItem, etc.
