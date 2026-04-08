# Beckn v2.1 Retail Schema Pack Index

Complete index of three generated Beckn v2.1 schema packs for retail fulfillment, contract, and commitment attributes.

---

## Quick Navigation

### Schema 1: RetailPerformance
- **Purpose:** Performance execution and delivery capability declarations
- **Container:** `performanceAttributes`
- **Prefix:** `rcpa` → https://schema.beckn.io/RetailPerformance#
- **Migration:** From v2 `fulfillmentAttributes` (renamed)
- **Files:**
  - [attributes.yaml](./RetailPerformance/attributes.yaml) - OpenAPI 3.1.1 schema definition
  - [context.jsonld](./RetailPerformance/context.jsonld) - JSON-LD namespace context
  - [vocab.jsonld](./RetailPerformance/vocab.jsonld) - RDF vocabulary definitions
  - [profile.json](./RetailPerformance/profile.json) - Beckn protocol profile
  - [renderer.json](./RetailPerformance/renderer.json) - UI templates
  - [README.md](./RetailPerformance/README.md) - Full documentation

**Key Properties:**
- `supportedPerformanceModes`: DELIVERY, PICKUP, SERVICE (DINE_IN → SERVICE remapping)
- `deliveryDetails`: Address and contact information
- `operatingHours`: Day/time range schedule
- `closures`: Closure periods with dates
- `sla`: Service level agreement (min/max ISO 8601 duration, unitBasis)
- `handling`: FRAGILE, COLD_CHAIN, TEMPERATURE_CONTROLLED, HAZARDOUS, OVERSIZED, BULK, HIGH_VALUE, LIQUID, PERISHABLE
- `installationScheduling`: NEW in v2.1 for home & kitchen item scheduling

---

### Schema 2: RetailContract
- **Purpose:** Contract-level buyer preferences and transaction terms
- **Container:** `contractAttributes`
- **Prefix:** `rcca` → https://schema.beckn.io/RetailContract#
- **Migration:** From v2 `orderAttributes` (direct 1:1 mapping, no structural changes)
- **Files:**
  - [attributes.yaml](./RetailContract/attributes.yaml) - OpenAPI 3.1.1 schema definition
  - [context.jsonld](./RetailContract/context.jsonld) - JSON-LD namespace context
  - [vocab.jsonld](./RetailContract/vocab.jsonld) - RDF vocabulary definitions
  - [profile.json](./RetailContract/profile.json) - Beckn protocol profile
  - [renderer.json](./RetailContract/renderer.json) - UI templates
  - [README.md](./RetailContract/README.md) - Full documentation

**Key Properties:**
- `buyerInstructions`: Free-text special instructions
- `deliveryPreferences`: leaveAtDoor, contactless, preferredTimeSlot
- `gift`: isGift, message, wrap
- `invoicePreferences`: taxId (scheme, country, value), companyName, email
- `loyalty`: programId, pointsRedeemed
- `source`: channel, campaignId

---

### Schema 3: RetailCommitment (NEW)
- **Purpose:** Line-item commitment details for retail contracts
- **Container:** `commitmentAttributes`
- **Prefix:** `rccma` → https://schema.beckn.io/RetailCommitment#
- **Status:** NEW schema in v2.1 (consolidates v2 scattered fields)
- **Files:**
  - [attributes.yaml](./RetailCommitment/attributes.yaml) - OpenAPI 3.1.1 schema definition
  - [context.jsonld](./RetailCommitment/context.jsonld) - JSON-LD namespace context
  - [vocab.jsonld](./RetailCommitment/vocab.jsonld) - RDF vocabulary definitions
  - [profile.json](./RetailCommitment/profile.json) - Beckn protocol profile
  - [renderer.json](./RetailCommitment/renderer.json) - UI templates
  - [README.md](./RetailCommitment/README.md) - Full documentation

**Key Properties:**
- `lineId`: Line reference within contract (required)
- `resourceId`: Reference to Resource/product (required)
- `offerId`: Reference to Offer
- `quantity`: unitCode (EA, KG, L, M, BOX, etc.) + unitQuantity (required)
- `price`: References core PriceSpecification
- `specialInstructions`: Per-item buyer notes (e.g., "no onions")

---

## Technical Reference

### File Standards
Each schema pack contains exactly 6 files:

| File | Purpose | Format |
|------|---------|--------|
| `attributes.yaml` | Complete schema definition | OpenAPI 3.1.1 |
| `context.jsonld` | Namespace and prefix mappings | JSON-LD Context |
| `vocab.jsonld` | Semantic vocabulary definitions | RDF/RDFS |
| `profile.json` | Beckn protocol configuration | JSON |
| `renderer.json` | UI/display templates | JSON |
| `README.md` | Full documentation | Markdown |

### Naming Conventions
- **Container Names:** camelCase (performanceAttributes, contractAttributes, commitmentAttributes)
- **Properties:** camelCase (buyerInstructions, deliveryPreferences)
- **Classes:** PascalCase (PerformanceAttributes, ContractAttributes, CommitmentAttributes)
- **Prefixes:** Short lowercase (rcpa, rcca, rccma)

### Cross-References
All schemas reference core Beckn types:
```
$ref: '../../v2.0.0/beckn.yaml#/components/schemas/...'
```

