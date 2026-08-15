# LIGHT Internal Request — Implementation Plan

- Target: Odoo 18
- Version: 1.0
- Status: BASELINED

## Source Code Location

Documentation repository:

`odoo18-dev/light-odoo`

Odoo addon source root:

`../custom-addons/`

All implementation milestones must create or modify Odoo addon source under
`../custom-addons/`.

Approved V1 addon paths:

- `../custom-addons/light_ir_approval`
- `../custom-addons/light_internal_request`
- `../custom-addons/light_ir_hr`
- `../custom-addons/light_ir_purchase`
- `../custom-addons/light_ir_stock`

Do not create production addon source inside `light-odoo`.

## Milestone Rules

- Implement one milestone at a time.
- Do not implement later milestones early.
- Every milestone must identify FR/TR/ADR coverage.
- Automated tests are part of Definition of Done.
- A milestone first reaches READY_FOR_REVIEW, not automatically DONE.
- Architecture deviations require an approved ADR.

## Milestone Status Values

- NOT_STARTED
- IN_PROGRESS
- BLOCKED
- READY_FOR_REVIEW
- DONE

## M01 — Repository & Addon Skeleton

### Scope

Create installable addon skeletons:

- `light_ir_approval`
- `light_internal_request`
- `light_ir_hr`
- `light_ir_purchase`
- `light_ir_stock`

Create standard addon structure where applicable:

- `__manifest__.py`
- `__init__.py`
- `models/`
- `security/`
- `views/`
- `tests/`

Do not implement business workflow in M01.

### Requirements

- ADR-002
- ADR-003
- ADR-004
- TR-ARC-001..010

### Acceptance Criteria

- All five addon skeletons exist.
- Manifest dependencies follow the approved module architecture.
- No circular dependencies.
- No custom Purchase engine.
- No custom Inventory engine.
- No Enterprise-only dependency is introduced into Community-compatible core.
- No later milestone business feature is implemented.

### Tests

- addon import/install smoke test where environment allows
- manifest dependency sanity
- module dependency review

### Definition of Done

- required skeletons created
- dependency graph matches baseline
- required tests/checks pass
- no architecture deviation
- completion report produced

## M02 — Approval Configuration Models

### Scope

Create approval master configuration models:

- `light.ir.approval.policy`
- `light.ir.approval.rule`
- `light.ir.approval.rule.step`
- `light.ir.responsibility.assignment`
- `light.ir.approval.delegation`

### Requirements

- FR-IR-026..028
- FR-IR-038..041
- FR-IR-149..158
- TR-APR-001..003
- TR-CFG-001..012
- ADR-005
- ADR-024
- ADR-025
- ADR-026

### Definition of Done

Configuration resolution and validation are deterministic and tested.

## M03 — Approval Cycle Engine

Create:

- `light.ir.approval.cycle`
- `light.ir.approval.cycle.step`
- `light.ir.approval.cycle.step.line`

Support policy resolution, cycle snapshot, sequential approval, self-approval skip, missing approver blocking, delegation, completion, rejection, and superseding.

Requirements:
- FR-IR-025..041
- FR-IR-059..063
- TR-APR-004..013
- TR-AUD-007..009
- ADR-005
- ADR-006
- ADR-007
- ADR-008

### Scope Clarification

M03 implements the purpose-agnostic Approval Cycle Engine and generic approval
capabilities shared by NEED and FINANCIAL approval flows.

The inclusion of `FR-IR-059..063` in M03 identifies generic engine capability
required by Financial Approval. It does not mean those Financial Approval
requirements are functionally complete in M03.

Financial-specific behavior for `FR-IR-059..063` remains implemented,
integrated, tested, and accepted in M07/M11 according to the RTM.

## M04 — HR Organizational Resolver

Implement `light_ir_hr` integration with standard Odoo HR.

Support requester-to-employee, employee-to-department, department manager/head, and organizational responsibility resolution.

Requirements:
- FR-IR-015
- FR-IR-027
- FR-IR-150
- FR-IR-152
- ADR-004
- TR-ARC-003
- TR-APR-005

## M05 — Internal Request Header & Lines

Create:

- `light.internal.request`
- `light.internal.request.line`
- `light.ir.request.type`

Support draft IR, requester, company, request type, receiver, required date, purpose, Goods/Service, registered/unregistered requirement, requested quantity, UoM, requester estimate, attachments.

Requirements:
- FR-IR-001..024
- FR-IR-149
- TR-DAT-001
- TR-DAT-009
- TR-QTY-001..003
- ADR-009
- ADR-023

## M06 — Submit & Need Approval Integration

Connect Internal Request to Approval Engine.

Support Submit, submission validation, NEED approval cycle, quantity reduction, rejection, self-approval handling, and transaction-specific authority.

Requirements:
- FR-IR-019..041
- FR-IR-146..148
- FR-IR-159..163
- TR-APR-*
- TR-QTY-001..003
- TR-SEC-001..005

## M07 — Price Validation

Create structured Price Validation.

Support requester estimate, reference price, validated price, currency, validator, validation history, and unknown initial price.

Requirements:
- FR-IR-017..018
- FR-IR-053..058
- TR-DAT-010
- TR-CFG-003
- TR-SEC-001

## M08 — Fulfillment Planning

Create `light.ir.fulfillment.allocation`.

V1 fulfillment types:
- purchase
- internal

Support Purchase-only, Internal-only, split fulfillment, review requirement, plan approval policy, and allocation constraints.

