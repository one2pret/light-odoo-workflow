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

### Scope Clarification

M13 implements the stock-execution derivation capability for Delivered
Quantity required by TR-STK-005 so that M14 can consume authoritative
delivery results.

This supporting implementation does not move functional completion of
FR-IR-091 from M14. FR-IR-091 remains implemented, integrated, tested and
accepted in M14 according to the RTM.

## M14 — Acceptance Engine

Create `light.ir.acceptance.event`.

Support:
- manual acceptance
- partial acceptance
- receiver authorization
- service acceptance without Stock Move
- reversal (corrective, immutable event history)

Scope Clarification: M14 implements Manual Acceptance and Reversal
mechanics only. FR-IR-093 is partially implemented — Manual mode
exists; policy-based mode selection is not. Automatic Acceptance
(FR-IR-100, FR-IR-101) is explicitly deferred: the baseline does not
define a deterministic trigger mechanism, automatic actor/authority,
"Not Required" completion semantics, or the interaction between an
automatic processor and a user-initiated Reversal. These require an
approved design decision before implementation, not invention during
coding.

Requirements:
- FR-IR-091..101
- TR-ACC-001, TR-ACC-002, TR-ACC-003, TR-ACC-004, TR-ACC-006, TR-ACC-010 (implemented)
- TR-ACC-005 (deferred — Automatic Acceptance)
- TR-ACC-007 is out of M14 scope (Exception/Cancellation boundary, M15/M16)
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
- TR-ACC-007, TR-ACC-008, TR-ACC-009, TR-ACC-010, TR-ACC-011, TR-ACC-012
- ADR-018
- ADR-019
- ADR-028

## M16 — Cancellation

Create `light.ir.cancellation`: a structured, ATOMIC, append-only
business event that removes remaining authorized business requirement.
Adds the canonical `light.internal.request.line.cancelled_qty`
(= SUM of effective Cancellation event quantities) and `outstanding_qty`
(ADR-019: `approved_qty - accepted_qty - cancelled_qty`). Enforces
`accepted_qty + cancelled_qty <= approved_qty` (FR-IR-117; the
invariant itself was already declared in TR-03 before M16 existed,
only enforcement was missing).

Support:
- cancellation request (partial or full — one action,
  `line.action_cancel(quantity, reason)`; full Cancellation is simply
  cancelling the entire current `outstanding_qty`)
- authorization
- effective cancellation

Requirements:
- FR-IR-114..119
- FR-IR-135..138 (partial — quantity/history portion only; IR primary
  state transition is not implemented, see Scope Clarification)

Technical Requirements:
- TR-AUD-003, TR-AUD-009..012, TR-QTY-006 (FR-IR-114..119)
- TR-QTY-005..010, TR-ACC-011 (shared portion of FR-IR-135..139,
  spanning M14/M16/M17/M20 per RTM)

ADRs:
- ADR-016, ADR-019, ADR-020, ADR-022, ADR-027, ADR-028

### Scope Clarification

M16 implements Cancellation as a single ATOMIC event (mirrors the
`light.ir.acceptance.event` shape, M14) — not a stateful multi-phase
record. TR-09's own field list for Cancellation names no
status/resolution fields, unlike M15's Exception.

Cancellation Authority (FR-IR-116, "applicable authorization according
to configured governance") is NOT capability-only (DEC-016 — capability
does not equal transaction authority). Authorization order: LIGHT
Cancellation capability (coarse gate) -> Company boundary -> resolve
the V1 responsibility code `IR_CANCELLATION_AUTHORITY` via the
existing, standalone, purpose-agnostic
`light.ir.responsibility.assignment._resolve()` (TR-10 "Configuration
Domains" lists "Cancellation Authority" as its own configured-
governance domain) -> resolved user must equal the acting user (no
requester fallback) -> the locked M08 readiness gate. This reuses only
the standalone Responsibility resolver: no Approval Cycle is created,
no new Approval Cycle purpose is introduced, and the generic Approval
Engine is not modified (NEED/FINANCIAL remain its only purposes,
ADR-005/DEC-010).

