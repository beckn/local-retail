# Beckn v2.1 Retail Schema Pack

**Protocol Version:** 2.0
**Semantic Model:** generalised (Resource / Offer / Contract)
**Schema Pack Version:** 2.1.0
**Domain:** Retail (Grocery, Food & Beverage, Home & Kitchen)
**Migration Source:** Beckn v2 Retail Attribute Schemas

## Overview

This schema pack provides the complete set of v2.1 generalised extension schemas for the
retail domain on the Beckn Protocol. It covers three sub-verticals — Grocery, Food & Beverage,
and Home & Kitchen — using a layered architecture where cross-vertical "Retail Core" schemas
provide the base, and domain-specific schemas extend them.

## Schema Taxonomy

### Retail Core (cross-vertical base)

| Schema | Container | Purpose |
|--------|-----------|---------|
| RetailResource | `resourceAttributes` | Identity, physical characteristics, food classification, packaged goods declarations, regulatory disclosures, credentials |
| RetailOffer | `offerAttributes` | Return/cancellation/replacement policies, payment constraints, serviceability, operating hours |
| RetailPerformance | `performanceAttributes` | Performance modes (DELIVERY/PICKUP/SERVICE), delivery details, SLA, handling, installation scheduling |
| RetailContract | `contractAttributes` | Buyer instructions, delivery preferences, gift, invoice preferences, loyalty, source |
| RetailCommitment | `commitmentAttributes` | Per-line resource/offer references, quantity, price, special instructions |
| RetailConsideration | `considerationAttributes` | Payment methods, currency, charge breakup, loyalty points applied |
| RetailSettlement | `settlementAttributes` | Gateway details, settlement method, settled amount, reconciliation |

### Domain Extensions

| Schema | Container | Extends | Purpose |
|--------|-----------|---------|---------|
| GroceryResource | `resourceAttributes` | RetailResource | Nutrition data, fresh produce declarations |
| FoodAndBeverageResource | `resourceAttributes` | RetailResource | Allergens, cuisine, preparation instructions |
| FoodAndBeverageOffer | `offerAttributes` | RetailOffer | Customization groups (size, toppings, etc.) |
| HomeAndKitchenResource | `resourceAttributes` | RetailResource | Care instructions, installation capability, usage, warranty |

## Inheritance Hierarchy

```
resourceAttributes:
  RetailResource
  ├── GroceryResource
  ├── FoodAndBeverageResource
  └── HomeAndKitchenResource

offerAttributes:
  RetailOffer
  └── FoodAndBeverageOffer

performanceAttributes:
  RetailPerformance

contractAttributes:
  RetailContract

commitmentAttributes:
  RetailCommitment

considerationAttributes:
  RetailConsideration

settlementAttributes:
  RetailSettlement
```

## Migration from v2

This pack was migrated from the Beckn v2 Retail Attribute Schemas. Key changes:

| v2 Schema | v2.1 Schema | Change |
|-----------|-------------|--------|
| RetailCoreItemAttributes | RetailResource | Container: itemAttributes → resourceAttributes |
| RetailOffer | RetailOffer | Context imports updated; container unchanged |
| RetailCoreFulfillmentAttributes | RetailPerformance | Container: fulfillmentAttributes → performanceAttributes; DINE_IN → SERVICE |
| RetailCoreOrderAttributes | RetailContract | Container: orderAttributes → contractAttributes |
| — | RetailCommitment | NEW: per-line fields extracted from FoodAndBeverageItem |
| — | RetailConsideration | NEW: payment method and breakup details |
| — | RetailSettlement | NEW: settlement discharge records |
| GroceryItem | GroceryResource | Container: itemAttributes → resourceAttributes |
| FoodAndBeverageItem | FoodAndBeverageResource | Container changed; line-level fields (resourceId, lineId, offerId, quantity, price) moved to CommitmentAttributes |
| FoodAndBeverageOffer | FoodAndBeverageOffer | Context imports updated; container unchanged |
| HomeAndKitchenItem | HomeAndKitchenResource | Container changed; installation split: capability in Resource, scheduling in Performance |

## Design Decisions

1. **Installation split**: HomeAndKitchenResource retains `installation` as a capability declaration
   (does the product need installation? does the seller provide it?). The actual scheduling of
   installation execution lives in RetailPerformance.installationScheduling.

2. **Commitment extraction**: FoodAndBeverageItem v2 carried per-line order fields (resourceId,
   lineId, offerId, quantity, price) in resourceAttributes. These are now properly placed in
   RetailCommitment, which is the v2.1 home for per-commitment metadata.

3. **Minimal Consideration/Settlement**: Retail is overwhelmingly monetary, so these schemas are
   intentionally thin. They capture payment methods, charge breakups, and settlement discharge
   records without over-engineering non-monetary scenarios.

4. **DINE_IN → SERVICE**: The v2 fulfillment type DINE_IN is remodeled as performance mode SERVICE
   in v2.1, reflecting that dine-in is a service provisioning mode rather than a delivery type.
