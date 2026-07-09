# RetailOffer — v2.2

**Schema Pack Version:** 2.2.0
**Status:** Active

## Changes from v2.1

- Added `discountCode` (string, optional, default `"NONE"`): promotional or coupon code associated with the offer.

## Translation Artifacts

- `RetailOffer_from_v2.1.jsonata` — upgrades a v2.1 object to v2.2 by injecting `discountCode: "NONE"` when absent.
- `v2.1/RetailOffer_from_v2.2.jsonata` — downgrades a v2.2 object to v2.1 by dropping `discountCode`.
