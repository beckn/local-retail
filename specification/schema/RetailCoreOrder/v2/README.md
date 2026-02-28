# Retail Core Order Attributes (v2)

## Overview
`RetailCoreOrderAttributes` is an order-level Attribute Pack attached to Beckn core `Order` at `beckn:orderAttributes`.

This pack captures retail-specific, transaction-instance metadata that is commonly needed during negotiation/checkout and post-order servicing, without duplicating core Order semantics.

## Attachment
- Container: `beckn:Order`
- Attachment point: `beckn:orderAttributes`

## What belongs here (and what does not)
### Included (retail transaction metadata)
- Buyer instructions (e.g., “call on arrival”)
- Delivery preferences expressed by buyer during negotiation (contactless / leave-at-door / preferred slot)
- Gift preferences (gift wrap, message)
- Invoice preferences (company name, email, jurisdiction-neutral taxId)
- Loyalty program usage (program ID, points redeemed)
- Attribution metadata (source channel, campaign id)

### Not included (handled elsewhere)
- Pricing, offer terms, and policies → `Offer` + `RetailCoreOfferAttributes`
- Delivery address, tracking, SLA, handling → `Fulfillment` + `RetailCoreFulfillmentAttributes`
- Payment method/status → core `Payment` object

## Files
- `attributes.yaml`: OpenAPI 3.1.1 schema for RetailCoreOrderAttributes
- `context.jsonld`: JSON-LD mapping (schema.org + retail namespace)
- `vocab.jsonld`: Retail order vocabulary (minimal currently)
- `profile.json`: Indexing/filtering hints; declares `extends` + `attachAt`
- `renderer.json`: UI templates; assumes root is full core Order and can render both core + pack fields
- `examples/`: End-to-end Order examples

## Examples
See:
- `examples/order-with-preferences.json`
- `examples/order-minimal.json`