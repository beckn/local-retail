# Retail v2 — Extended Attribute Schemas (Beckn v2)

## Overview

This folder defines the **Retail schema family** for Beckn Protocol v2.

These schemas extend Beckn core container objects (`Item`, `Offer`, `Fulfillment`, `Order`) with structured retail-specific attributes while preserving core protocol composability.

The objectives are to:

- Fully express retail domain semantics using structured schemas  
- Avoid duplicating Beckn core constructs  
- Maintain clean layering (Core → RetailCore → Domain-specific)  
- Support international and cross-network adoption  
- Enable consistent indexing, rendering, and validation  

---

## Approach

The schemas in this folder were developed through:

- Reviewing and learning from existing retail network implementations  
- Identifying recurring retail-wide concepts  
- Extracting common semantics into RetailCore schemas  
- Retaining only truly domain-specific extensions in vertical schemas  
- Aligning all modeling decisions with Beckn v2 composability principles  

The design intentionally:

- Avoids introducing hierarchy or graph semantics into atomic entities  
- Avoids duplicating payment, fulfillment, or order constructs already present in core  
- Supports progressive evolution and upstreaming where justified  

---

## Schema Index

Below is a high-level definition of each schema in this folder.

---

### 🧾 RetailCoreItemAttributes

**Location:** [`./RetailCoreItem/v2/`](./RetailCoreItem/v2/)

Defines intrinsic retail product metadata:

- Identity (brand, origin)  
- Physical attributes (weight, dimensions, volume)  
- Appearance  
- Packaged goods declaration  
- Regulatory declarations  
- Generic credentials  

**Attaches to:**  
`beckn:Item → beckn:itemAttributes`

---

### 🏷 RetailCoreOfferAttributes

**Location:** [`./RetailCoreOffer/v2/`](./RetailCoreOffer/v2/)

Defines retail-wide commercial semantics:

- Returns, cancellation, replacement policies  
- Payment constraints (e.g., COD availability)  
- Serviceability constraints (distance, timing)  
- Reuse of `RecurringSchedule` for timing rules  

**Attaches to:**  
`beckn:Offer → beckn:offerAttributes`

---

### 🚚 RetailCoreFulfillmentAttributes

**Location:** [`./RetailCoreFulfillment/v2/`](./RetailCoreFulfillment/v2/)

Defines retail fulfillment extensions:

- Supported fulfillment types  
- Delivery endpoint details  
- SLA modeling  
- Handling requirements  
- Reuse of `RecurringSchedule` for operating hours  

**Attaches to:**  
`beckn:Fulfillment → beckn:fulfillmentAttributes`

---

### 🧾 RetailCoreOrderAttributes

**Location:** [`./RetailCoreOrder/v2/`](./RetailCoreOrder/v2/)

Defines transaction-instance retail metadata:

- Buyer instructions  
- Delivery preferences  
- Gift metadata  
- Loyalty details  
- Tax profile information  

**Attaches to:**  
`beckn:Order → beckn:orderAttributes`

---

### 🔁 RecurringSchedule

**Location:** [`./RecurringSchedule/v2/`](./RecurringSchedule/v2/)

Defines reusable recurring temporal constraints such as:

- Operating hours  
- Offer timing windows  
- Pickup schedules  
- Delivery acceptance windows  

This schema is referenced by:

- `RetailCoreOfferAttributes`  
- `RetailCoreFulfillmentAttributes`  

It is intentionally modeled as a standalone schema to:

- Avoid duplication  
- Maintain a single authoritative definition  
- Enable future upstreaming into Beckn Core if validated across domains  

---

### 🥬 GroceryItemAttributes

**Location:** [`./GroceryItem/v2/`](./GroceryItem/v2/)

Extends RetailCoreItem with grocery-specific metadata:

- Nutrition facts  
- Fresh produce declarations  

**Attaches to:**  
`beckn:Item → beckn:itemAttributes`

