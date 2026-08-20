# LIGHT Internal Request — Decision Register

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED

This register captures binding V1 product and governance decisions.

## DEC-001 — Odoo Version

**Status:** LOCKED

LIGHT Internal Request V1 targets Odoo 18.

## DEC-002 — Initial Deployment Edition

**Status:** LOCKED

Initial deployment targets Odoo Enterprise.

Core LIGHT architecture must remain Community-compatible unless an explicitly
approved integration requires otherwise.

## DEC-003 — Standard Odoo First

**Status:** LOCKED

Use standard Odoo business objects for capabilities already owned by Odoo.

LIGHT must not duplicate standard Purchase, Inventory, Product, UoM, Company,
User, or Partner masters merely for implementation convenience.

## DEC-004 — Modular Addon Architecture

**Status:** LOCKED

V1 addon family:

- `light_ir_approval`
- `light_internal_request`
- `light_ir_hr`
- `light_ir_purchase`
- `light_ir_stock`

Circular addon dependencies are forbidden.

## DEC-005 — HR Integration Boundary

**Status:** LOCKED

Organizational integration with standard Odoo HR is isolated in `light_ir_hr`.

Core approval functionality must not require HR as an unconditional dependency.

## DEC-006 — Request Type Is Configurable Governance Context

**Status:** LOCKED

Request Type may participate in determining applicable Review and Approval policy.

Requester does not manually decide whether a Request Type requires Review.

The applicable behavior is resolved from configuration.

## DEC-007 — Department/Division Uses Organizational Master

**Status:** LOCKED

Where Odoo HR integration is available, Department/Division context uses standard
`hr.department`.

LIGHT must not create a competing Department master merely to reproduce
organizational structure.

## DEC-008 — Company Governance Can Differ

**Status:** LOCKED

Approval/governance rules may differ by Company/business entity.

Governance configuration must support Company scope and applicable
organizational dimensions rather than hard-coded universal approval chains.

## DEC-009 — Approval Matrix Is Configurable

**Status:** LOCKED

Approval paths must be configuration-driven.

The system must not hard-code named individuals such as Head, CFO, CEO, or
specific C-Level users into business logic.

## DEC-010 — One Approval Engine

**Status:** LOCKED

Need Approval and Financial Approval use one configurable Approval Engine.

Approval purposes:

- `NEED`
- `FINANCIAL`

## DEC-011 — Requester Price Is Optional by Default

**Status:** LOCKED

Requester Price Estimate is optional by default.

A requester is not assumed to know the procurement price.

## DEC-012 — Price Status Is Not Requester Knowledge Burden

**Status:** LOCKED

Requester is not required by default to determine a technical Price Status
they cannot reasonably know.

## DEC-013 — Structured Price Validation

**Status:** LOCKED

Requester estimate, reference price, validated price, vendor quotation, and
actual PO commitment are distinct concepts.

## DEC-014 — Financial Threshold Basis Is Configurable

**Status:** LOCKED

Applicable Financial Approval policy may define whether threshold evaluation uses:

- Untaxed amount
- Tax Included amount

## DEC-015 — Self-Approval Forbidden by Default

**Status:** LOCKED

A requester/transaction owner must not approve their own applicable approval step
by default.

## DEC-016 — Capability Does Not Equal Transaction Authority

**Status:** LOCKED

Membership in an Approver or Administrator security group does not grant
authority to approve every transaction.

## DEC-017 — Missing Mandatory Approver Blocks

**Status:** LOCKED

If a mandatory approver/responsibility cannot be resolved, processing blocks
by default.

## DEC-018 — Delegation Is Supported and Audited

**Status:** LOCKED

Valid delegation may permit a delegate to perform an approval action.

Audit must preserve original authority and actual actor.

## DEC-019 — Governance Has No Silent Critical Fallback

**Status:** LOCKED

Missing, ambiguous, expired, or otherwise invalid governance-critical
configuration must block with an explicit error.

## DEC-020 — Need Approval May Reduce Quantity

**Status:** LOCKED

Need Approval may authorize less than Requested Quantity.

Quantity not approved during Need Approval is not Cancellation.

## DEC-021 — Fulfillment Planning Is Separate From Execution

**Status:** LOCKED

Approved requirements are planned through Fulfillment Allocation.

V1 routes:

- Purchase
- Internal

Allocation is not equivalent to actual Purchase Order or Stock Move.

## DEC-022 — Split Fulfillment Is Supported

**Status:** LOCKED

One IR Line may be fulfilled using both Purchase and Internal routes where
applicable.

## DEC-023 — IR Merge Into Procurement Is Allowed

**Status:** LOCKED

Multiple compatible IR requirements may be consolidated into procurement.

The source IRs and quantities must remain explicitly traceable.

