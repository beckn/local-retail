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


## Design Rationale


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
