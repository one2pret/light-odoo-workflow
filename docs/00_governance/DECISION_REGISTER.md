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

**Status:** LOCKED. Partially superseded by DEC-042 (M21): optionality
applies while Draft only. This entry is preserved unmodified for
historical traceability; DEC-042 is the authoritative Submit-time rule.

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

## DEC-042 — Requester Price Estimate Mandatory at Submit (M21)

**Status:** LOCKED. Supersedes DEC-011 regarding Submit-time optionality
only. DEC-012 and DEC-013 remain valid and unaffected.

`light.internal.request.line.price_estimate` remains optional while the
Internal Request is Draft — Estimated Unit Price is never required while
Draft.

Every submitted requirement line must have `price_estimate > 0`. Submit
blocks otherwise (FR-IR-017/018, TR-APR-003 pattern: missing
governance-critical basis blocks by default).

`price_estimate` remains distinct from `validated_price_untaxed` /
`validated_price_tax_included` (DEC-013) and from actual PO commercial
value: it is Requester-supplied evidence used as NEED Approval routing
basis, never overwritten by Price Validation or Purchase.

## DEC-043 — Product Cost Is a Draft-Time Seed, Never a Live Approval Dependency (M21)

**Status:** LOCKED.

`product.standard_price` may initialize `price_estimate` once, when a
catalog Product is selected on a Draft requirement line and the field is
still empty. This is a Draft-time convenience default only.

The Requester may override the seeded `price_estimate` at any time while
Draft; the override persists and is never re-seeded.

Where the Internal Request line's Unit of Measure differs from the
Product's own Unit of Measure, standard Odoo UoM price conversion
(`uom.uom._compute_price()`) must be used when seeding — never an
invented ratio. Standard Odoo currency conversion must be used where the
Product's Cost currency differs from the Internal Request's Currency.

`standard_price` is never a live approval dependency: it is read once, at
seed time, and never read again. Changing Product Cost after Submit must
never alter the historical NEED Approval valuation of an already-submitted
Internal Request.

No separate requester-facing "Product Cost" field is exposed; `standard_price`
is used server-side only, to compute the `price_estimate` default.

## DEC-044 — Department Snapshot and Independent Routing Dimension (M21)

**Status:** LOCKED.

Department is an approval-routing dimension independent from Request
Type. Request Type must never be used as a Department proxy, and vice
versa.

`light.internal.request.department_id` is a stored snapshot field
(never a live/related field), resolved from the Requester's HR Employee
record (`res.users -> hr.employee -> department_id`) within the Internal
Request's own Company.

The snapshot is re-resolvable while Draft (tracks Requester/Company
changes for Draft-time convenience) and freezes immutably at Submit,
using the same Draft-only write-guard pattern as other material header
fields (TR-APR-004 Cycle Snapshot pattern).

Later Employee Department changes (HR reorganization) after Submit must
never alter the historical routing meaning of an already-submitted
Internal Request.

No historical Department backfill is performed for Internal Requests
submitted before M21; `department_id` remains `False` on those records.

## DEC-045 — Department Policy Precedence and Fail-Closed Relevance (M21)

**Status:** LOCKED.

`light.ir.approval.policy` gains an optional `department_id` dimension,
independent of and additive to the existing Request Type dimension
(DEC-006/DEC-019 pattern). Resolution for NEED purpose uses a
deterministic 4-tier precedence:

1. Department exact + Request Type exact
2. Department exact + Request Type global
3. Department global + Request Type exact
4. Department global + Request Type global (fully global)

At each tier: exactly one match resolves it; more than one match at the
same tier blocks as ambiguous (never "first record wins"); zero matches
falls through to the next tier; no match at Tier 4 blocks per existing
governance behavior (TR-APR-003, ADR-025 explicit-fallback allowance).

**Fail-closed relevance rule.** Department resolution is NOT universally
mandatory. Before Submit, the system determines whether Department-aware
NEED governance is relevant: at least one active Policy exists for the
current Company, `purpose = need`, the current effective date, with
`department_id` set, and Request Type compatible with the current
request (`request_type_id` equal to the current Request Type, or global).

- If no such Policy exists: M20 backward-compatible Request-Type/global
  resolution is preserved. A missing or unresolvable Requester
  Employee/Department does not, by itself, block Submit merely because
  `light_ir_hr` happens to be installed.
- If one or more such Policies exist: Requester Employee resolution
  becomes mandatory. Exactly one applicable Employee must resolve in the
  request's Company; the Employee must have a Department. Any failure
  (missing Employee, ambiguous Employee, Employee without Department)
  BLOCKS Submit. Department context must never be silently discarded to
  reach an unrelated global Policy.

Once Department resolves successfully, legitimate fallback through Tiers
1-4 above remains allowed and is not itself an error.

## DEC-046 — NEED Amount-Based Routing Extension (M21)

**Status:** LOCKED.

NEED Approval Cycle creation (`light.ir.approval.cycle._create_cycle`,
unchanged engine method) may now receive an amount basis, computed and
supplied by `light.internal.request.action_submit()`:

- Estimated Line Amount = `requested_qty x price_estimate`
- Estimated Submission Amount = SUM of Estimated Line Amount across all
  requirement lines of the Internal Request

This aggregate is the amount passed to NEED cycle creation. Threshold
selection is IR-aggregate-based, never per-line: an amount that crosses a
threshold only when lines are summed still resolves to the tier matching
the aggregate, not to any individual line's own value.

Existing Approval Rule threshold semantics are unchanged and reused as-is:
`threshold_min <= amount < threshold_max`; an empty `threshold_max` means
unbounded. Worked boundary examples:

| Amount | Tier |
|---|---|
| 4,999,999 | [0, 5,000,000) |
| 5,000,000 | [5,000,000, 10,000,000) |
| 9,999,999 | [5,000,000, 10,000,000) |
| 10,000,000 | [10,000,000, 50,000,000) |
| 49,999,999 | [10,000,000, 50,000,000) |
| 50,000,000 | [50,000,000, infinity) |

This does not introduce a third Approval Purpose. NEED remains NEED
(DEC-010); using an estimated monetary amount to select the NEED
authority chain does not turn NEED into FINANCIAL.

## Change Control

A coding agent must not silently alter a LOCKED decision.

If implementation reveals a conflict:

1. Stop the affected implementation.
2. Identify affected DEC/FR/NFR/TR/ADR IDs.
3. Report the conflict.
4. Propose resolution options.
5. Obtain an approved baseline change where required.
