# Phase 0.1 — Define the Booking System

## 1. Problem Statement

The system is an Airbnb-like accommodation booking platform where:

* Hosts can create and manage listings.
* Guests can search for available listings.
* Guests can temporarily hold dates while completing payment.
* Successful payment results in a confirmed booking.
* The system must prevent double booking.
* Hosts and guests can cancel eligible future bookings.
* Availability is determined by actual business commitments rather than an independently managed status.

The primary engineering challenge of the system is maintaining **correct inventory and booking state under concurrency, failures, retries, and partial system failures**.

---

# 2. Initial System Scope

The initial version supports:

* User authentication and authorization.
* Hosts creating and managing accommodation listings.
* Listing lifecycle management.
* Guest search using location, dates, and guest count.
* Availability checking.
* Temporary booking holds.
* Payment flow integration.
* Booking confirmation.
* Booking cancellation before check-in.
* Host date blocking.
* Basic booking history.
* Failure recovery and reconciliation principles.

The system will intentionally evolve into more advanced backend problems rather than introducing advanced infrastructure prematurely.

---

# 3. Actors

## 3.1 Guest

A guest can:

* Search/browse active listings.
* View listing details.
* Check availability.
* Initiate a booking attempt.
* Acquire a temporary hold.
* Complete payment.
* View their own holds and bookings.
* Resume an active hold/payment attempt.
* Cancel an eligible confirmed booking.

---

## 3.2 Host

A host can:

* Create a listing as `DRAFT`.
* Update listing details.
* Activate a listing when required information is complete.
* Deactivate a listing.
* Block specific date ranges.
* View confirmed bookings for listings they own.
* Cancel eligible bookings for listings they own.

A host:

* Cannot create a conflicting date block over an existing confirmed booking.
* Cannot create a conflicting date block over an active valid hold.

---

## 3.3 User Role Principle

A user is not permanently restricted to only one business role.

A user may:

* Host their own listings.
* Book another user's listing as a guest.

However:

> A user cannot book a listing they own.

---

## 3.4 Admin

No dedicated admin workflow is required for the initial version.

Specifically, the following is currently out of scope:

* Listing approval workflows.
* Host approval workflows.
* Marketplace moderation.

Admin functionality can be introduced later when a real business problem requires it.

---

# 4. Listing Requirements

## 4.1 Listing Lifecycle

Initial listing lifecycle:

```text
DRAFT → ACTIVE → INACTIVE
```

### DRAFT

A newly created listing starts as `DRAFT`.

A draft listing:

* Cannot receive booking attempts.
* Cannot appear as a bookable listing.

### ACTIVE

A listing can become `ACTIVE` only when all required booking-relevant information is present and valid.

An active listing:

* Can appear in search.
* Can accept new booking attempts.

### INACTIVE

An inactive listing:

* Cannot accept new booking attempts.
* Does not invalidate existing confirmed bookings.
* Does not invalidate existing valid holds.

Deactivation affects future operations, not already established commitments.

---

## 4.2 Listing Activation Requirements

Initially, a listing requires:

* Title
* Description
* Location
* Nightly price
* Maximum guest capacity

Exact schema and validation details will be decided during Domain Modeling.

---

## 4.3 Listing Changes

Changes to listing configuration affect future booking attempts only.

Existing confirmed bookings remain valid.

Examples:

* Price changes do not change existing booking prices.
* Capacity changes do not invalidate existing bookings.
* Listing deactivation does not cancel existing bookings.

Core principle:

> Changes to listing configuration affect future booking attempts, not existing confirmed commitments.

---

## 4.4 Listing Deletion

Listings must not be hard-deleted through normal business operations.

Hosts should use lifecycle state changes instead.

This preserves:

* Historical booking context.
* Business relationships.
* Auditability.

An `ARCHIVED` state may be introduced later if a real business distinction emerges.

---

# 5. Listing Search

Guests can initially search using:

* Location
* Check-in date
* Check-out date
* Guest count

Search results should return listings that:

* Are `ACTIVE`.
* Have sufficient guest capacity.
* Appear available for the requested date range.

Important:

> Search availability is informational and point-in-time only.

