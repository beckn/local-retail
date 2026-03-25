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

### Core Container
```
performanceAttributes: PerformanceAttributes
```

### Primary Properties

| Property | Type | Migrated | Notes |
|----------|------|----------|-------|
| `supportedPerformanceModes` | Array<Enum> | ✓ | From `supportedFulfillmentTypes`; DINE_IN → SERVICE |
| `deliveryDetails` | Object | ✓ | Address and contact info for delivery |
| `operatingHours` | Array<OperatingHour> | ✓ | Days and time ranges for operation |
| `closures` | Array<Closure> | ✓ | Closure periods with start/end dates |
| `sla` | Object | ✓ | Service level agreement with duration and basis |
| `handling` | Array<Enum> | ✓ | Special handling capabilities supported |
| `installationScheduling` | Object | ✗ | NEW in v2.1 for H&K installation detail |

### Enum Values

#### supportedPerformanceModes
- `DELIVERY` – Delivery to customer location
- `PICKUP` – Customer pickup from provider location
- `SERVICE` – Service provisioning at customer location (remapped from DINE_IN)

#### sla.unitBasis
- `ORDER_CONFIRMATION` – Duration measured from order confirmation
- `PACKING_COMPLETE` – Duration measured from packing completion
- `DISPATCH` – Duration measured from dispatch/shipment

#### handling (Multi-select)
- `FRAGILE` – Handles fragile items carefully
- `COLD_CHAIN` – Maintains cold chain for perishables
- `TEMPERATURE_CONTROLLED` – Maintains temperature control
- `HAZARDOUS` – Handles hazardous materials safely
- `OVERSIZED` – Handles oversized items
- `BULK` – Handles bulk orders
- `HIGH_VALUE` – Handles high-value items with security
- `LIQUID` – Handles liquid products
- `PERISHABLE` – Handles perishable items

## Design Rationale

### Migration from v2 (fulfillmentAttributes → performanceAttributes)

**Naming Change**: The term "performance" better captures the semantic intent. Fulfillment describes the outcome; performance describes the execution capability. This naming aligns with industry practice and clarifies the distinction from outcome-focused attributes.

**DINE_IN → SERVICE Remapping**: In v2, DINE_IN was used to indicate dine-in service. In v2.1, this is remodeled as SERVICE (generic service provisioning mode) to support broader use cases beyond food-and-beverage, such as home services, installation, and consulting.

**operatingHours Structure**: Enhanced from simple schedules to support per-day-range specifications, enabling providers to express complex weekly schedules (e.g., different hours per weekday).

**installationScheduling (NEW)**: Separated from HomeAndKitchenResource's capability declaration into concrete execution details. The capability (whether installation is offered) remains in the resource; the scheduling detail (when, proposed date, notes) lives here.

### SLA Design

The `unitBasis` field provides clarity on when an SLA duration begins:
- **ORDER_CONFIRMATION**: For guaranteed service windows post-order
- **PACKING_COMPLETE**: For fulfillment pipeline tracking
- **DISPATCH**: For last-mile SLA commitments

This allows precise SLA communication without ambiguity about the start reference point.

### Handling Capabilities

Multi-select handling types enable providers to declare comprehensive capability profiles. This supports:
- Item-level fulfillment constraints (e.g., "cold chain + fragile" for frozen gifts)
- Buyer filtering (e.g., "show me same-day delivery providers with cold chain")
- Logistics optimization (e.g., route consolidation for bulk items)

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

## Beckn Integration Notes

- **Core Schema References**: Uses `$ref` to Address and PriceSpecification from `../../v2.0.0/beckn.yaml`
- **Context/Vocab**: Imports generalized context and vocab from Beckn core v2
- **Protocol Version**: Targets Beckn protocol v2.0 with v2.1 semantic extensions
- **Backward Compatibility**: Not directly compatible with v2 fulfillmentAttributes; explicit migration mapping required

## Version History

- **v2.1.0** (Current) – Initial release; migration from fulfillmentAttributes with installationScheduling addition
- **v2.0.0** – Original fulfillmentAttributes (deprecated in favor of performanceAttributes)
