# Home & Kitchen Item Attributes (v2)

## Overview

`HomeAndKitchenItemAttributes` extends `RetailCoreItemAttributes` for home, furniture, appliances, and kitchen product use cases.

This pack captures care instructions, installation requirements, usage constraints, and warranty information while inheriting all common retail constructs from RetailCoreItem.

## Attachment

- Container: `beckn:Item`
- Field: `beckn:itemAttributes`
- Extends: `RetailCoreItemAttributes`

## Covers

- Care and maintenance instructions
- Installation requirements and support
- Usage constraints (indoor/outdoor compatibility)
- Warranty information (if item-level and generic)

## Does NOT Duplicate

Inherited from RetailCoreItem:

- Identity (brand, origin)
- Physical attributes (weight, dimensions, appearance)
- Packaged goods declarations
- Regulatory information
- Additives and credentials

## Design Intent

- Keep home & kitchen extensions domain-specific
- Avoid duplication of common retail semantics
- Preserve composability across retail verticals

## Files

- `attributes.yaml` – OpenAPI schema
- `context.jsonld` – JSON-LD mapping
- `vocab.jsonld` – Domain vocabulary namespace
- `profile.json` – Indexing and discovery hints
- `renderer.json` – UI rendering templates
- `examples/` – Example Item objects

## Version

v2.0.0 – Beckn Protocol v2 aligned