# Retail Core Item Attributes (v2)

## Overview
Defines cross-vertical retail item attributes reusable across grocery, F&B, home & kitchen, and future retail domains.

## Design Principles
- Item remains atomic and composable.
- Commercial grouping handled via Offer constructs.
- Regulatory declarations are jurisdiction-neutral.
- Reuses Beckn Core Quantity and schema.org IRIs.

## Extensibility
Domain schemas extend this using `allOf`.

## Example
See `examples/example.json`