# LIGHT Internal Request
## TR-06 — Purchase Integration

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

## Purpose

This document defines the LIGHT integration boundary with standard Odoo
Purchase.

## Standard Odoo Execution

Actual procurement shall use:

- `purchase.order`
- `purchase.order.line`

LIGHT shall not create a custom Purchase Order engine.

## Purchase Allocation

Purchase Allocation is a LIGHT planning concept.

It is not:

- RFQ
- Purchase Order
- Purchase Order Line
- final commercial commitment

## Sourcing

Sourcing may occur before final Financial Approval where vendor quotations are
required to establish commercial value.

RFQ creation itself is not purchase commitment.

## Multiple RFQs

Example:

Purchase Allocation = 10

Vendor A RFQ = 10
Vendor B RFQ = 10
Vendor C RFQ = 10

The effective authorized requirement remains 10.

Sourcing candidate quantity must not multiply the Purchase Allocation.

## Purchase Attribution

Authoritative relation:

IR Line
↔ Purchase Allocation
↔ Purchase Attribution
↔ `purchase.order.line`

Canonical model:

`light.ir.purchase.attribution`

`purchase.order.origin` is informational only.

## One IR to Multiple POs

Supported.

Example:

IR Purchase Qty = 10

PO Vendor A = 6
PO Vendor B = 4

## Multiple IRs to One PO Line

Supported where compatible.

Example:

PO Line Qty = 10

- IR001 Qty 3
- IR002 Qty 2
- IR003 Qty 5

Attribution must remain explicit and quantity-aware.

## Compatibility

Consolidation shall validate applicable dimensions such as:

- Company
- Vendor
- Product
- compatible UoM
- commercial context
- applicable destination/context

Exact rules may be refined during implementation without weakening governance.

## Financial Approval Timing

Supported business pattern:

Need Approved
→ Purchase Allocation
→ RFQ / Sourcing
→ Vendor selection / commercial value known
→ Financial Approval
→ PO Confirmation

Where pre-authorization exists, final PO commitment must still be rechecked.

## PO Confirmation Guard

LIGHT-specific validation shall apply only to LIGHT-linked PO.

Conceptually:

PO confirm
→ LIGHT attribution exists?
  → no: standard Odoo
  → yes: validate LIGHT authority
        → pass: standard confirmation
        → fail: block

## PO Commitment Authority

The system must evaluate actual applicable PO commitment and prevent approval
splitting.

Example:

IR A = 30m
IR B = 30m
IR C = 20m

Merged PO = 80m

If 80m requires higher authority, that authority must be satisfied before
commitment.

## Material Commercial Change

Material commercial changes to LIGHT-linked procurement shall trigger applicable
authorization re-evaluation.

Examples may include:

- quantity
- unit price
- discount
- taxes
- currency
- vendor/commercial basis

Harmless changes shall not create unnecessary reapproval.

## Cancellation

PO cancellation shall release effective commitment as appropriate but shall not
automatically delete the underlying LIGHT Purchase Allocation.

## Receipt

Receipt attribution must remain deterministic for consolidated IR requirements.

Receipt/delivery is separate from Business Acceptance.

## Service Purchase

Service procurement shall work without requiring physical Stock Move
fulfillment.

---

## TR-PUR-001 — Reuse Standard Odoo Purchase

LIGHT shall reuse standard `purchase.order` and `purchase.order.line`.

## TR-PUR-002 — Purchase Allocation Separate From Execution

Purchase Allocation shall remain separate from RFQ/PO execution records.

## TR-PUR-003 — Structured Purchase Attribution

Explicit structured Purchase Attribution shall be the authoritative
relationship between LIGHT and PO Lines.

## TR-PUR-004 — origin Is Informational

`purchase.order.origin` shall not be the authoritative source of LIGHT
traceability.

## TR-PUR-005 — One IR to Multiple Purchase Lines

One IR requirement shall be able to map to multiple PO Lines/Orders where
required.

## TR-PUR-006 — Multiple IRs to One PO Line

One PO Line shall be able to carry explicit attribution to multiple compatible
IR requirements.

## TR-PUR-007 — Attribution Quantity Integrity

Effective awarded/committed Purchase Attribution shall remain within the
applicable authorized Purchase Allocation.

## TR-PUR-008 — Sourcing Does Not Multiply Requirement

Multiple sourcing quotations shall not multiply or overconsume the authorized
Purchase Allocation.

## TR-PUR-009 — Award Distinct From Quotation

Vendor sourcing candidates and awarded/effective procurement quantity shall be
distinguishable.

## TR-PUR-010 — LIGHT PO Commitment Guard

A LIGHT-linked PO shall validate applicable financial authority before
commercial commitment.

## TR-PUR-011 — Consolidated Commitment Evaluation

Applicable PO commitment authority shall consider the actual relevant
consolidated commercial commitment and shall prevent threshold circumvention.

## TR-PUR-012 — Preserve Non-LIGHT Purchase Behavior

Purchase transactions without applicable LIGHT relationship shall retain
standard Odoo Purchase behavior.

---

## Currency

Use standard Odoo currency conversion APIs.

Threshold comparison shall use the applicable configured Company-currency
evaluation basis.

## Tax

Use standard Odoo tax/commercial computation semantics.

Do not implement independent ad-hoc tax arithmetic for authorization where
standard Odoo values are available.

## Purchase Integration Baseline Statement

`TR-PUR-001` through `TR-PUR-012` constitute the V1 Purchase Integration
baseline.
