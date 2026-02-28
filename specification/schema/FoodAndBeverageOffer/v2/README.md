# Food & Beverage Offer Attributes (v2)

## Overview

`FoodAndBeverageOfferAttributes` extends `RetailCoreOfferAttributes` to support structured customization for restaurant and prepared food use cases.

This pack enables dynamic configuration of an offer (e.g., pizza size, toppings, crust type) without introducing SKU hierarchy or cart semantics into the protocol layer.

## Attachment

- Container: `beckn:Offer`
- Field: `beckn:offerAttributes`
- Extends: `RetailCoreOfferAttributes`

## Covers

- Structured customization groups
- Selection rules (single required, optional, multiple)
- Price deltas per option
- Optional references to Items or Offers
- Availability flags for options

## Customization Model

Customization is modeled as:

Offer
  └── customization
        └── groups[]
              ├── code
              ├── selection rule
              └── options[]
                    ├── code
                    ├── name
                    ├── priceDelta
                    ├── itemRef (optional)
                    └── offerRef (optional)

This allows:

- Size selection
- Toppings
- Spice level
- Crust type
- Combo upgrades
- Optional beverage additions (though addOn/addOnItems remain core constructs)

## Does NOT Duplicate

Inherited from RetailCoreOffer:

- Returns / cancellation / replacement policies
- Payment constraints
- Serviceability
- Core price snapshot (PriceSpecification)
- Add-ons and bundles (core Offer semantics)

## Design Intent

- Keep Item atomic (no parent-child SKU hierarchy)
- Keep customization logic at Offer level
- Support dynamic pricing via priceDelta
- Allow BAP-side UI flexibility
- Remain composable with addOn and addOnItems
- Avoid embedding cart or order-state logic

## Files

- `attributes.yaml` – OpenAPI schema
- `context.jsonld` – JSON-LD mapping
- `profile.json` – Indexing and discovery hints
- `renderer.json` – UI rendering templates
- `examples/` – Example Offer objects

## Version

v2.0.0 – Beckn Protocol v2 aligned