A successful search result does not reserve inventory.

Availability must always be revalidated when attempting to acquire a booking hold.

---

# 6. Booking Requirements

## 6.1 Booking Request

A booking request includes:

* Listing
* Check-in date
* Check-out date
* Guest count

The backend determines authoritative business values such as:

* Listing state.
* Availability.
* Capacity validity.
* Pricing.
* Authorization.
* Booking outcome.

The client expresses intent; the backend validates reality.

---

## 6.2 Stay Duration

A booking must contain at least one night.

```text
checkIn < checkOut
```

The system initially does not support:

* Per-listing minimum stay.
* Per-listing maximum stay.
* Global maximum stay duration.

---

## 6.3 Date Semantics

Booking dates follow:

```text
[checkIn, checkOut)
```

Meaning:

* Check-in date is occupied.
* Check-out date is not occupied.

Example:

```text
Booking A: Dec 10 → Dec 15
Booking B: Dec 15 → Dec 20

No overlap.
```

---

## 6.4 Past and Same-Day Bookings

* Past check-in dates are not allowed.
* Same-day booking is allowed.
* Future booking is allowed.
* No maximum advance booking window initially.

---

## 6.5 Guest Count

A booking request must specify a guest count.

Rules:

```text
guestCount >= 1

guestCount <= listing.maxGuestCapacity
```

Initially, the system does not distinguish:

* Adults.
* Children.
* Infants.
* Pets.

---

# 7. Booking Lifecycle

Initial lifecycle:

```text
HOLD
  │
  ├── Payment fails
  │       ↓
  │   Hold resolved
  │   Inventory released
  │
  ├── Hold expires
  │       ↓
  │   Inventory released
  │
  └── Payment succeeds
          ↓
      CONFIRMED
          │
          ├── Cancelled before check-in
          │       ↓
          │   CANCELLED
          │
          └── Stay reaches checkout
                  ↓
              COMPLETED
```

Initial booking states:

* `HOLD`
* `CONFIRMED`
* `CANCELLED`
* `COMPLETED`

The exact representation of intermediate payment states will be determined later.

---

# 8. Temporary Hold Requirements

## 8.1 Hold Duration

A successful booking attempt creates a temporary hold.

Initial hold duration:

> 10 minutes.

The guest must be informed that:

* Dates are temporarily reserved.
* The hold has an expiration time.
* Payment must be completed within the allowed process.

---

## 8.2 Browser Disconnect

Closing the browser or leaving the booking page does not immediately release a hold.

The hold remains valid until:

* It expires.
* Payment resolves.
* Another definitive business event ends it.

Reason:

A hold represents a temporary inventory commitment, not merely a browser session.

---

## 8.3 Hold Recovery

A guest can:

* View their own active hold.
* Resume their payment attempt.

This remains possible until the hold expires or resolves.

Hosts do not see active holds as bookings.

---

## 8.4 Hold Expiration

If payment has not been initiated before hold expiration:

```text
Hold expires
    ↓
Inventory released
    ↓
Guest must check availability again
```

Keeping an old payment page open must not indefinitely extend a hold.

---

## 8.5 Payment Grace

If payment processing was initiated before the original hold expiration:

> The booking may enter a payment grace period.

During this period:

* Inventory must not be silently released.
* The payment outcome must be resolved.

Payment grace is intended to handle legitimate in-progress payment processing.

---

# 9. Booking Confirmation and Immutability

A confirmed booking represents a commercial agreement.

Its core terms are immutable:

* Guest
* Listing
* Check-in date
* Check-out date
* Guest count
* Agreed nightly price
* Total price

Changes must happen through explicit business workflows.

Example:

```text
Change dates
→ Cancel existing booking
→ Create a new booking
```

Core principle:

> A confirmed booking is immutable in its core commercial terms; lifecycle changes happen through explicit state transitions.

---

# 10. Pricing

Initial pricing model:

> Fixed base nightly price.

Calculation:

```text
Total Price = Nightly Price × Number of Nights
```

Example:

```text
5 nights × ₹5,000 = ₹25,000
```

Not initially supported:

