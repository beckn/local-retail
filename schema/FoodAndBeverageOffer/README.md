# FoodAndBeverageOffer Schema

**Container:** `Offer.offerAttributes`
**Protocol Version:** 2.0
**Semantic Model:** generalised
**Use Cases:** Prepared foods, beverages, restaurant items with customization
**Tag:** food and beverage offer

## Overview

Food and beverage-specific offer attributes for prepared foods with customization options on Beckn v2.1.
Extends RetailOffer via `allOf` to add customization groups and options (SIZE, TOPPINGS, SAUCE, etc.).

Migrated from `FoodAndBeverageOffer` (v2) to the v2.1 generalised model. The container changes from
`offerAttributes` to `offerAttributes` (no change), but the schema structure aligns with v2.1 patterns.


## Attachment Points

- **Offer.offerAttributes** — primary attachment. F&B offers with customization carry these attributes.


## Design Rationale

- **Customization as groups with selection constraints**: Each group (SIZE, TOPPINGS) defines its own
  selection constraint: SINGLE_REQUIRED (must pick one), SINGLE_OPTIONAL (pick zero or one),
  MULTIPLE_OPTIONAL (pick zero or more). This flexibility supports diverse product configurations.
- **Options with price deltas**: Each option within a group can have a FIXED or PERCENTAGE price adjustment,
  enabling accurate per-customization pricing. An option with no `priceDelta` incurs no additional charge.
- **Available flag on options**: Allows rapid marking of out-of-stock options without modifying the schema.
- **itemRef / offerRef**: Enable linking customization options to other resources or offers (e.g., a larger
  pizza size that is a separate item in the catalog).
- **Extends RetailCore**: Inherits policies (returns, cancellation), service windows, and other commercial
  terms from RetailOffer.


## Non-Goals

- Nutritional or allergen information (belongs in `FoodAndBeverageResource`)
- Cuisine or preparation details (belong in `FoodAndBeverageResource`)
- Per-commitment quantity (belongs in `commitmentAttributes`)
- Fulfillment scheduling (belongs in `performanceAttributes`)


## Examples


## Related Schemas

- **RetailOffer** — base retail offer schema (policies, terms, service windows)
- **FoodAndBeverageResource** — F&B resource attributes (allergens, cuisine, preparation)
- **GroceryResource** — packaged foods and fresh produce
- **HomeAndKitchenResource** — non-food home and kitchen products