### Semantic Integration
All schemas use Beckn core:
- **Context:** `https://schema.beckn.io/core/v2/context.jsonld#generalised`
- **Vocabulary:** `https://schema.beckn.io/core/v2/vocab.jsonld`

### Profile Configuration
All profiles specify:
- `protocol_version: "2.0"`
- `semantic_model: "generalised"`
- `version: "2.1.0"`

---

## Directory Structure

```
v2.1/
├── INDEX.md (this file)
├── SCHEMA_GENERATION_SUMMARY.md
├── RetailPerformance/
│   ├── attributes.yaml
│   ├── context.jsonld
│   ├── vocab.jsonld
│   ├── profile.json
│   ├── renderer.json
│   └── README.md
├── RetailContract/
│   ├── attributes.yaml
│   ├── context.jsonld
│   ├── vocab.jsonld
│   ├── profile.json
│   ├── renderer.json
│   └── README.md
└── RetailCommitment/
    ├── attributes.yaml
    ├── context.jsonld
    ├── vocab.jsonld
    ├── profile.json
    ├── renderer.json
    └── README.md
│   └── README.md
└── ...
```

---

## Migration Guide

### v2 to v2.1 Mapping

**RetailPerformance** (from fulfillmentAttributes):
- `supportedFulfillmentTypes` → `supportedPerformanceModes`
- DINE_IN → SERVICE (semantic remapping for broader use)
- NEW: `installationScheduling`

**RetailContract** (from orderAttributes):
- Direct 1:1 field mapping (no changes)
- Container renamed from "order" to "contract"

**RetailCommitment** (NEW schema):
- Consolidates fields scattered in FoodAndBeverageItem, HomeAndKitchenItem, etc.
- Enables domain-agnostic line-item processing
- Supports backward compatibility (v2 systems use resource-specific fields)

---

## Design Highlights

### RetailPerformance
- **Semantic Clarity:** "Performance" describes execution capability, not fulfillment outcome
- **Mode Extensibility:** SERVICE replaces DINE_IN for broader applicability (home services, consultation, etc.)
- **Execution Detail:** installationScheduling bridges capability (in resource) with timing (in attributes)
- **SLA Precision:** unitBasis field clarifies when SLA duration measurement begins

### RetailContract
- **Backward Compatibility:** Preserved all v2 orderAttributes fields
- **Context Clarity:** Buyer-side preferences and terms at contract level
- **Multi-Domain:** Supports both B2C (delivery) and B2B (invoice) use cases
- **Attribution Tracking:** source field enables marketing analytics

### RetailCommitment
- **Domain Agnostic:** Single schema replaces domain-specific item types
- **Consolidation:** Brings together scattered v2 fields (resourceId, lineId, offerId, quantity, price)
- **Traceability:** lineId and resourceId enable line-by-line tracking
- **Flexibility:** quantity.unitCode supports any ISO 80000 unit (EA, KG, L, M, BOX, etc.)

---

## Standards Compliance

- **Format:** OpenAPI 3.1.1
- **Semantics:** RDF/RDFS vocabulary
- **Linked Data:** JSON-LD context
- **Protocol:** Beckn v2.0
- **Duration:** ISO 8601 (P[n]Y[n]M[n]DT[n]H[n]M[n]S)
- **Country:** ISO 3166-1 alpha-2 codes
- **Currency:** ISO 4217 codes
- **Email:** RFC 5322 validation
- **Enums:** Controlled vocabularies with semantic descriptions

---

## Documentation Structure

Each README includes:

1. **Overview** – Purpose, domain coverage, semantic scope
2. **Attachment Points** – Which Beckn core entities use this schema
3. **Schema Structure** – Properties table with types and descriptions
4. **Design Rationale** – Why this design (migrations, semantics, constraints)
5. **Non-Goals** – Explicit boundaries of what schema does NOT do
6. **Upstream Candidates** – Proposed v2.2+ enhancements
7. **Beckn Integration Notes** – References, compatibility, integration points
8. **Version History** – Evolution from v2.0 to v2.1
9. (Bonus) **Migration Guides** – For schemas with structural changes

---

## Validation Checklist

Use this checklist when integrating schemas into your system:

- [ ] All YAML files parse as valid OpenAPI 3.1.1
- [ ] All JSON files are valid JSON (syntax)
- [ ] All JSON-LD files are valid RDF/RDFS
- [ ] All $ref paths resolve correctly
- [ ] All x-jsonld annotations use valid URIs
- [ ] All enum values are documented in vocab.jsonld
- [ ] Required fields are marked in attributes.yaml
- [ ] profile.json indexable/filterable attributes are valid paths
- [ ] renderer.json templates reference valid property paths
- [ ] README.md files are complete and readable

---

## Contact & Support

For questions or issues with these schema packs:

1. Review the specific schema's README.md
2. Check the SCHEMA_GENERATION_SUMMARY.md for detailed explanations
3. Refer to Beckn v2.0 core documentation for integration patterns
4. Validate files against their respective specifications

---

**Generated:** 2026-03-25
**Status:** Complete and ready for integration
**Total Files:** 18 (6 per schema)
**Total Size:** ~73 KB
