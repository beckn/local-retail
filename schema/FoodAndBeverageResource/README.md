# FoodAndBeverageResource Schema

**Container:** `Resource.resourceAttributes`
**Protocol Version:** 2.0
**Semantic Model:** generalised
**Use Cases:** Prepared foods, beverages, restaurant items, catered meals
**Tag:** food and beverage resource

## Overview

Food and beverage-specific resource attributes for prepared foods and beverages on Beckn v2.1.
Extends RetailResource via `allOf` to add allergens, cuisine type, and preparation
instructions.

Migrated from `FoodAndBeverageItem` (v2). **Important migration note:** In v2, FoodAndBeverageItem
contained fields like `resourceId`, `lineId`, `offerId`, `quantity`, and `price`. These have moved
to **RetailCommitment** in v2.1 (line-level commitment data) and **RetailOffer**
(pricing and commercial terms). Only semantic food and beverage properties remain in resourceAttributes.

The container changes from `itemAttributes` to `resourceAttributes` to align with the v2.1 generalised
Resource model.


## Attachment Points

- **Resource.resourceAttributes** — primary attachment. All F&B resources carry these attributes.


## Design Rationale

- **Allergens as an array**: Multiple allergen types (GLUTEN, NUTS, DAIRY, EGGS, SOY, SHELLFISH, FISH, SESAME)
  are modeled as an array for discovery and filtering. Critical for consumer safety.
- **Cuisine as a string**: Flexible cuisine classification (Italian, Indian, Mexican, American, Chinese, Mediterranean, etc.)
  enables search and discovery without imposing a rigid enumeration.
- **Preparation as a container**: Groups related instructions (how to prepare, how to store, how long it lasts)
  for coherent presentation and maintenance.
- **Extends RetailCore**: Inherits identity, physical characteristics, food classification, packaged goods
  declaration, regulatory disclosures, and credentials from RetailResource.


## Non-Goals

- Pricing and commercial terms (belong in `offerAttributes`)
- Customization options (SIZE, TOPPINGS) (belong in `FoodAndBeverageOffer.customization`)
- Per-commitment quantity or line-level data (belong in `commitmentAttributes`)
- Fulfillment/delivery and service slots (belong in `performanceAttributes`)


## Examples


## Related Schemas

- **RetailResource** — base retail resource schema (identity, physical, food classification)
- **FoodAndBeverageOffer** — F&B offer-level customization (SIZE, TOPPINGS, etc.)
- **GroceryResource** — packaged foods and fresh produce (nutrition, fresh produce flag)
- **HomeAndKitchenResource** — non-food home and kitchen products
