# RetailPerformance Schema Pack v2.1

## Overview

RetailPerformance is a Beckn v2.1 schema pack that declares performance execution and delivery capability parameters for retail fulfillment. This schema was **migrated from v2's `RetailCoreFulfillmentAttributes`** with significant renaming and structural enhancements to better reflect the semantic intent of performance modes rather than fulfillment types.

The schema enables providers to declare:
- **Performance modes** (DELIVERY, PICKUP, SERVICE) they can execute
- **Delivery specifications** (address, contact, operating hours, closures)
- **Service-level agreements** with defined minimum/maximum durations
- **Handling capabilities** for specialized items (fragile, cold chain, hazardous, etc.)
- **Installation scheduling** for home and kitchen items (new in v2.1)


## Attachment Points

This schema attaches to the following Beckn v2.0 core entities via the `performanceAttributes` container:

- **Catalog/Provider**: Declares global fulfillment capabilities
- **Item**: Item-level fulfillment constraints and special handling
- **Fulfillment**: Specific fulfillment execution details
- **Commitment**: Per-commitment performance specifications


## Schema Structure


## Design Rationale


## Non-Goals

This schema does **not**:
- Specify actual fulfillment state or progress (use Fulfillment for that)
- Define item-by-item handling constraints (constraints are in Item schema)
- Encode pricing or surcharge models for special handling
- Model customer communication preferences (see ContractAttributes)
- Define post-delivery or last-mile specific behaviors (see Fulfillment schema)


## Upstream Candidates

Future enhancement opportunities for v2.2+:

1. **Surcharge Model**: Optional array of handling surcharges (e.g., `FRAGILE_SURCHARGE: {charge_percent: 5, min_charge: 500}`)
2. **Availability Windows**: `availabilityWindows` for time-slot booking with slot capacity
3. **Temperature Ranges**: `temperatureRanges` for precision control (min/max Celsius)
4. **Weight Limits**: `weightLimits: {minKg, maxKg}` for handling constraints
5. **Geographic Restrictions**: `serviceableAreas` referencing geo-fenced zones
6. **Fulfillment Levels**: Tiered service levels (standard, express, premium) with associated SLAs
7. **Partner Integrations**: `partnerNetworks` for third-party logistics providers
8. **Eco-Credentials**: `ecoFeatures` for sustainable delivery (carbon-neutral, package reuse, etc.)
