# RetailOffer Schema

**Container:** `Offer.offerAttributes`
**Protocol Version:** 2.0
**Semantic Model:** generalised
**Version:** 2.1.0
**Use Cases:** Grocery, Food & Beverage, Home & Kitchen, General Retail
**Tag:** retail core offer

## Overview

Cross-vertical offer attributes for retail domains on Beckn v2.1. Captures the commercial terms
under which retail resources are offered: return/cancellation/replacement policies, payment
constraints (e.g., COD availability), serviceability rules, operating hours, and holidays.

Container name unchanged from v2 (`offerAttributes`), but context imports updated to the
v2.1 generalised core.

## Attachment Points

- **Offer.offerAttributes** — primary attachment. All retail offers carry these commercial terms.

## Design Rationale

- **Policy triplet (return/cancellation/replacement)**: Modeled as three parallel sub-objects with
  consistent shape (allowed, window, method/conditions) for uniform BAP rendering.
- **Serviceability as offer-level**: Distance and timing constraints are offer-level concerns
  (a seller may offer different serviceability per offer tier) rather than resource-level.
- **COD as payment constraint**: Placed in offerAttributes rather than considerationAttributes
  because COD availability is a commercial term of the offer, not a property of the payment itself.

## Non-Goals

- Pricing / price breakdowns (belong in core Consideration or PriceSpecification)
- F&B-specific customization groups (belong in FoodAndBeverageOffer extension)
- Fulfillment logistics (belong in performanceAttributes)

## Upstream Candidates

- The policy triplet pattern (allowed + window + method + conditions) is generic enough for
  any domain that has return/cancellation/replacement semantics
