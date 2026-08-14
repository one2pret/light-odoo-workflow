# LIGHT Internal Request
## Non-Functional Requirements

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED
- Initial Deployment: Odoo Enterprise
- Core Architecture Goal: Community-Compatible

This document defines the non-functional constraints for LIGHT Internal
Request V1.

---

## NFR-IR-001 — Odoo 18 Compatibility

LIGHT Internal Request V1 shall target Odoo 18.

Implementation shall not rely on obsolete or deprecated behavior from older
Odoo versions when an Odoo 18-supported mechanism exists.

---

## NFR-IR-002 — Standard Odoo First

The implementation shall reuse standard Odoo capabilities and business objects
before introducing custom equivalents.

Examples include:

- `purchase.order`
- `purchase.order.line`
- `stock.picking`
- `stock.move`
- `stock.move.line`
- `product.product`
- `product.template`
- `uom.uom`
- `res.company`
- `res.users`
- `res.partner`
- `hr.department` through the approved HR integration boundary

Custom LIGHT models shall be created only for LIGHT-specific domain concepts.

---

## NFR-IR-003 — Community-Compatible Core

Core LIGHT architecture shall remain compatible with Odoo Community unless an
explicit approved integration requires otherwise.

Initial production deployment may use Odoo Enterprise.

Community-compatible does not mean production must run Odoo Community.

---

## NFR-IR-004 — Modular Architecture

LIGHT shall use clear addon boundaries:

- `light_ir_approval`
- `light_internal_request`
- `light_ir_hr`
- `light_ir_purchase`
- `light_ir_stock`

Circular addon dependencies are prohibited.

Integration-specific dependencies shall not be moved into core merely for
implementation convenience.

---

## NFR-IR-005 — Server-Side Business Integrity

Critical business rules shall be enforced server-side.

This includes, where applicable:

- approval authority;
- quantity limits;
- Company compatibility;
- financial authority;
- Acceptance limits;
- Cancellation limits;
- Revision restrictions;
- governance policy resolution.

UI restrictions alone are insufficient.

---

## NFR-IR-006 — Security by Design

The system shall use Odoo security architecture appropriately:

- ACL
- Record Rules
- Business Authorization
- Company Validation

Security shall not rely only on:

- XML `readonly`;
- XML `invisible`;
- button visibility;
- client-side JavaScript;
- view domains.

---

## NFR-IR-007 — Multi-Company Isolation

The system shall prevent unauthorized cross-company visibility and
transaction linkage.

Applicable LIGHT records shall have deterministic Company ownership/context.

Cross-company fulfillment is outside V1 and shall be blocked where a V1
boundary would otherwise be violated.

---

## NFR-IR-008 — Auditability

Critical business decisions and events shall be reconstructable.

Applicable audit context shall include, as required by the event:

- Actor
- Timestamp
- Business Object
- Action
- Reason
- Relevant Business Basis
- Result
- Applicable Authority
- Source Transaction

Odoo Chatter may supplement audit history but shall not be the sole
authoritative audit mechanism for critical governance.

---

## NFR-IR-009 — Historical Integrity

Finalized historical records shall not normally be destructively rewritten.

Correction shall use an appropriate mechanism such as:

- Revision
- Superseding Approval Cycle
- Reversal Event

depending on the business event being corrected.

---

## NFR-IR-010 — Deterministic Governance

Critical policy resolution shall produce deterministic results for the same
authoritative business context.

Missing critical configuration shall:

BLOCK

Ambiguous critical configuration shall:

BLOCK

The system shall not silently select an arbitrary weaker policy.

---

## NFR-IR-011 — Configuration Over Hard-Code

Business-specific governance values shall not be hard-coded as database IDs,
named users, or fixed organization-specific thresholds.

Examples of prohibited patterns include logic conceptually equivalent to:

`if user.id == 17: approve()`

or:

`if amount > 100000000: require_ceo()`

when those values are business configuration.

Thresholds, responsibilities, Request Types, and policy behavior shall be
configuration-driven.

---

## NFR-IR-012 — Upgrade-Safe Odoo Extension

