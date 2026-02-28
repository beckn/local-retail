# Retail Core Offer Attributes (v2)

## Overview
Defines retail offer-level constraints and policies that extend Beckn core `Offer` using `beckn:offerAttributes`.

This pack captures ONDC-style retail semantics in a clean, offer-centric model:
- returns / cancellation / replacement policies
- COD availability constraints
- additional serviceability constraints beyond geographic eligibility (`eligibleRegion` in core Offer)

## Key Design Notes
- Geographic eligibility is handled by core `Offer.eligibleRegion`.
- `serviceability` here is only for additional constraints (distance + timing).
- `OperatingSchedule` is used as a reusable recurring schedule structure.

## Files
- `attributes.yaml`: OpenAPI schemas + x-jsonld
- `context.jsonld`: JSON-LD context mapping
- `vocab.jsonld`: Local vocabulary for enums
- `profile.json`: discovery/index hints for implementers
- `renderer.json`: default UI rendering templates (structured + HTML)
- `examples/`: end-to-end Offer examples using Beckn core envelope + offerAttributes pack

## Examples
See:
- `examples/offer-with-policies-and-cod.json`
- `examples/offer-with-serviceability-timing.json`