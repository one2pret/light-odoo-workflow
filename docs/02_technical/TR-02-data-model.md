# LIGHT Internal Request
## TR-02 — Data Model

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

## Purpose

This document defines the logical V1 data model boundaries.

Exact field implementation may be refined during milestone implementation as
long as canonical semantics and architecture remain unchanged.

## Core Aggregate

Primary business aggregate:

- `light.internal.request`
- `light.internal.request.line`

One Internal Request contains one or more requirement lines.

## Governance Models

Approval configuration:

- `light.ir.approval.policy`
- `light.ir.approval.rule`
- `light.ir.approval.rule.step`

Approval transaction/history:

- `light.ir.approval.cycle`
- `light.ir.approval.cycle.step`
- `light.ir.approval.cycle.step.line`

Responsibility/delegation:

- `light.ir.responsibility.assignment`
- `light.ir.approval.delegation`

## Request Configuration

Core request configuration may include:

- `light.ir.request.type`
- `light.ir.policy`

Configuration shall remain distinct from historical transaction records.

### `light.ir.policy`

`light.ir.policy` is the request-level governance configuration model that
determines, per applicable Company / Request Type / effective period,
whether Review (TERMINOLOGY #23, FR-IR-042..045) and Fulfillment Plan
Approval (FR-IR-052) are required for a given Internal Request context.

`light.ir.policy` is distinct from `light.ir.approval.policy`:

- `light.ir.approval.policy` governs the NEED/FINANCIAL Approval Engine
  (ADR-005, DEC-010) and lives in `light_ir_approval`, which remains
  purpose-agnostic/generic and must not depend on `light_internal_request`
  (ADR-003, DEC-004).
- `light.ir.policy` governs request-level Review / Plan-Approval
  requirement configuration and lives in `light_internal_request`. It
  introduces no new Approval Engine purpose; NEED and FINANCIAL remain the
  only V1 approval purposes.

Minimum canonical fields:

- `name`
- `company_id` — required; applicable Company scope (TR-CFG-002).
- `request_type_id` — optional; empty means an explicit global policy,
  applicable only when no exact-Request-Type policy applies for the same
  Company/effective date (see TR-10 precedence rule). This never adds a
  behavioral field to `light.ir.request.type` itself (DEC-006, DEC-009,
  TERMINOLOGY #5): the behavior lives on `light.ir.policy`, resolved using
  Request Type only as a resolution dimension, exactly as
  `light.ir.approval.policy.request_type_id` already does for Approval
  Policy resolution.
- `date_from` / `date_to` — optional effective period (TR-CFG-005).
- `active` — standard archiving flag.
- `review_required` — Boolean. The resolved policy's explicit value
  determines whether Review is mandatory (FR-IR-042). An explicit `False`
  is a deliberate configuration decision, not a fallback.
- `plan_approval_required` — Boolean. The resolved policy's explicit value
  determines whether Fulfillment Plan Approval is mandatory (FR-IR-052).
  An explicit `False` is a deliberate configuration decision, not a
  fallback.

An applicable `light.ir.policy` MUST exist for the resolution context
before Review/Plan-Approval requirement can be determined. Missing,
ambiguous, or expired applicable policy blocks with an actionable error
(TR-CFG-006/007/008, ADR-025, DEC-019) — there is no silent default value
for `review_required` or `plan_approval_required`. See TR-10 for the exact
resolution/precedence rule.

## Fulfillment Planning

Structured fulfillment planning shall use a dedicated allocation model.

Canonical candidate:

- `light.ir.fulfillment.allocation`

V1 allocation types:

- `purchase`
- `internal`

## Price Validation

Structured price validation shall use a dedicated historical/business model
where applicable.

Canonical candidate:

- `light.ir.price.validation`

## Purchase Attribution

Purchase integration shall use structured attribution.

Canonical model:

- `light.ir.purchase.attribution`

It relates LIGHT requirement/allocation to standard Odoo Purchase Order Lines.

## Stock Attribution

Stock integration shall use structured attribution.

Canonical model:

- `light.ir.stock.attribution`

It relates LIGHT Internal Allocation to standard Odoo Stock Moves.

## Acceptance

Acceptance shall use structured events.

Canonical model:

- `light.ir.acceptance.event`

Aggregate Accepted Quantity shall derive from valid Acceptance and Reversal
effects rather than unrestricted direct editing.

## Fulfillment Exception

Canonical model:

- `light.ir.fulfillment.exception`

Exception shall remain distinct from Cancellation.

## Cancellation

Cancellation shall be represented by structured business records/events.

Canonical candidate:

- `light.ir.cancellation`

## Revision

Material post-submission changes shall use structured Revision.

Canonical model:

- `light.ir.revision`

## Company Ownership

Transactional and governance records shall have explicit Company context where
applicable.

Cross-company relationships outside V1 shall be rejected.

---

## TR-DAT-001 — IR Header and Lines Separate

Internal Request header and requirement lines shall be separate persistent
models.

## TR-DAT-002 — Structured Audit Beyond Chatter

Critical business history shall use structured records and shall not rely only
on Odoo Chatter.

## TR-DAT-003 — Approval Master and Approval Transaction Separate

Approval Policy/Rule/Step configuration shall be separate from Approval Cycle
transaction history.

## TR-DAT-004 — Fulfillment Plan and Execution Separate

Fulfillment Allocation shall remain separate from Purchase Order and Stock Move
execution.

## TR-DAT-005 — Structured Purchase Attribution

Purchase traceability shall use a dedicated structured attribution relation.

Free-text `origin` shall not be the authoritative relationship.

## TR-DAT-006 — Structured Stock Attribution

Stock traceability shall use a dedicated structured attribution relation at the
applicable execution level.

## TR-DAT-007 — Acceptance Event Model

Acceptance shall be represented by structured historical events rather than
only by freely editable aggregate quantity fields.

## TR-DAT-008 — Revision Preserves Prior Business State

Material Revision shall preserve enough historical context to reconstruct the
prior business version and its authorization relationship.

## TR-DAT-009 — Explicit Company Context

Applicable LIGHT transactional models shall carry explicit Company ownership
or deterministic Company context.

## TR-DAT-010 — Historical Governance Traceability

Critical governance records shall preserve enough historical data to reconstruct
who acted, under which authority, on what business basis, and when.

---

## Data Integrity Principle

Master/configuration records answer:

"What policy currently applies?"

Historical transaction records answer:

"What policy/authority actually applied to this transaction at that time?"

These concerns shall not be collapsed into one mutable record structure.

---

## Data Model Baseline Statement

`TR-DAT-001` through `TR-DAT-010` constitute the V1 data-model baseline.

Implementation may refine technical field names only where canonical
terminology, history, and traceability semantics remain intact.
