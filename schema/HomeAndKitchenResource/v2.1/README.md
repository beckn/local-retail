# HomeAndKitchenResourceAttributes Schema

**Container:** `Resource.resourceAttributes`
**Protocol Version:** 2.0
**Semantic Model:** generalised
**Version:** 2.1.0
**Use Cases:** Furniture, appliances, kitchenware, home décor items
**Tag:** home and kitchen resource

## Overview

Home and kitchen-specific resource attributes for furniture, appliances, and household products on Beckn v2.1.
Extends RetailResource via `allOf` to add care instructions, installation requirements, usage
context, and warranty information.

Migrated from `HomeAndKitchenItem` (v2) — the container changes from `itemAttributes` to
`resourceAttributes` to align with the v2.1 generalised Resource model.

## Attachment Points

- **Resource.resourceAttributes** — primary attachment. All home and kitchen resources carry these attributes.

## Design Rationale

- **Installation capability vs. scheduling**: `installation.required` and `installation.providedBySeller` are
  capability declarations (does this product need installation? does the seller offer it?). The actual
  scheduling of installation slots and time windows belongs in **RetailPerformance.installationScheduling**.
- **Usage context**: Flags like `indoorOnly`, `outdoorOnly`, and `compatibleWith` enable filtering and
  discovery of items suitable for specific environments or standards (e.g., "120V electrical outlet", "standard doorway width").
- **Warranty structure**: Separates warranty duration (ISO 8601 duration), type (MANUFACTURER vs. SELLER),
  and detailed terms (coverage, exclusions) for flexible representation.
- **Extends RetailCore**: Inherits identity, physical characteristics, packaged goods declaration, regulatory
  disclosures, and credentials from RetailResource.

## Non-Goals

- Pricing and commercial terms (belong in `offerAttributes`)
- Installation scheduling and slot availability (belong in `performanceAttributes`)
- Per-commitment quantity or line-level data (belong in `commitmentAttributes`)
- User reviews or ratings (belong in separate feedback schemas)

## Examples

### Oak Wood Dining Table
```json
{
  "@type": "hkr:HomeAndKitchenResourceAttributes",
  "identity": {
    "brand": "Furniture Co.",
    "originCountry": "IN"
  },
  "physical": {
    "dimensions": {
      "unit": "CM",
      "length": 180,
      "breadth": 90,
      "height": 76
    },
    "material": "Oak wood",
    "finish": "Matte lacquer"
  },
  "careInstructions": "Wipe with a dry cloth. For stubborn stains, use a slightly damp cloth with mild soap. Avoid harsh chemicals. Polish quarterly with furniture wax for best appearance.",
  "installation": {
    "required": true,
    "providedBySeller": true,
    "notes": "Professional assembly recommended. Includes assembly kit and screws."
  },
  "usage": {
    "indoorOnly": true,
    "compatibleWith": [
      "standard 4-seater dining chairs",
      "standard electrical outlet for extension cords"
    ]
  },
  "warranty": {
    "duration": "P3Y",
    "type": "MANUFACTURER",
    "terms": "Covers defects in materials and workmanship for 3 years. Excludes damage from misuse or accidents."
  }
}
```

## Related Schemas

- **RetailResource** — base retail resource schema (identity, physical, dimensions)
- **RetailPerformance** — installation scheduling and service slots (installationScheduling)
- **GroceryResource** — packaged foods and fresh produce
- **FoodAndBeverageResource** — prepared foods and beverages
