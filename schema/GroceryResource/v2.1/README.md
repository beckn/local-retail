# GroceryResourceAttributes Schema

**Container:** `Resource.resourceAttributes`
**Protocol Version:** 2.0
**Semantic Model:** generalised
**Version:** 2.1.0
**Use Cases:** Packaged foods, fresh produce, grocery items
**Tag:** grocery resource

## Overview

Grocery-specific resource attributes for packaged food items and fresh produce on Beckn v2.1.
Extends RetailResource via `allOf` to add domain-specific properties for nutrition
and fresh produce handling.

Migrated from `GroceryItem` (v2) — the container changes from `itemAttributes` to
`resourceAttributes` to align with the v2.1 generalised Resource model.

## Attachment Points

- **Resource.resourceAttributes** — primary attachment. All grocery resources carry these attributes.

## Design Rationale

- **Nutrition as an array**: Multiple nutrition facts (energy, protein, carbs, fat, fiber, sodium)
  are modeled as an array of {nutrient, value} objects to support flexible, per-100g or per-serving declarations.
- **Fresh produce flag**: `freshProduce.netQuantityDeclaration` identifies non-standard packaging items
  (bulk, harvested) that rely on weight/count declarations rather than pre-packaged quantities.
- **Extends RetailCore**: Inherits identity, physical, food classification, packaged goods declaration,
  regulatory disclosures, and credentials from RetailResource.

## Non-Goals

- Pricing (belongs in `offerAttributes`)
- Availability and stock (belongs in `performanceAttributes`)
- Promotional bundles (belong in `offerAttributes`)

## Examples

### Packaged Flour (with nutrition)
```json
{
  "@type": "groc:GroceryResourceAttributes",
  "identity": {
    "brand": "Aashirvaad",
    "originCountry": "IN"
  },
  "physical": {
    "weight": { "unitQuantity": 1, "unitCode": "KG" }
  },
  "food": {
    "classification": "VEG"
  },
  "nutrition": [
    { "nutrient": "Energy", "value": { "unitQuantity": 350, "unitCode": "KCAL" } },
    { "nutrient": "Protein", "value": { "unitQuantity": 10, "unitCode": "GRAM" } }
  ]
}
```

### Fresh Red Apples (with fresh produce flag)
```json
{
  "@type": "groc:GroceryResourceAttributes",
  "identity": {
    "brand": "Organic Valley",
    "originCountry": "IN"
  },
  "physical": {
    "weight": { "unitQuantity": 1, "unitCode": "KG" }
  },
  "food": {
    "classification": "VEG"
  },
  "freshProduce": {
    "netQuantityDeclaration": "1 kg"
  }
}
```

## Related Schemas

- **RetailResource** — base retail resource schema (identity, physical, food)
- **FoodAndBeverageResource** — prepared foods and beverages (cuisine, allergens, preparation)
- **HomeAndKitchenResource** — non-food home and kitchen products
