# LIGHT Internal Request
## TR-07 — Stock / Internal Transfer Integration

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

## Purpose

This document defines LIGHT integration with standard Odoo Inventory for
internal stock fulfillment.

## Standard Odoo Inventory

Actual inventory execution shall use:

- `stock.picking`
- `stock.move`
- `stock.move.line`

LIGHT shall not create a parallel stock movement engine.

## Internal Allocation

Internal Allocation is a LIGHT planning concept.

It is not a Stock Move.

## Eligibility

Before creating executable internal stock fulfillment, the applicable
requirement shall have sufficient valid context, including:

- Company
- Product
- UoM
- Internal Allocation
- applicable source/destination context
- required governance completion

## Goods vs Service

Physical Internal Transfer applies to eligible Goods.

Service shall not be forced through physical Stock Move execution.

## Same Company V1

V1 internal fulfillment shall remain within the same Company as the IR.

Cross-company fulfillment is outside V1.

## Stock Attribution

Canonical model:

`light.ir.stock.attribution`

Authoritative execution relation is at `stock.move` level.

`stock.picking` remains the operational container/navigation document.

## One IR to Multiple Transfers

Supported.

Example:

IR Internal Qty = 10

Transfer A = 6
Transfer B = 4

## Multiple IRs to Consolidated Transfer

Supported where operational context is compatible.

Example:

Stock Move Qty = 8

- IR001 Qty 5
- IR002 Qty 3

Attribution must remain explicit.

## Partial Execution

Partial transfer shall increase Delivered Quantity only by the quantity
actually completed and attributed.

A planned/confirmed quantity is not automatically Delivered Quantity.

## Backorder

Standard Odoo backorder behavior shall remain authoritative.

LIGHT attribution shall follow the resulting execution so remaining IR
quantity remains traceable.

## Deterministic Partial Attribution

Partial completion of a consolidated move shall not use arbitrary record order.

Final IR attribution must be deterministic and valid for the applicable UoM
precision.

System-assisted allocation may be used, but final attribution must remain
explicit and valid.

## Lot and Serial

Standard Odoo lot/serial tracking remains authoritative.

LIGHT shall preserve navigation/traceability to the relevant stock execution.

## Transfer Cancellation

Cancelling a stock execution releases applicable execution commitment but does
not automatically delete the underlying Internal Allocation.

## Reallocation

Where execution has not been realized, an authorized business process may
replan remaining quantity.

Realized stock history shall not be erased by later reallocation.

---

## TR-STK-001 — Reuse Standard Odoo Inventory

LIGHT shall reuse standard Odoo Inventory models for physical stock execution.

## TR-STK-002 — Internal Allocation Separate From Stock Move

Internal Allocation shall remain separate from Stock Move execution.

## TR-STK-003 — Explicit Stock Attribution

LIGHT shall maintain structured quantity-aware Stock Attribution between IR
allocation and actual stock execution.

## TR-STK-004 — Attribution Quantity Integrity

Effective Stock Attribution shall remain within the applicable authorized
Internal Allocation.

## TR-STK-005 — Partial Done Drives Delivery

Delivered Quantity from internal fulfillment shall reflect the attributable
quantity actually completed by standard Odoo stock execution.

## TR-STK-006 — Backorder Preserves Attribution

Backorder processing shall preserve the remaining applicable IR attribution
rather than incorrectly completing the source requirement.

## TR-STK-007 — Deterministic Consolidated Attribution

Partial completion of consolidated stock execution shall use deterministic,
UoM-valid attribution.

## TR-STK-008 — Standard Lot/Serial Authority

Lot and Serial execution shall remain authoritative in standard Odoo Inventory.

## TR-STK-009 — Cross-Company Internal Fulfillment Block

V1 shall reject cross-company Internal Fulfillment relationships.

## TR-STK-010 — Preserve Non-LIGHT Inventory Behavior

Inventory transactions without applicable LIGHT relationship shall retain
standard Odoo behavior.

---

## Acceptance Boundary

Stock completion and Business Acceptance remain separate.

Where Acceptance is Manual:

Stock Done
→ Delivered Quantity
→ Pending Acceptance

Where Acceptance is Automatic:

Stock Done
→ structured Automatic Acceptance Event

## Stock Integration Baseline Statement

`TR-STK-001` through `TR-STK-010` constitute the V1 Stock Integration
baseline.