Standard Odoo shall be extended using supported Odoo extension mechanisms.

Prefer:

- `_inherit`
- `super()`
- ORM
- standard model APIs
- standard business hooks

Avoid:

- copying entire standard Odoo methods only to insert small LIGHT changes;
- replacing standard workflows unnecessarily;
- monkey-patching without approved architecture;
- direct database changes that bypass Odoo business behavior.

LIGHT-specific behavior shall be narrowly scoped to valid LIGHT-linked
transactions where applicable.

---

## NFR-IR-013 — ORM-First Data Access

Normal business transaction processing shall use the Odoo ORM by default.

Direct SQL may only be used when there is a justified technical requirement,
the ORM is not adequate, cache/security implications are understood, and the
implementation is appropriately documented and tested.

Direct SQL shall not be used merely to bypass ORM constraints or governance.

---

## NFR-IR-014 — Performance Awareness

Implementation shall avoid obvious scalability problems, including:

- N+1 queries;
- repeated `search()` calls inside large loops;
- unbounded record loading;
- unnecessary recomputation;
- repeated policy resolution without need.

Use recordset-based and batched operations where appropriate.

Premature optimization is not required, but known pathological
implementations are not acceptable.

---

## NFR-IR-015 — Transactional Consistency

Critical operations that create or update related business records shall
preserve transactional consistency.

Examples include:

- Approval Cycle plus Approval Steps;
- Purchase Attribution plus allocation effect;
- Stock Attribution plus execution relationship;
- Acceptance Event plus derived business state.

A partial technical failure shall not leave the business process in a state
that appears successfully completed but is internally inconsistent.

---

## NFR-IR-016 — Idempotency Where Required

Operations that may be triggered more than once shall be idempotent where
duplicate processing could corrupt quantity, authorization, or audit history.

This requirement applies particularly to:

- Automatic Acceptance;
- event processing;
- attribution synchronization where applicable.

Duplicate trigger execution shall not create duplicate business effect.

---

## NFR-IR-017 — Explicit Failure

Critical failures shall provide actionable user-facing errors.

Example:

`No active Financial Approval Policy was found for the applicable Company,
Department, and Request Type.`

Generic validation errors should be avoided where the system knows the actual
cause.

Error messages shall not expose information the user is not authorized to
view.

---

## NFR-IR-018 — Maintainability

Source code shall maintain separation of concerns and canonical terminology.

The same business rule shall not be duplicated across multiple addons without
clear architectural justification.

Canonical names shall be used consistently, including:

- `requested_qty`
- `approved_qty`
- `allocated_qty`
- `delivered_qty`
- `accepted_qty`
- `cancelled_qty`
- `outstanding_qty`

Inconsistent alternate names for the same canonical concept shall be avoided.

---

## NFR-IR-019 — Automated Regression

Critical functional behavior shall have automated test coverage.

At minimum, test coverage shall include applicable cases for:

- Approval
- Threshold boundaries
- Self-approval
- Delegation
- Missing approver
- Quantity invariants
- Purchase attribution
- PO Commitment Authority
- Stock attribution
- Partial execution
- Backorder
- Acceptance
- Automatic Acceptance idempotency
- Fulfillment Exception
- Cancellation
- Revision
- Reversal
- Security
- Multi-company
- Critical configuration failure
- Non-LIGHT Purchase regression
- Non-LIGHT Stock regression

Automated tests are part of Definition of Done.

---

## NFR-IR-020 — Requirement Traceability

Implementation shall maintain traceability across:

FR
→ NFR / ADR
→ Technical Requirement
→ Test
→ Milestone
→ Implementation

Critical implementation without a traceable business/technical justification
shall be reviewed before entering the baseline.

---

# Non-Functional Baseline Statement

`NFR-IR-001` through `NFR-IR-020` constitute the LIGHT Internal Request V1
Non-Functional Requirements baseline.

A coding agent shall not weaken these constraints merely to simplify
implementation.

If a conflict is discovered between functional behavior and non-functional
constraints, implementation of the affected area shall stop until the conflict
is resolved through approved baseline change control.
