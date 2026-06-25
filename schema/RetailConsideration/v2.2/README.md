# RetailConsideration — v2.2

**Schema Pack Version:** 2.2.0
**Status:** Test branch — schema version mediation testing only. Do not merge to main.

## Changes from v2.1

- **`currency` → `currencyCode`**: Renamed to make the ISO 4217 semantics explicit and consistent with other Beckn schema packs that use `currencyCode` as the standard field name.

## Migration

Nodes on v2.1 sending to nodes on v2.2 (or vice versa) are automatically bridged by the `schemaversionmediator` plugin using the translation artifacts in this folder.

| Direction | Artifact |
|---|---|
| v2.1 → v2.2 | `RetailConsideration_from_v2.1` |
| v2.2 → v2.1 | `../v2.1/RetailConsideration_from_v2.2` |