* Taxes
* Cleaning fees
* Platform fees
* Discounts
* Coupons
* Promotions

---

## 10.1 Price Preservation

Booking financial terms are preserved at booking time.

Example:

```text
Booking created:
₹5,000/night
5 nights
Total ₹25,000

Host later changes price:
₹7,000/night
```

Existing booking remains:

```text
₹25,000
```

New booking attempts use the updated price.

---

# 11. Booking Visibility

## Guest

Can view:

* Their own active holds.
* Their own confirmed bookings.
* Their own cancelled bookings.
* Their own completed bookings.

## Host

Can view:

* Confirmed bookings for listings they own.
* Cancelled/completed booking history for their listings.

Hosts do not see temporary holds as bookings.

## Other Users

Cannot access unrelated booking information.

## Admin

No specific admin workflow is currently defined.

---

# 12. Cancellation Rules

## 12.1 Cancellation Scope

Initially, cancellation is supported only before check-in.

Out of scope:

* Mid-stay cancellation.
* Early checkout.
* Partial cancellation.
* Cancelling remaining nights.
* Partial stay refunds.

---

## 12.2 Guest Cancellation

A guest can cancel an eligible confirmed booking before check-in.

Result:

```text
CONFIRMED
    ↓
CANCELLED
    ↓
Inventory released
```

---

## 12.3 Host Cancellation

A host can cancel an eligible confirmed booking before check-in.

This is recorded as a host-initiated cancellation.

The guest should receive the applicable refund.

---

## 12.4 Inventory Release

Once cancellation is accepted:

> Inventory is released immediately.

Refund processing must not block availability.

```text
Cancellation accepted
        ↓
Booking CANCELLED
        ↓
Inventory released
        ↓
Refund processing
```

If refund processing fails:

> The booking must not become active again.

---

# 13. Availability Lifecycle

Availability is not treated as an unrelated independent state.

Availability is determined by business conditions.

For a requested date range:

```text
Listing ACTIVE
        +
No host date block
        +
No conflicting active hold
        +
No conflicting confirmed booking
        =
AVAILABLE
```

A date range can become unavailable because of:

* Host block.
* Active hold.
* Confirmed booking.

Availability can return when:

* Hold expires.
* Payment definitively fails.
* Booking is cancelled.

---

# 14. Host Date Blocking

Hosts can block specific date ranges.

However, a host cannot create a date block that conflicts with:

* An existing confirmed booking.
* A valid active hold.

Example:

```text
Guest HOLD:
Dec 10 → Dec 15

Host attempts block:
Dec 12 → Dec 14

Result:
Rejected
```

Once the hold resolves or expires, the host may attempt the block again.

---

# 15. Core Inventory Invariant

This is the most important booking invariant:

> **The system must never allow two active holds or confirmed bookings with overlapping date ranges for the same independently-bookable listing.**

Conflicting example:

```text
Guest A:
Dec 10 → Dec 15

Guest B:
Dec 12 → Dec 17

Result:
Only one conflicting inventory commitment can succeed.
```

Non-conflicting example:

```text
Guest A:
Dec 10 → Dec 15

Guest B:
Dec 15 → Dec 20

Both allowed.
```

This rule applies even if both attempts belong to the same guest.

---

# 16. Same Guest Overlapping Attempts

An active hold blocks overlapping inventory for everyone, including the guest who owns that hold.

Example:

```text
Guest A

Hold:
Dec 10 → Dec 15

New attempt:
Dec 12 → Dec 17

Result:
Rejected
```

The guest must resolve or wait for the existing hold to expire before making a conflicting attempt.

---

# 17. Payment Requirements

## 17.1 Successful Payment

A successful payment does not automatically mean the entire booking lifecycle is complete.

The system must ensure inventory can be fulfilled.

Required eventual outcomes:

```text
Payment succeeds
      │
      ├── Inventory secured
      │       ↓
      │   Booking CONFIRMED
      │
      └── Inventory cannot be fulfilled
              ↓
          Compensation / Refund
```

Core principle:

> A successfully charged guest must never be left with an unfulfillable booking.

---

## 17.2 Unknown Payment Outcome