Requirements:
- FR-IR-042..052
- TR-QTY-003..005
- TR-DAT-004
- TR-CFG-002..003
- ADR-010

## M09 — Purchase Attribution

Implement `light_ir_purchase`.

Create `light.ir.purchase.attribution`.

Support:
- one IR to multiple PO lines/orders
- multiple IR to one PO line
- explicit quantity attribution
- bidirectional navigation

Requirements:
- FR-IR-064..074
- TR-PUR-001..009
- ADR-011
- ADR-012

## M10 — Sourcing / RFQ Workflow

Support:
- Create RFQ
- Multiple vendor quotation
- Consolidate compatible IR
- Add to compatible draft RFQ
- Vendor award
- Split award

Sourcing quantities must not multiply the effective Purchase Allocation.

Requirements:
- FR-IR-066..073
- ADR-012
- TR-PUR-002..009

## M11 — Financial Approval & PO Confirmation Guard

Add LIGHT-specific financial authority checks to LIGHT-linked standard Odoo Purchase Orders.

Support:
- IR Financial Authorization
- PO Commitment Authority
- consolidated commitment evaluation
- threshold crossing
- financial reauthorization
- non-LIGHT PO standard behavior

Requirements:
- FR-IR-055..063
- FR-IR-075..078
- TR-PUR-010..012
- TR-APR-012..013
- ADR-013
- ADR-014

## M12 — Stock Attribution

Implement `light_ir_stock`.

Create `light.ir.stock.attribution`.

Support explicit quantity-aware relation between Internal Allocation and `stock.move`.

Requirements:
- FR-IR-079..090
- TR-STK-001..004
- ADR-015

## M13 — Internal Transfer Execution

Support:
- create internal transfer
- consolidate compatible allocations
- partial transfer
- backorder
- deterministic attribution
- non-LIGHT stock regression

Requirements:
- FR-IR-082..090
- TR-STK-005..010

## M14 — Acceptance Engine

Create `light.ir.acceptance.event`.

Support:
- manual acceptance
- automatic acceptance
- not required
- partial acceptance
- receiver authorization
- idempotent automatic acceptance
- service acceptance without Stock Move

Requirements:
- FR-IR-091..101
- FR-IR-110..113
- TR-ACC-001..007
- ADR-016
- ADR-017

## M15 — Fulfillment Exception & Replacement

Create `light.ir.fulfillment.exception`.

Support configurable exception reasons and controlled resolution:
- replacement
- return and replace
- redelivery
- accept as is
- cancel remaining
- other

Requirements:
- FR-IR-102..113
- TR-ACC-008..011
- ADR-018
- ADR-019

## M16 — Cancellation

Create structured cancellation workflow.

Support:
- cancellation request
- authorization
- downstream resolution
- effective cancellation
- partial cancellation
- full cancellation

Requirements:
- FR-IR-114..119
- FR-IR-135..138

## M17 — Revision & Reversal

Create `light.ir.revision` and controlled reversal mechanisms.

Support:
- material revision
- impact evaluation
- approval superseding
- company change block
- single active material revision
- acceptance reversal
- controlled reactivation through valid business events

Requirements:
- FR-IR-120..134
- TR-AUD-001..012
- ADR-021
- ADR-022
- ADR-028

## M18 — Security & Configuration Hardening

Review and harden:
- ACL
- record rules
- groups
- company isolation
- server-side authorization
- sudo usage
- unlink restrictions
- configuration rights
- auditor rights
- administrator separation

Requirements:
- FR-IR-149..178
- TR-SEC-*
- TR-CFG-*
- ADR-007
- ADR-008
- ADR-025

## M19 — Worklists, Monitoring & Navigation

Implement operational UX using standard Odoo views and activities.

Menus/worklists may include:
- My Requests
- My Actions
- Pending Approvals
- Review Queue
- Procurement Worklist
- Internal Fulfillment Worklist
- Pending Acceptance
- Exceptions
- All Requests
- Configuration

Requirements:
- FR-IR-186..195

Do not create a large custom JavaScript dashboard without a new approved requirement.

## M20 — End-to-End Regression & Release Candidate

No major new feature.

Validate golden scenarios:
- Simple Purchase
- Simple Internal Transfer
- Split Internal + Purchase
- Unknown Price → Sourcing → Financial
- Many IR → One PO
- One IR → Multiple Vendors
- Partial Receipt / Acceptance
- Exception → Replacement
- Partial Cancellation
- Material Revision → Reapproval
- Delegated Approval
- Cross-company violation blocked
- Self-approval skip
- Merged PO higher commitment authority

Also validate:
- non-LIGHT Purchase regression
- non-LIGHT Stock regression
- module install/upgrade
- security regression
- multi-company regression

M20 is complete only when:
- applicable automated tests pass
- V1 RTM coverage is complete
- no critical unmapped requirement remains
- no critical unresolved security or architecture defect remains

## Milestone Completion Report

Every milestone must end with:

### Milestone
Mxx — Name

### Status
READY_FOR_REVIEW

### Requirements Covered
- FR IDs
- TR IDs
- ADR IDs

### Files Changed
- list

### Tests Added
- list

### Tests Executed
- list

### Results
- passed
- failed

### Standard Odoo Extensions
- model
- method
- reason

### Security Impact
- summary

### Database/Data Model Impact
- summary

### Deferred Items
- list

### Open Issues
- list

### Architecture Deviations
None, or reference the approved ADR.
