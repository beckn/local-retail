# RetailResource Schema

**Container:** `Resource.resourceAttributes`
**Protocol Version:** 2.0
**Semantic Model:** generalised
**Use Cases:** Grocery, Food & Beverage, Home & Kitchen, Electronics, Fashion, General Retail
**Tag:** retail core resource

## Overview

Cross-vertical base resource attributes for all retail domains on Beckn v2.1. Provides the
foundational identity, physical characteristics, food classification, packaged goods declarations,
regulatory disclosures, and credential references that apply across retail sub-verticals.

Migrated from `RetailCoreItemAttributes` (v2) — the container changes from `itemAttributes` to
`resourceAttributes` to align with the v2.1 generalised Resource model.


## Attachment Points

- **Resource.resourceAttributes** — primary attachment. All retail resources carry these base attributes.


## Design Rationale

- **Jurisdiction-neutral regulatory fields**: `foodRegulatoryDeclaration.registrations[]` uses a
  scheme+role pattern rather than hardcoding FSSAI or FDA identifiers, enabling cross-country portability.
- **Packaged goods as declaration, not label**: `packagedGoodsDeclaration` models the manufacturer's
  legal declaration (MRP, net quantity, origin) rather than label rendering.
- **Food classification at core level**: VEG/NON_VEG/EGG classification is placed in the retail core
  (not in FoodAndBeverageResource) because it applies to grocery, F&B, and potentially other verticals.


## Non-Goals

- Pricing and commercial terms (belong in `offerAttributes`)
- Per-commitment quantity or line-level data (belong in `commitmentAttributes`)
- Fulfillment/delivery logistics (belong in `performanceAttributes`)


## Upstream Candidates

- `physical.dimensions` pattern (unit + length/breadth/height) is generic enough for Beckn core
- `credentials[]` array pattern could serve any domain needing verifiable credential references
