# RetailContract Schema Pack v2.1

## Overview

RetailContract is a Beckn v2.1 schema pack that declares buyer preferences, special instructions, and transaction terms at the contract (order) level. This schema was **migrated directly from v2's `RetailCoreOrderAttributes`** with no structural changes, establishing buyer-side terms and preferences for fulfillment execution.

The schema enables buyers to specify:
- **Special instructions** for the provider
- **Delivery preferences** (timing, contactless, leave-at-door options)
- **Gift attributes** (gift wrapping, personalized messages)
- **Invoice preferences** (tax ID, company name, email)
- **Loyalty program** participation and points redemption
- **Source attribution** for marketing and analytics

## Attachment Points

This schema attaches to the following Beckn v2.0 core entities via the `contractAttributes` container:

- **Contract**: High-level buyer terms and preferences
- **Order** (legacy naming): Buyer-side special instructions and delivery preferences
- **Fulfillment**: Fulfillment-specific buyer constraints

## Schema Structure

### Core Container
```
contractAttributes: ContractAttributes
```

### Primary Properties

| Property | Type | Status | Notes |
|----------|------|--------|-------|
| `buyerInstructions` | String | Migrated | Free-text buyer-to-provider notes |
| `deliveryPreferences` | Object | Migrated | Timing, contactless, leave-at-door |
| `gift` | Object | Migrated | Gift wrapping and message |
| `invoicePreferences` | Object | Migrated | Tax ID and billing address details |
| `loyalty` | Object | Migrated | Loyalty program and points redemption |
| `source` | Object | Migrated | Channel and campaign attribution |

### deliveryPreferences Structure

```yaml
leaveAtDoor: boolean          # Permission to leave at door
contactless: boolean           # Contactless delivery request
preferredTimeSlot:
  start: date-time            # Window start
  end: date-time              # Window end
```

### invoicePreferences Structure

```yaml
taxId:
  scheme: string              # Tax scheme (GST, VAT, PAN, etc.)
  country: string             # ISO 3166-1 country code
  value: string               # Tax ID value (required)
companyName: string           # Company name for invoice
email: string                 # Invoice delivery email
```

### loyalty Structure

```yaml
programId: string             # Loyalty program identifier
pointsRedeemed: number        # Points used in this transaction
```

### source Structure

```yaml
channel: string               # Transaction channel (web, mobile, app, api, social, email)
campaignId: string            # Marketing campaign identifier
```

## Design Rationale

### Direct Migration from v2 (orderAttributes → contractAttributes)

**Naming Shift**: The term "contract" better represents the semantic scope. A contract encompasses the buyer-provider agreement including delivery terms, instructions, and special preferences. This aligns with Beckn v2.1's modular contract model where a contract is the economic unit of a transaction.

**Zero Structural Changes**: All fields from v2's `RetailCoreOrderAttributes` are carried over without modification. This ensures:
- Backward compatibility with v2 systems
- Minimal migration friction
- Familiar field semantics for existing implementations

### Field Semantics

**buyerInstructions**: Free-text field for any buyer-to-provider communication that doesn't fit structured fields. Examples: "Please ring the doorbell twice," "Avoid green bell peppers," "Call before arriving."

**deliveryPreferences**: Three distinct boolean flags provide granular control:
- `leaveAtDoor`: Override signature/contact requirement
- `contactless`: Explicitly request no-contact delivery (e.g., during health crises)
- `preferredTimeSlot`: Time-bounded delivery window for convenience or availability

**gift**: Separates transactional purchase (invoicePreferences) from gift context:
- `isGift`: Enables gift-specific handling
- `message`: Personalized text (max length implementation-dependent)
- `wrap`: Boolean for premium presentation

**invoicePreferences**: Supports both B2C and B2B transactions:
- `taxId`: Multi-jurisdiction support via scheme and country codes
- `companyName`: For B2B invoicing
- `email`: PDF invoice delivery destination

**loyalty**: Enables rewards redemption at transaction time:
- `programId`: Links to buyer's loyalty account
- `pointsRedeemed`: Credits points used for discounts/benefits

**source**: Supports marketing analytics and attribution modeling:
- `channel`: Acquisition source (organic, paid, referral, etc.)
- `campaignId`: Campaign-specific tracking for A/B testing and ROI

## Non-Goals

This schema does **not**:
- Define loyalty program mechanics (point structure, tiers, rules)
- Enforce tax compliance or validation (that's provider/market responsibility)
- Specify delivery success metrics or outcomes (see Fulfillment)
- Model buyer communication preferences (see a separate PreferencesAttributes schema)
- Encode pricing or surcharge logic
- Define payment method preferences (see PaymentAttributes)

## Upstream Candidates

Future enhancement opportunities for v2.2+:

1. **Communication Preferences**: `communicationChannel` (sms, email, whatsapp) for delivery updates
2. **Sustainability Preferences**: `ecoPreferences: {minimalPackaging: boolean, sustainableMaterials: boolean}`
3. **Insurance Election**: `insuranceOptIn: boolean, insuranceType: enum`
4. **Subscription Metadata**: `isSubscription: boolean, subscriptionId: string, nextBillingDate: date-time`
5. **Recurring Instructions**: `recurringInstructions: {frequency: string, startDate: date-time, endDate: date-time}`
6. **Recipient Details**: `recipientName: string, recipientPhone: string` for sender ≠ receiver
7. **Dietary/Allergy Info**: `dietaryRequirements: array, allergies: array` (F&B specific but generalizable)
8. **Regional Tax Handling**: `reverseChargeApplicable: boolean, placeOfSupply: string`
9. **Payment Terms**: `paymentTerms: {creditDays: number, dueDate: date}` for B2B
10. **Notification Preferences**: `notificationSettings: {email: boolean, sms: boolean, push: boolean}`

## Beckn Integration Notes

- **Core Schema References**: Uses $ref to PriceSpecification from `../../v2.0.0/beckn.yaml`
- **Context/Vocab**: Imports generalized context and vocab from Beckn core v2
- **Protocol Version**: Targets Beckn protocol v2.0 with v2.1 semantic extensions
- **Backward Compatibility**: Direct 1:1 mapping from v2 orderAttributes enables straightforward migration

## Version History

- **v2.1.0** (Current) – Initial release; direct migration from RetailCoreOrderAttributes with no structural changes
- **v2.0.0** – Original orderAttributes in Beckn retail spec (deprecated in favor of contractAttributes naming)
