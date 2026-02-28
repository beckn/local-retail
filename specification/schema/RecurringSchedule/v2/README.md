# RecurringSchedule (v2)

## Overview

`RecurringSchedule` defines reusable recurring temporal constraints for Retail schemas in Beckn Protocol v2.

It models day-based recurrence with time windows and optional exclusions, enabling consistent representation of:

- Store operating hours
- Offer serviceability timing windows
- Pickup or delivery acceptance windows
- Time-bound retail participation rules

This schema is referenced by multiple RetailCore schemas to avoid duplication and ensure semantic consistency.

---

## Attachment Model

`RecurringSchedule` is **not directly attached** to a Beckn core container object.

Instead, it is referenced by:

- `RetailCoreOfferAttributes`
- `RetailCoreFulfillmentAttributes`

Example usage:

- `RetailCoreOffer.serviceability.timing`
- `RetailCoreFulfillment.operatingHours`

---

## Why This Schema Exists

Beckn Core defines `TimePeriod`, which models absolute date-time validity windows.

Retail domains require recurring temporal constraints such as:

- "Mon–Fri, 9:00 AM – 6:00 PM"
- "Delivery only between 10 AM and 4 PM"
- "Closed on public holidays"

These cannot be fully represented using absolute `TimePeriod` constructs.

`RecurringSchedule` provides a reusable abstraction for recurring retail time rules without expanding Beckn Core prematurely.

---

## Structure

The schema models:

- `days` — Applicable days of week
- `timeRange` — Daily time window
- `holidays` — Explicit exclusion dates
- `timezone` — Optional IANA timezone identifier

This structure supports predictable indexing and UI rendering while remaining simple and extensible.

---

## Future Evolution

If recurring scheduling requirements become common across multiple Beckn domains (e.g., mobility peak windows, EV charging tariff windows, education class schedules), this schema may be proposed for upstream inclusion in Beckn Core.

Until then, it remains a Retail v2 reusable schema.

---

## Version

- Schema Family: Retail
- Protocol Alignment: Beckn v2
- Version: v2