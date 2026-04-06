# Retail Core Settlement Attributes (v2.1)

## Overview

The **RetailSettlement** schema defines the structure for retail settlement discharge records in the Beckn protocol v2.1. This schema is intentionally minimal because retail settlements are typically handled by payment gateways, allowing for a clean and efficient representation of settlement transactions without duplicating payment processor logic.


## Usage Examples


## Design Rationale

This schema is intentionally minimal because:

1. **Payment Gateway Abstraction**: Most retail payment processing is handled by dedicated payment gateways (Razorpay, PayPal, Stripe, etc.), which maintain their own comprehensive settlement records.

2. **Separation of Concerns**: Settlement discharge is a distinct operation from consideration (pricing) and payment processing, allowing for clean architectural boundaries.

3. **Retail Focus**: Retail transactions typically have straightforward 1-to-1 settlement (single payment, single merchant), unlike complex B2B scenarios with multiple parties and split payments.

4. **Gateway Integration**: The `gateway` object allows referencing the authoritative payment processor record without duplicating their settlement logic.