## DEC-024 — One IR May Use Multiple Vendors

**Status:** LOCKED

One IR requirement may be fulfilled through multiple vendors/POs where required.

## DEC-025 — Purchase Attribution Is Authoritative

**Status:** LOCKED

LIGHT uses structured Purchase Attribution between IR/Purchase Allocation and
standard Odoo Purchase Order Lines.

`purchase.order.origin` is informational only.

## DEC-026 — RFQ/Sourcing Is Not Commitment

**Status:** LOCKED

Competing RFQs do not multiply the authorized Purchase requirement.

## DEC-027 — Merged PO Must Respect Actual Commitment Authority

**Status:** LOCKED

Financial governance applies not only to individual source IR authorization but
also to the actual LIGHT-linked PO commitment.

## DEC-028 — LIGHT Purchase Guard Is Scoped

**Status:** LOCKED

LIGHT-specific Purchase controls apply only to Purchase transactions having a
valid LIGHT relationship.

Normal non-LIGHT Odoo Purchase transactions retain standard behavior.

## DEC-029 — Standard Odoo Inventory Executes Stock

**Status:** LOCKED

Actual inventory execution uses standard Odoo Inventory models.

LIGHT must not create a parallel inventory engine.

## DEC-030 — Stock Attribution Is Quantity-Aware

**Status:** LOCKED

LIGHT Internal Allocation must be explicitly attributable to actual stock
execution.

## DEC-031 — Delivery Is Not Acceptance

**Status:** LOCKED

Operational delivery/stock completion does not automatically mean business
Acceptance unless an applicable automatic acceptance policy explicitly creates
the required Acceptance event.

## DEC-032 — Acceptance Is Structured and Event-Based

**Status:** LOCKED

Acceptance must be represented by structured historical events.

Default Acceptance mode is Manual.

## DEC-033 — Exception Is Not Cancellation

**Status:** LOCKED

Damaged, wrong, short, incomplete, or otherwise problematic fulfillment is a
Fulfillment Exception.

It does not automatically reduce the authorized requirement.

## DEC-034 — Replacement Must Not Double Count

**Status:** LOCKED

Replacement/redelivery execution must not create a second authorized business
requirement for the same quantity.

## DEC-035 — Canonical Outstanding Quantity

**Status:** LOCKED

Canonical V1 formula:

`outstanding_qty = approved_qty - accepted_qty - cancelled_qty`

## DEC-036 — Revision Preserves History

**Status:** LOCKED

Material changes after submission use formal Revision.

Revision must not destructively rewrite historical approvals or finalized
governance history.

## DEC-037 — No Generic Reopen

**Status:** LOCKED

There is no unrestricted generic `Reopen IR` action.

## DEC-038 — Company Is a Hard V1 Boundary

**Status:** LOCKED

Company is a security/accounting/execution boundary.

Cross-company fulfillment is outside V1.

## DEC-039 — Structured Audit Is Authoritative

**Status:** LOCKED

Critical governance/business history must use structured auditable records.

Odoo Chatter is supplementary.

## DEC-040 — Tests Are Part of Definition of Done

**Status:** LOCKED

Automated tests are part of implementation.

A milestone is not Done while applicable required tests are missing or failing.

## DEC-041 — IR Completion Predicate and Controlled Reactivation

**Status:** LOCKED

Closes the FR-IR-136..142/145 gap left open by M14/M16/M17 (M20).

Completion eligibility, per line:

- `outstanding_qty == 0`
- no open Fulfillment Exception
- no pending Acceptance (`delivered_qty > accepted_qty`)

Terminal outcome, once every line is eligible:

- any line has `accepted_qty > 0` (accepted-only or mixed with Cancellation) -> `done`
- no line has any `accepted_qty > 0` (cancellation-only) -> `cancelled`

Completion is evaluated automatically by a centralized evaluator invoked only
from within an already-authorized business action (Acceptance, Reversal,
Cancellation, Exception report/resolve, applied Revision). No new Mark Done
action, authority, group, or responsibility is introduced.

A terminal IR may become non-terminal again only as a side effect of one of
those same already-authorized actions (Acceptance Reversal, Report Exception,
authorized Revision) re-opening the predicate above. This is not DEC-037's
generic Reopen: no unrestricted reopen action exists: reactivation is never
independently callable and never accepts a caller-supplied target state.

Automatic Acceptance (FR-IR-100/101) remains outside this decision and
remains deferred.

## Change Control

A coding agent must not silently alter a LOCKED decision.

If implementation reveals a conflict:

1. Stop the affected implementation.
2. Identify affected DEC/FR/NFR/TR/ADR IDs.
3. Report the conflict.
4. Propose resolution options.
5. Obtain an approved baseline change where required.
