# Grocery Item Attributes (v2)

## Overview

`GroceryItemAttributes` extends `RetailCoreItemAttributes` for grocery-specific item semantics.

This pack adds structured nutrition information and fresh produce declarations while inheriting all common retail constructs (identity, physical attributes, regulatory declarations, additives, credentials, etc.) from RetailCoreItem.

## Attachment

- Container: `beckn:Item`
- Field: `beckn:itemAttributes`
- Extends: `RetailCoreItemAttributes`

## Covers

- Nutrition facts (using Beckn core `Quantity`)
- Fresh produce declarations (for loose/unpackaged goods)

## Does NOT Duplicate

The following are inherited from RetailCoreItem:

- Brand and origin
- Physical attributes (weight, dimensions, appearance)
- Packaged goods declaration
- Food regulatory declarations
- Additives
- Credentials

## Design Intent

- Keep grocery-specific logic minimal
- Reuse Beckn core Quantity for measurement
- Remain internationally neutral
- Avoid regulatory duplication
- Preserve composability across retail verticals

## Files

- `attributes.yaml` – OpenAPI schema
- `context.jsonld` – JSON-LD mapping
- `vocab.jsonld` – Grocery vocabulary namespace
- `profile.json` – Indexing and discovery hints
- `renderer.json` – UI rendering templates
- `examples/` – Example Item objects

## Version

v2.0.0 – Beckn Protocol v2 aligned