---

### 🍕 FoodAndBeverageItemAttributes

**Location:** [`./FoodAndBeverageItem/v2/`](./FoodAndBeverageItem/v2/)

Extends RetailCoreItem with F&B-specific metadata:

- Cuisine  
- Dietary classification  
- Allergen information  

**Attaches to:**  
`beckn:Item → beckn:itemAttributes`

---

### 🍕 FoodAndBeverageOfferAttributes

**Location:** [`./FoodAndBeverageOffer/v2/`](./FoodAndBeverageOffer/v2/)

Extends RetailCoreOffer with structured customization semantics:

- Customization groups  
- Selection rules  
- Option-level price deltas  

**Attaches to:**  
`beckn:Offer → beckn:offerAttributes`

---

### 🏠 HomeAndKitchenItemAttributes

**Location:** [`./HomeAndKitchenItem/v2/`](./HomeAndKitchenItem/v2/)

Extends RetailCoreItem with home & kitchen metadata:

- Warranty  
- Installation requirements  
- Usage characteristics  

**Attaches to:**  
`beckn:Item → beckn:itemAttributes`

---

## Design Principles

### Atomic Item Modeling

Items remain atomic sellable units.

Hierarchical SKU relationships are not embedded in the protocol layer.

Commercial grouping is supported via:

- `Offer.items`  
- `Offer.addOn`  
- `Offer.addOnItems`  

UI-level grouping remains a consuming application concern.

---

### Clear Separation of Concerns

| Concern | Schema Layer |
|---------|-------------|
| Product metadata | Item schemas |
| Commercial policies | Offer schemas |
| Delivery & SLA | Fulfillment schemas |
| Buyer transaction metadata | Order schemas |
| Payment method & status | Beckn Core Payment |
| Bundles & add-ons | Beckn Core Offer |
| Pricing structure | Beckn Core `PriceSpecification` |
| Absolute time windows | Beckn Core `TimePeriod` |
| Recurring retail schedules | Retail `RecurringSchedule` |

---

### Reusable Temporal Modeling

Recurring temporal constraints (operating hours, serviceability timing) are modeled using the reusable `RecurringSchedule` schema within Retail v2.

Future upstreaming to Beckn Core as a generalized recurring schedule abstraction may be considered after broader cross-domain validation.

---

### Extensibility

- All schemas use OpenAPI 3.1.1  
- JSON-LD contexts define semantic mappings  
- `@import` enables inheritance without duplication  
- Vocabulary files define domain-specific enumerations  
- Profile files support indexing hints  
- Renderer files support UI generation  

---

## Schema Folder Structure

Each schema in this folder follows a consistent structure to ensure validation, semantic clarity, and operational usability.

| File / Folder | Purpose |
|---------------|---------|
| `attributes.yaml` | OpenAPI 3.1.1 schema definitions for the entity, including JSON-LD annotations (`x-jsonld`). Defines the structural contract. |
| `context.jsonld` | JSON-LD context mapping all properties to schema.org or Beckn IRIs. Enables semantic interoperability and linked data compatibility. |
| `vocab.jsonld` | Domain-specific vocabulary definitions for enumerations or terms not covered by schema.org. Provides semantic grounding. |
| `profile.json` | Operational profile describing indexing hints, minimal discovery fields, filterable/sortable paths, and implementation guidance. |
| `renderer.json` | Default UI rendering templates (data bindings and HTML snippets) to support consistent front-end display of schema data. |
| `README.md` | Human-readable documentation explaining intent, design decisions, attachment points, and usage guidance. |
| `examples/` | End-to-end example payloads demonstrating correct usage of the schema within Beckn container objects and transaction flows. |

This structure enables:

- Structural validation via OpenAPI  
- Semantic interoperability via JSON-LD  
- Efficient indexing and discovery  
- Predictable UI rendering  
- Example-driven implementation and onboarding  

---