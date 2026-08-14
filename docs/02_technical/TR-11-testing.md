# LIGHT Internal Request
## TR-11 — Testing Strategy and Definition of Done

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

## Purpose

This document defines mandatory automated testing expectations for LIGHT V1.

Tests are part of implementation, not optional post-development cleanup.

## Odoo Test Strategy

Use appropriate Odoo automated test infrastructure.

Prefer transactional test isolation for business logic.

Use HTTP/UI tests only where behavior genuinely requires that layer.

Core governance shall primarily be verifiable through server-side automated
tests.

## Test Categories

V1 requires applicable coverage for:

- Model constraints
- Business actions
- Approval resolution
- Security
- Multi-company
- Purchase integration
- Stock integration
- Quantity invariants
- Acceptance
- Exception
- Cancellation
- Revision
- Audit
- Configuration failure
- Regression isolation

## Approval Tests

Tests shall include:

- valid Need Approval
- valid Financial Approval
- sequential steps
- threshold boundary
- missing approver
- ambiguous policy
- self-approval protection
- self-step skip where applicable
- valid delegation
- expired delegation
- unauthorized approval
- superseded approval cycle

## Quantity Tests

Tests shall include:

- Approved <= Requested
- allocation <= authorized quantity
- split fulfillment
- partial execution
- Accepted + Cancelled <= Approved
- Outstanding formula
- approval quantity reduction not treated as Cancellation
- UoM precision behavior

## Purchase Tests

Tests shall include:

- Purchase Allocation
- RFQ creation
- multiple sourcing RFQs do not multiply requirement
- one IR to multiple POs
- multiple IRs to one PO Line
- attribution quantity integrity
- LIGHT PO commitment guard
- merged commitment threshold
- commercial change reauthorization where applicable
- PO cancellation/release behavior
- non-LIGHT PO regression

## Stock Tests

Tests shall include:

- Internal Allocation
- Stock Attribution
- partial transfer
- backorder
- consolidated Stock Move attribution
- deterministic partial attribution
- cross-company block
- non-LIGHT Inventory regression

## Acceptance Tests

Tests shall include:

- manual Acceptance
- partial Acceptance
- unauthorized Acceptance
- automatic Acceptance
- automatic Acceptance idempotency
- Acceptance Reversal
- Delivered != Accepted
- Not Required policy where applicable

## Exception Tests

Tests shall include:

- Exception creation
- partial Exception
- Exception does not become Cancellation
- replacement
- redelivery
- accept as is
- cancel remaining
- no replacement double counting
- Service Exception

## Revision Tests

Tests shall include:

- material quantity Revision
- Product Revision
- financial basis Revision
- reapproval
- superseded historical cycle
- Company change blocked
- no generic Reopen

## Security Tests

Tests shall include:

- Requester
- Approver
- Reviewer
- Procurement
- Inventory
- Receiver
- Auditor
- Administrator
- unauthorized action
- Company visibility
- cross-company relationships

## Configuration Tests

Tests shall include:

- valid policy resolution
- missing policy
- ambiguous policy
- expired policy
- threshold overlap
- effective-date boundary

## Regression Principle

Installing LIGHT shall not break unrelated standard Odoo business behavior.

At minimum include regression scenarios for:

- normal non-LIGHT Purchase Order
- normal non-LIGHT Inventory transaction

## Test Naming

Test names should describe business behavior.

Preferred:

`test_requester_cannot_approve_own_need_step`

`test_multiple_rfq_does_not_multiply_purchase_allocation`

`test_partial_receipt_does_not_complete_ir`

`test_automatic_acceptance_is_idempotent`

Avoid meaningless names such as:

`test_1`

`test_flow2`

## Requirement Traceability

Critical tests shall reference applicable FR/TR identifiers in test comments,
docstrings, naming conventions, or RTM mapping according to the implementation
standard.

---

## TR-TST-001 — Tests Are Definition of Done

A milestone is not Done while applicable required automated tests are missing
or failing.

## TR-TST-002 — Server-Side Governance Tests

Critical server-side governance shall have automated tests independent of UI
button visibility.

## TR-TST-003 — Negative Tests Required

Critical authorization and invariants shall include negative tests, not only
happy-path tests.

## TR-TST-004 — Threshold Boundary Tests

Financial threshold behavior shall be tested at exact boundary values.

## TR-TST-005 — Multi-Company Tests

Company isolation and cross-company relationship guards shall have automated
tests.

## TR-TST-006 — Idempotency Tests

Automatic/event-driven operations that require idempotency shall have duplicate
trigger tests.

## TR-TST-007 — Partial Quantity Tests

Partial Purchase, Stock, Acceptance, Cancellation, and applicable backorder
behavior shall be tested.

## TR-TST-008 — Historical Integrity Tests

Revision, Reversal, superseding Approval, and other historical mechanisms shall
verify that prior finalized history remains intact.

## TR-TST-009 — Non-LIGHT Regression Tests

LIGHT Purchase/Stock integration shall include tests proving unrelated
non-LIGHT standard transactions retain expected behavior.

## TR-TST-010 — Traceability

Critical automated tests shall be traceable to applicable functional and
technical requirements.

## TR-TST-011 — No False Green

Tests shall assert business results and invariants rather than merely asserting
that methods execute without exceptions.

## TR-TST-012 — Reproducibility

Automated tests shall not depend on uncontrolled production data, arbitrary
record ordering, or external mutable state.

---

## Milestone Gate

Before marking an implementation milestone complete:

1. module installs/upgrades successfully;
2. applicable automated tests pass;
3. no known critical invariant is bypassed;
4. security tests pass;
5. requirement traceability is updated;
6. documentation changes caused by implementation are reconciled.

## Testing Baseline Statement

`TR-TST-001` through `TR-TST-012` constitute the V1 Testing baseline.
