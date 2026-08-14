# LIGHT Internal Request
## TR-08 — Acceptance and Fulfillment Exception

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

## Purpose

This document defines Business Acceptance, Acceptance Reversal, Fulfillment
Exception, and replacement/redelivery behavior.

## Acceptance Boundary

Operational delivery and Business Acceptance are separate events.

Examples:

Purchase Receipt Done
→ Delivered

Internal Transfer Done
→ Delivered

Service Fulfillment Confirmed
→ Delivered

These events do not automatically mean Accepted unless the applicable policy
uses Automatic Acceptance.

## Acceptance Modes

V1 supports:

- `manual`
- `automatic`
- `not_required`

Default:

`manual`

## Acceptance Event

Acceptance shall use structured historical events.

Canonical model:

`light.ir.acceptance.event`

An event shall preserve applicable context such as:

- IR
- IR Line
- quantity
- event type
- actor
- timestamp
- source fulfillment
- reason where applicable
- Company

## Acceptance Event Types

V1 shall support concepts equivalent to:

- Acceptance
- Reversal

Aggregate Accepted Quantity shall be derived from effective event quantity.

Example:

Acceptance +10
Reversal -2

Effective Accepted Qty = 8

## Partial Acceptance

Partial Acceptance is supported.

Example:

Delivered Qty = 10
Accepted Qty = 8
Outstanding Qty = 2

The remaining quantity may require further Acceptance, Exception handling, or
Cancellation.

## Automatic Acceptance

Automatic Acceptance shall create a structured Acceptance Event.

It shall not merely write an aggregate accepted quantity.

Automatic processing shall be idempotent.

## Not Required

If Acceptance policy is `not_required`, completion logic shall use the
explicit policy semantics defined by implementation.

The implementation shall not silently pretend that a human Acceptance event
occurred.

## Receiver

Receiver/Acceptor authority shall be determined according to applicable
business policy.

Requester may be the default Receiver but Requester and Receiver are not
identical concepts.

## Fulfillment Exception

Canonical model:

`light.ir.fulfillment.exception`

Exception examples:

- damaged
- wrong_product
- wrong_specification
- shortage
- quality_issue
- service_incomplete
- other

Exception shall preserve:

- affected requirement
- affected fulfillment/source
- affected quantity where applicable
- reason/type
- actor
- timestamp
- status
- resolution

## Exception Is Not Cancellation

Exception quantity shall not automatically increase Cancelled Quantity.

## Resolution

Supported resolution concepts include:

- replacement
- return_and_replace
- redelivery
- accept_as_is
- cancel_remaining
- other

## Replacement

Replacement addresses the original authorized requirement.

It shall not create a second authorized business need.

Example:

Approved = 10
Delivered = 10
Exception = 2
Replacement Delivery = 2

This does not mean Approved Quantity becomes 12.

## Stock Return

Where physical stock return is required, standard Odoo Inventory return
mechanisms shall remain authoritative.

## Service Exception

Service Exception shall not require artificial Stock Moves.

---

## TR-ACC-001 — Delivery and Acceptance Separate

Delivered Quantity and Accepted Quantity shall remain separate authoritative
business concepts.

## TR-ACC-002 — Structured Acceptance Events

Acceptance shall be represented by structured historical events.

## TR-ACC-003 — Partial Acceptance Supported

Acceptance processing shall support partial quantities.

## TR-ACC-004 — Acceptance Quantity Integrity

Effective Accepted Quantity shall not exceed the applicable quantity eligible
for Acceptance.

## TR-ACC-005 — Automatic Acceptance Is Idempotent

Automatic Acceptance shall not duplicate effective Accepted Quantity when the
same source event is processed more than once.

## TR-ACC-006 — Reversal Preserves Original Event

Acceptance correction shall use a Reversal event and shall not destructively
rewrite finalized historical Acceptance.

## TR-ACC-007 — Exception Separate From Cancellation

Fulfillment Exception shall remain distinct from Cancellation.

## TR-ACC-008 — Replacement Does Not Double Count

Replacement or Redelivery shall not increase the original authorized
requirement merely because fulfillment is being corrected.

## TR-ACC-009 — Standard Odoo Return Execution

Physical Purchase/Inventory return execution shall reuse applicable standard
Odoo mechanisms.

## TR-ACC-010 — Server-Side Acceptance Authority

Manual Acceptance shall validate applicable Receiver/Acceptor authority
server-side.

## TR-ACC-011 — Blocking Exception Prevents Completion

An unresolved blocking Fulfillment Exception shall prevent applicable business
completion.

## TR-ACC-012 — Service Acceptance Supported

Acceptance and Exception handling shall support Service fulfillment without
requiring physical Inventory execution.

---

## Idempotency Key Principle

Where an automatic Acceptance is generated from a downstream execution event,
the implementation shall maintain sufficient source identity to ensure the same
business effect cannot be generated twice.

## Historical Integrity

Finalized Acceptance and Exception history shall not be directly rewritten by
ordinary operational editing.

Corrections shall use controlled events or resolution actions.

## Acceptance/Exception Baseline Statement

`TR-ACC-001` through `TR-ACC-012` constitute the V1 Acceptance and Fulfillment
Exception baseline.