"Downstream resolution" (FR-IR-118) is not implemented as a
route-specific check: M16 core remains route-agnostic (never inspects
`purchase.order`/`purchase.order.line`/`stock.move`/`stock.picking`),
mirroring M14/M15's own route-agnostic precedent. Cancellation changes
LIGHT business-requirement authority only — it never creates, modifies,
or cancels a standard Odoo Purchase or Stock document.

M16 does not transition the IR primary state (to `cancelled` or
`done`): the baseline does not provide a deterministic exact rule
distinguishing fully-accepted/fully-cancelled/mixed completion. That
remains open for a later milestone.

M15's `resolution_type == 'cancel_remaining'` is an optional upstream
intent only, never a mandatory prerequisite for `action_cancel()`.

## M17 — Revision & Reversal

Create `light.ir.revision`: a structured, STATEFUL record (active/
applied/rejected — the minimal lifecycle FR-IR-133's "single active
material Revision" requires) representing a material post-submission
change to an IR Line's requirement.

Support:
- material revision (quantity, Product, Specification)
- impact evaluation (NEED/FINANCIAL reapproval, Fulfillment
  Replanning)
- approval superseding (reuses `light.ir.approval.cycle`'s existing
  `_create_cycle(..., supersedes=...)` mechanism unchanged, the same
  pattern M11 already proved for FINANCIAL)
- company change block (ADR-023)
- single active material revision (FR-IR-133; a genuine DB-level
  partial unique index, not only an ORM-level constrain, is required —
  found necessary during concurrency testing since the Python
  `@api.constrains` alone cannot see a concurrent sibling's write under
  PostgreSQL REPEATABLE READ when the constrain reads a different table
  than the one being row-locked)
- controlled reactivation through valid business events (FR-IR-140
  remains explicitly deferred — M17 does not implement generic reopen
  of `done`/`cancelled` IRs; operates only on `submitted`/`in_process`)

Does NOT reimplement Acceptance Reversal (FR-IR-134): M14's
`light.ir.acceptance.event`/`action_reverse_acceptance()` remains the
sole, unmodified mechanism — M17 preserves and regression-tests that
history only.

Pending-vs-effective semantics: while a Revision is `active`,
`approved_qty`/`product_id`/`description` remain at their current
effective value — proposed values live only on the Revision record.
`requested_qty` is the one documented exception: for a quantity
*increase* specifically, `requested_qty` becomes effective immediately
when NEED reapproval is triggered (not deferred to apply). This is not
a deviation from baseline: TERMINOLOGY #42/TR-03 define `requested_qty`
as the Requester's ask (not "the currently effective authorized
requirement" — that is `approved_qty`'s role, TERMINOLOGY #43), no
FR/TR/ADR/DEC anywhere requires `requested_qty` to stay frozen during a
pending reauthorization, M06's own `action_submit()` already puts the
IR in exactly this state from the moment of Submit (`requested_qty` set
immediately, `approved_qty` remaining 0 for the entire pending NEED
Approval window), and no downstream execution ceiling (Fulfillment
Allocation, Purchase/Stock Attribution, Acceptance, Cancellation,
`outstanding_qty`, Financial Authorization basis) ever reads
`requested_qty` — all read `approved_qty` exclusively. The early move
is additionally structurally required: M06's own locked
`need_pending_qty <= requested_qty` invariant (`_check_approved_qty_
invariant`, unmodified by M17) would otherwise be violated the moment
`need_pending_qty` is primed to a higher proposed ceiling. Reverted
automatically to the prior value if the Revision is later rejected.

Requirements:
- FR-IR-120..134
- TR-AUD-001..012
- ADR-021
- ADR-022
- ADR-023
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

Fixes the one confirmed gap found during the M18 read-only scope
analysis: `light.ir.fulfillment.exception` (M15) had ACL rows but no
company-scoped `ir.rule`, unlike every other LIGHT historical model —
any internal user in any Company could read Fulfillment Exception
records across all Companies (cross-company action was already
blocked; cross-company read was not).

Introduces `group_light_auditor` (FR-IR-165, TR-05, TR-SEC-006):
strictly read-only, company-scoped like every other LIGHT group, no
`implied_ids` to any business capability group, no create/write/
unlink, no business action authority anywhere. Read access is granted
only on the TRANSACTIONAL/HISTORY model set (TR-04's own explicit
"Approval Transaction Models" vs "Approval Configuration Models" split,
extended consistently to the rest of the schema) — never on
configuration/master data (Approval Policy/Rule/Rule Step,
Responsibility Assignment, Delegation, Governance Policy, HR
Responsibility Rule, Request Type). Locked M18 V1 decision: Auditor
receives NO additional access to configuration/master models — a
least-privilege boundary, not a statement that a future governance-
audit requirement may never grant such access.

Hardening only: no locked business semantics changed, no new business
model introduced, no deferred M14/M17 feature (Automatic Acceptance,
terminal-state reopen) implemented here.

Requirements:
- FR-IR-149..178
- TR-SEC-*
- TR-CFG-*
- ADR-007
- ADR-008
- ADR-023
- ADR-024
- ADR-025
- ADR-026

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
- Quantity Monitoring
- All Requests
- Configuration

Built entirely from standard Odoo menus/actions/list/form/search views
and deterministic domains over existing fields — no mail.thread, no
mail.activity.mixin, no client actions, no new business state. Approval
Worklist is deliberately ungated (DEC-016): NEED/FINANCIAL approval
authority is Responsibility-resolved, not group-based, so the domain
filter (`resolved_user_id = uid`) is what makes it personal, and the
underlying engine re-verifies authority regardless of UI visibility.

Locked M19 V1 decision on FR-IR-187 (My Actions): satisfied through the
distributed, per-actor worklists (Approval Worklist, Review Worklist,
Plan Approval Worklist, Pending Acceptance, Exceptions) rather than a
cross-model aggregate view — no new model, field, SQL view, client
action, or dashboard is introduced for it.

FR-IR-193 (Quantity Monitoring) is pure navigation onto the existing
Internal Request Line list/action, already exposing requested/
approved/delivered/accepted/cancelled/outstanding quantities — no new
model, field, compute, business action, ACL, or record rule.

Pending Acceptance uses one non-stored, search-only computed field,
`light.internal.request.line.is_pending_acceptance`
(`delivered_qty > accepted_qty`) — the exact condition, not an
approximation, since M14's own `action_accept()`/
`action_reverse_acceptance()` already enforce
`0 <= accepted_qty <= delivered_qty`. No DB column, no new canonical
quantity.

Requirements:
- FR-IR-186..195
- ADR-007, ADR-020
- TR-SEC-001..007

Do not create a large custom JavaScript dashboard without a new approved requirement.

## M20 — End-to-End Regression & Release Candidate

Closes the FR-IR-136..142/145 IR Completion / Controlled Reactivation gap
that M14/M16/M17 each explicitly left open (see M16's own Scope
Clarification: "spanning M14/M16/M17/M20 per RTM"). Locked as DEC-041.

Completion eligibility, per line: `outstanding_qty == 0`, no open
Fulfillment Exception, no pending Acceptance (`delivered_qty >
accepted_qty`). Terminal outcome once every line is eligible: any
`accepted_qty > 0` (accepted-only or mixed with Cancellation, per
TR-03's own "Cancellation Example") -> `done`; no line with any
`accepted_qty > 0` (cancellation-only) -> `cancelled`. Evaluated
automatically by a single centralized evaluator invoked only from
inside the six already-authorized business actions that can change a
completion-relevant fact (Accept, Reverse Acceptance, Cancel, Report
Exception, Resolve Exception, Apply Revision) — no new Mark Done
action, authority, group, or responsibility. Controlled reactivation
(FR-IR-140) is a side effect of the same three of those six actions
(Acceptance Reversal, Report Exception, an approved Revision increase)
re-opening the predicate on an already-terminal IR — never a generic
Reopen (FR-IR-139/DEC-037 unchanged): no `action_reopen()` exists, no
caller-supplied target state, direct non-`su` `state` writes remain
blocked exactly as before. `_check_revision_authorized()`'s state
guard was narrowly widened to permit `done`/`cancelled` (FR-IR-140
explicitly names Revision as a sanctioned reactivation path); Draft
remains blocked.

Automatic Acceptance (FR-IR-100/101) remains deferred, not implemented
here or anywhere in V1.

Requirements:
- FR-IR-135..145
- FR-IR-196
- ADR-016, ADR-019, ADR-020, ADR-022
- TR-QTY-005..010, TR-ACC-011
- TR-PUR-012, TR-STK-010, TR-TST-001..012
- DEC-041

Known test-infrastructure debt (not a runtime defect): running all
`database_breaking`-tagged concurrency suites together in one process
causes 3 of the pre-existing M15/M16/M17 classes to fail at
`setUpClass`, because each independently commits (via `BaseCase`, no
rollback) its own global, unscoped NEED Approval Policy with no
cleanup — the same `_resolve()` ambiguity guard each is designed to
exercise fires across suites, not within any one of them. M20's own
concurrency test avoids this via a Tier-1 (request-type-scoped)
policy. Not fixed here; would require modifying locked M14-M17 test
files without instruction. Each suite continues to be run individually
per its own documented "run explicitly" intent.

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

## M21 — Department-Aware Amount-Based NEED Approval

Post-V1 addition (light-ir-m20 baseline), closing FR-IR-027's remaining
gap (Department as a full Approval Policy resolution dimension, not just
Department Head responsibility resolution) and FR-IR-017's Submit-time
completeness. Locked as DEC-042..046.

`price_estimate` remains optional while Draft, seeded once from Product
Cost (`product.standard_price`, converted via standard Odoo UoM/currency
mechanisms — never a live approval dependency), overridable by the
Requester, and mandatory (>0, every line) before Submit (DEC-042/043).
Estimated Line Amount (`requested_qty x price_estimate`) and Estimated
Submission Amount (their sum) are new compute+store fields; the
aggregate is passed to the existing, unchanged NEED `_create_cycle()` as
an amount basis, reusing the same tiered `threshold_min <= amount <
threshold_max` engine FINANCIAL already exercised (no new engine, no
third Approval Purpose, DEC-046).

`department_id` is a new immutable submission-time snapshot on
`light.internal.request` (added in `light_ir_hr`, never in core
`light_internal_request` — HR stays isolated per AGENTS.md), resolved
from the Requester's HR Employee and frozen at Submit (DEC-044).
`light.ir.approval.policy` gains an independent, optional `department_id`
dimension with a locked 4-tier precedence against Request Type
(DEC-045). Department/Employee resolution is mandatory (fails closed)
only when a Department-scoped NEED Policy is actually relevant to the
Company/Purpose/Request Type in play — otherwise M20 backward
compatibility is preserved unconditionally, so installing `light_ir_hr`
alone never blocks a Submit that has nothing to do with Department
governance.

Department Head reuses the existing `light.ir.hr.responsibility.rule`
mechanism unchanged (zero new code); Related C-Level/CFO/CEO reuse
static `light.ir.responsibility.assignment` rows (e.g. `GM_CLEVEL`,
`CFO`, `CEO`) — configuration only. `self_approval_skip` is reused
unchanged.

Requirements:
- FR-IR-017, FR-IR-018, FR-IR-027, FR-IR-028
- TR-APR-001..013 (engine unchanged, reused as-is)
- TR-ARC-003 (HR isolation preserved)
- DEC-042, DEC-043, DEC-044, DEC-045, DEC-046 (supersedes DEC-011 at
  Submit time only)

Explicitly out of scope, unchanged: FINANCIAL Tier-1/Tier-2, PO
Commitment Authority, PO ceiling consumption/double-approval, budget-
aware routing, 7-day escalation, shared-account policy, price-change-
after-approval Revision support, a third Approval Purpose.

No historical Department backfill: pre-M21 Internal Requests keep
`department_id = False`.

M21 is complete only when:
- applicable automated tests pass (807 standard LIGHT tests: 769 M20
  baseline + 38 new, 0 failed, 0 errors)
- fresh-install and upgrade-from-`light-ir-m20` both verified clean
- no critical unmapped requirement remains
- no critical unresolved security or architecture defect remains

## M22 — Internal Request & Approval Operational UX

Presentation/UX only, over the existing M21 approval engine. No approval
sequencing, authority, status, Policy resolution, Department routing,
threshold routing, self-approval-skip, ACL, or `ir.rule` change.

Approval Worklist (FR-IR-188) already filtered to `resolved_user_id = uid`;
M22 adds `is_actionable = True` to that domain, so a future
(resolved-but-not-current) step never appears as an actionable task, and
enriches the list with business context (Requester, Department, Request
Type, Estimated Amount, Required Date, friendly Approval Step) via
non-stored `related=` fields on `light.ir.approval.cycle.step` — reusing
the exact IR/Department/amount snapshots M21 already produces, never
re-resolving them. My Requests (FR-IR-186) gains the same Estimated
Amount/Required Date/Department columns, all pre-existing fields on
`light.internal.request`.

A dedicated business-facing form (`view_light_ir_approval_worklist_task_form`)
was added for the same `light.ir.approval.cycle.step` model — no new
transactional model — showing Request Information, Business Purpose
(`light.internal.request.purpose`, deliberately labeled distinctly from the
NEED/FINANCIAL `cycle_purpose`), Requested Items (embedded
`internal_request_id.line_ids`, never flattened), and an Approval Progress
list (embedded `cycle_id.step_ids`) rendering Approved/Current/
Waiting-Queued/Rejected purely from the existing `status` + `is_actionable`
fields — no new approval status. Only this Worklist action's own `view_ids`
was repointed to the new form; the shared audit/admin Cycle Step form
(`light_ir_approval.view_light_ir_approval_cycle_step_form`) is untouched
and keeps its full technical/snapshot terminology for admin/audit use.

Friendly Step Label (e.g. "Department Head" for `GM_HEAD`, "Related
C-Level" for `GM_CLEVEL`) is Rule Step **configuration data**
(`light.ir.approval.rule.step.name`), never a Python code→label mapping;
`responsibility_code` remains the technical routing identity, unchanged.
Existing historical Approval Cycle Step snapshots retain whatever label was
configured at the time they were created (TR-APR-004/005 snapshot
immutability) — only new cycles created after a label edit pick up the new
one.

Combined "IR reference — Step Label" breadcrumb/title is explicitly
**deferred**: it would require a `display_name`/`name_get()` override
(not achievable via view-only XML), out of this presentation-only
milestone's bar.

Requirements: FR-IR-186, FR-IR-188 (extended, not superseded).

Explicitly out of scope, unchanged: FINANCIAL Tier-1/Tier-2, DEF-PU-01, PO
Commitment Authority/ceiling consumption, double-approval redesign,
budget-aware routing, 7-day escalation, Automatic Acceptance,
price-change-after-approval Revision, a third Approval Purpose.

M22 is complete only when:
- focused M22 tests pass (11/11, reusing the same evidence standard as
  prior milestones — no full regression required for a presentation-only
  change)
- manual UAT confirms Worklist/Task/Progress/sequential handoff on a real
  multi-step (Tier 4) request
- no ACL/`ir.rule`/engine/security change

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
