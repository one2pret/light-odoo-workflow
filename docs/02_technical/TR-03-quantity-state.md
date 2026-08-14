# LIGHT Internal Request
## TR-03 — Quantity and State Engine

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

## Purpose

This document defines canonical V1 quantity semantics, invariants, and primary
state principles.

## Canonical Quantities

Use the following canonical quantity concepts:

- `requested_qty`
- `approved_qty`
- `internal_allocated_qty`
- `purchase_allocated_qty`
- `allocated_qty`
- `committed_qty`
- `delivered_qty`
- `accepted_qty`
- `cancelled_qty`
- `outstanding_qty`

The term `fulfilled_qty` shall not be introduced as an authoritative canonical
business quantity because it is ambiguous.

## Requested Quantity

`requested_qty`

Quantity requested by the Requester.

## Approved Quantity

`approved_qty`

Quantity authorized by Need Approval.

Invariant:

`0 <= approved_qty <= requested_qty`

The difference between Requested and Approved Quantity is not automatically
Cancelled Quantity.

## Allocation

`internal_allocated_qty`

Quantity planned for Internal fulfillment.

`purchase_allocated_qty`

Quantity planned for Purchase fulfillment.

Where both apply:

`allocated_qty = internal_allocated_qty + purchase_allocated_qty`

subject to applicable allocation validity.

Allocation is planning, not execution.

## Committed Quantity

`committed_qty`

Quantity represented by applicable committed downstream execution.

RFQ sourcing quantity alone shall not become Committed Quantity.

## Delivered Quantity

`delivered_qty`

Quantity operationally delivered/executed and attributed to the IR.

Delivered does not mean Accepted.

## Accepted Quantity

`accepted_qty`

Quantity represented by valid Acceptance events net of applicable Reversal
effects.

Accepted Quantity shall not be an unrestricted editable business total.

## Cancelled Quantity

`cancelled_qty`

Quantity formally cancelled through authorized Cancellation.

It is distinct from:

- not-approved quantity;
- rejected quantity;
- exception quantity;
- sourcing quantity.

## Outstanding Quantity

Canonical V1 formula:

`outstanding_qty = approved_qty - accepted_qty - cancelled_qty`

This is the authoritative remaining business-outcome quantity.

## Primary State

Canonical primary IR states:

- `draft`
- `submitted`
- `in_process`
- `done`
- `cancelled`

Detailed subprocess progress shall use dedicated status indicators.

Examples:

- approval status;
- review status;
- purchase status;
- stock status;
- acceptance status;
- exception status;
- completion outcome.

---

## TR-QTY-001 — Server-Side Quantity Invariants

Critical quantity invariants shall be enforced server-side.

## TR-QTY-002 — Requested and Approved Separate

Requested Quantity and Approved Quantity shall remain separate concepts and
shall not overwrite each other.

## TR-QTY-003 — Not Approved Is Not Cancellation

The difference between Requested and Approved Quantity shall not be represented
as Cancelled Quantity unless a separate authorized Cancellation actually
occurs.

## TR-QTY-004 — Allocation Separate From Execution

Fulfillment Allocation shall remain separate from Purchase/Stock execution
quantity.

## TR-QTY-005 — Delivered Separate From Accepted

Delivered Quantity and Accepted Quantity shall remain separate concepts.

## TR-QTY-006 — Outstanding Uses Accepted and Cancelled

Canonical Outstanding Quantity shall be calculated using:

`approved_qty - accepted_qty - cancelled_qty`

not merely delivered or ordered quantity.

## TR-QTY-007 — Aggregate Quantities From Authoritative Records

Where an aggregate quantity represents historical events or attributions, it
should be derived from or reconciled against authoritative structured records.

## TR-QTY-008 — Partial Execution Supported

Quantity logic shall support partial Purchase, partial Stock execution,
backorder, partial Acceptance, partial Cancellation, and Split Fulfillment.

## TR-QTY-009 — Standard Odoo UoM Semantics

Quantity conversion and comparison shall use standard Odoo UoM semantics and
appropriate float precision/comparison mechanisms.

LIGHT shall not create its own UoM conversion engine.

## TR-QTY-010 — Compact Primary State

The primary IR state shall remain compact.

Subprocess complexity shall not be encoded by continuously adding new primary
states.

---

## Core Quantity Invariants

At minimum:

`requested_qty >= 0`

`approved_qty >= 0`

`approved_qty <= requested_qty`

`internal_allocated_qty >= 0`

`purchase_allocated_qty >= 0`

`allocated_qty >= 0`

`accepted_qty >= 0`

`cancelled_qty >= 0`

`accepted_qty + cancelled_qty <= approved_qty`

and applicable effective allocation shall not exceed the remaining authorized
quantity according to the active business context.

## Split Example

Approved Qty = 10

Internal Allocation = 4

Purchase Allocation = 6

Total Allocated = 10

This is valid.

Internal Allocation = 6

Purchase Allocation = 6

Total Allocated = 12

against Approved Qty 10 is invalid unless a prior authorized Revision changes
the effective Approved Quantity.

## Approval Reduction Example

Requested Qty = 10

Approved Qty = 8

Not Approved = 2

Cancelled Qty = 0

Outstanding initially = 8

The two units not approved shall not become Cancellation.

## Acceptance Example

Approved Qty = 10

Delivered Qty = 10

Accepted Qty = 8

Cancelled Qty = 0

Outstanding Qty = 2

The IR Line is not complete solely because Delivered Qty reached 10.

## Cancellation Example

Approved Qty = 10

Accepted Qty = 6

Cancelled Qty = 4

Outstanding Qty = 0

The quantity condition for completion may be satisfied, subject to all other
blocking workflow conditions.

---

## State Transition Principle

Users shall not freely edit the primary state.

State transitions shall occur through authorized server-side business actions.

A previously terminal IR shall not have a generic unrestricted Reopen action.

---

## Quantity and State Baseline Statement

`TR-QTY-001` through `TR-QTY-010` constitute the V1 quantity/state baseline.

Any proposed new authoritative quantity concept or major state-model change
requires explicit baseline review and, where architectural, approved ADR.