Timeouts and ambiguous responses must not automatically be treated as payment failures.

Example:

```text
Payment initiated
      ↓
Timeout
      ↓
Outcome unknown
```

The system must:

```text
Reconcile actual payment outcome
      │
      ├── Success → Confirm/resolve booking
      │
      └── Failure → Release inventory
```

Core principle:

> Known payment failure and unknown payment outcome are different states.

---

## 17.3 Duplicate Payment/Booking Requests

Repeated requests representing the same logical operation must not create duplicate business effects.

Example:

```text
Guest clicks Pay twice

Result:
ONE logical booking operation
NOT duplicate holds
NOT duplicate bookings
NOT duplicate charges
```

The system must eventually distinguish:

* Retry of an existing operation.
* A genuinely new operation.

---

# 18. Source of Truth

Confirmed bookings represent authoritative business commitments.

Priority conceptually:

```text
CONFIRMED BOOKING
        ↓
Strongest commitment

ACTIVE HOLD
        ↓
Temporary commitment

SEARCH / DERIVED AVAILABILITY
        ↓
Informational representation
```

Core principle:

> Confirmed bookings are authoritative business commitments; derived availability representations must never override them.

Search or cached availability may become stale.

Critical booking validation must always protect authoritative commitments.

---

# 19. Time and Timezone Rules

## Stay Dates

Booking stay dates follow the listing's local timezone/calendar.

Example:

```text
Listing:
New York

Timezone:
America/New_York

Check-in:
2026-12-10

Check-out:
2026-12-15
```

## System Events

System events use absolute timestamps.

Examples:

* Hold creation.
* Hold expiration.
* Payment timestamps.
* Lifecycle event timestamps.

Core principle:

> Booking stay dates follow the listing's local timezone/calendar, while system events use absolute timestamps.

---

# 20. Historical Data Preservation

Listings must not be hard-deleted through normal operations.

Bookings must not be hard-deleted through normal operations.

Lifecycle changes preserve historical business events.

Examples:

```text
CONFIRMED → CANCELLED
CONFIRMED → COMPLETED
```

History is retained.

The retention strategy for expired/failed holds will be decided later.

---

# 21. Important Failure Scenarios

The system must reason about and eventually handle scenarios such as:

### Server crash after hold creation

```text
Hold written successfully
        ↓
Server crashes
        ↓
Response never reaches client
```

The operation must remain recoverable.

---

### Duplicate client requests

The same logical operation must not create duplicate business effects.

---

### Concurrent booking attempts

Two guests attempting overlapping dates simultaneously must not result in double booking.

---

### Browser/session disconnect

A valid hold remains until expiration or explicit resolution.

---

### Payment timeout

Unknown payment outcome must be reconciled rather than assumed to be failed.

---

### Payment succeeds but booking cannot be fulfilled

The system must eventually confirm or compensate/refund.

---

### Notification failure

A successfully confirmed booking remains confirmed even if notification delivery fails.

Notification delivery should be retried independently.

---

### Refund failure

A cancelled booking remains cancelled.

Refund failure must not reactivate inventory commitments.

---

# 22. Notification Principle

Notifications are secondary side effects.

Example:

```text
Booking CONFIRMED
        ↓
Notification fails
```

Result:

```text
Booking remains CONFIRMED
Notification can be retried
```

Core principle:

> Failure of a secondary side effect must not invalidate a successful core business operation.

---

# 23. Security and Authorization Requirements

The backend is authoritative for:

* Authentication.
* Authorization.
* Ownership validation.

Examples:

```text
Guest A
→ Cannot view Guest B's booking

Guest A
→ Cannot cancel Guest B's booking

Host A
→ Cannot modify Host B's listing

Host A
→ Cannot access unauthorized booking information
```

Client-side UI restrictions are not considered security boundaries.

Core principle:

> Backend authorization and ownership validation are mandatory; client-side checks are never sufficient.

---

# 24. Input Validation and Trust Boundaries

All external input must be validated.

The backend must not trust client-provided authoritative business values.

Example:

Client sends:

```text
listingId
dates
guestCount
totalPrice
```

The backend determines:

```text
Actual listing state
Actual availability
Actual capacity validity
Actual price
Authorization
Final booking result
```

Core principle:

> The client expresses intent; the backend validates and determines authoritative business reality.

---

# 25. Initial Non-Functional Requirements

## 25.1 Correctness over Raw Latency

For critical booking operations:

> Correctness and consistency take priority over minimizing latency.

Especially for:

* Inventory holds.
* Booking confirmation.
* Concurrent booking operations.
* Inventory release.
* Payment reconciliation.

---

## 25.2 Recoverability

Critical business operations must be recoverable and reconcilable after:

* Server crashes.
* Process restarts.
* Network failures.
* Lost responses.
* External provider failures.

The system should eventually be able to determine:

> What actually happened?

---

## 25.3 Observability

Critical booking and payment workflows must be traceable.

The system should eventually support investigation of:

* Which request occurred?
* Which user initiated it?
* Which booking was affected?
* Which lifecycle transition occurred?
* When did it happen?
* What failed?
* Was the operation retried?

Implementation mechanisms will be introduced later.

---

## 25.4 Backend-Enforced Security

Authorization and ownership must be enforced by the backend.

The frontend cannot be trusted as a security boundary.

---

## 25.5 Defense in Depth

Critical business invariants should be protected as close to the source of truth as practical.

Conceptually:

```text
Client validation
        ↓
Input validation
        ↓
Business rule validation
        ↓
Data-layer / atomic protection
```

Not every rule requires multiple layers.

But critical invariants—especially double-booking prevention—deserve stronger protection.

---

# 26. Core Domain Invariants

The following are non-negotiable guarantees.

## 1. No Double Booking

> Two active holds or confirmed bookings must never overlap for the same independently-bookable listing.

## 2. Payment Consistency

> A successful payment must never be silently lost or leave the guest with an unresolved unfulfillable booking.

## 3. Recoverability

> Critical operations must be recoverable and reconcilable after failures.

## 4. Authorization

> Users must only access or modify resources they are authorized to interact with.

## 5. Cancellation Releases Inventory

> Once cancellation is accepted, inventory is released immediately and refund processing is independent.

## 6. Historical Preservation

> Bookings and listings are not hard-deleted through normal business operations.

---

# 27. Explicitly Out of Scope

The following are intentionally excluded from the initial version.

## Listing

* Admin listing approval.
* Marketplace moderation.
* Listing archival distinction.
* Photos.
* Amenities.
* Check-in instructions.
* House rules.

## Search

* Price filtering.
* Amenities filtering.
* Maps.
* Ranking algorithms.
* Personalization.

## Booking

* Booking modification.
* Changing dates after confirmation.
* Mid-stay cancellation.
* Early checkout.
* Partial cancellation.
* Partial stays.

## Stay Rules

* Per-listing minimum stay.
* Per-listing maximum stay.
* Maximum advance booking window.
* Minimum advance notice.
* Check-in cutoff rules.

## Guest Types

* Adults vs children.
* Infants.
* Pets.

## Pricing

* Taxes.
* Cleaning fees.
* Platform fees.
* Discounts.
* Coupons.
* Promotions.
* Dynamic/date-specific pricing.

## Advanced Operations

* Reviews.
* Guest-host messaging.
* Dispute resolution.
* Fraud detection.
* Marketplace moderation.

These features may be introduced later when they create meaningful backend engineering problems.

---

# 28. Phase 0.1 Final Takeaway

The booking system is fundamentally an **inventory consistency system**.

At a simple level:

```text
Guest searches
      ↓
Availability appears open
      ↓
Guest acquires temporary hold
      ↓
Payment
      ↓
Booking becomes confirmed
```

But the real engineering complexity begins when reality intervenes:

```text
Concurrent guests
Duplicate requests
Server crashes
Payment timeouts
Unknown outcomes
Refund failures
Notification failures
Stale availability
```

The central principle guiding the future system design is:

> **Business commitments must remain correct even when requests are concurrent, clients retry, servers fail, and external systems behave unpredictably.**

We now have enough business understanding to begin **Phase 0.2 — Domain Modeling**.
