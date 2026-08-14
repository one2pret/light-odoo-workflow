# LIGHT Internal Request
## Functional Requirements Specification

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

## 1. Purpose

This document defines the functional requirements for LIGHT Internal Request V1.

Every functional requirement has a stable identifier:

`FR-IR-nnn`

These identifiers are used by Technical Requirements, ADRs, RTM, tests, and
implementation milestones.

A coding agent must not silently change the meaning of a requirement.

---

# A. Internal Request Creation

## FR-IR-001 — Create Internal Request

The system shall allow an authorized Requester to create an Internal Request.

## FR-IR-002 — Unique IR Number

The system shall assign each Internal Request a unique business reference
according to applicable Company sequence configuration.

## FR-IR-003 — IR Company

Every Internal Request shall belong to one authoritative Company.

## FR-IR-004 — Requester

Every Internal Request shall identify its Requester.

## FR-IR-005 — Organizational Context

The system shall support applicable organizational context, including
Department where available through the approved organizational integration.

## FR-IR-006 — Request Type

Every Internal Request shall identify an applicable configured Request Type.

## FR-IR-007 — Receiver

The system shall support an intended Receiver that may differ from the
Requester.

## FR-IR-008 — Required Date

The system shall support a required date for the Internal Request or its
applicable requirement lines.

## FR-IR-009 — Business Purpose

The system shall allow the Requester to provide a business purpose,
justification, or equivalent request reason.

## FR-IR-010 — Multiple Requirement Lines

One Internal Request shall support one or more requirement lines.

## FR-IR-011 — Goods Requirement

An IR Line shall support Item Nature `Goods`.

## FR-IR-012 — Service Requirement

An IR Line shall support Item Nature `Service`.

## FR-IR-013 — Registered Requirement

An IR Line shall support selection of an existing standard Odoo Product.

## FR-IR-014 — Unregistered Requirement

An IR Line shall support an unregistered requirement when no appropriate
standard Odoo Product has yet been resolved.

## FR-IR-015 — Department Context

Where HR integration is enabled, Department context shall use standard
`hr.department` through the approved LIGHT HR integration boundary.

## FR-IR-016 — Requested Quantity and UoM

Each quantity-based IR Line shall record Requested Quantity and applicable
Unit of Measure.

## FR-IR-017 — Optional Requester Price Estimate

Requester Price Estimate shall be optional by default.

Unknown requester price shall not by itself invalidate a request unless an
explicitly applicable approved policy requires a price basis at that stage.

## FR-IR-018 — Requester Currency Context

Where Requester Price Estimate is provided, the applicable currency context
shall be explicitly known.

## FR-IR-019 — Attachments

The system shall support supporting attachments for the Internal Request.

## FR-IR-020 — Draft Editing

An authorized Requester shall be able to edit their IR while it remains Draft,
subject to security rules.

## FR-IR-021 — Draft Deletion

An authorized user may delete a Draft IR according to applicable security
policy.

Submitted or historically material IRs shall not normally be hard-deleted.

## FR-IR-022 — Submission Validation

Before submission, the system shall validate mandatory data and applicable
business invariants.

## FR-IR-023 — Submit Internal Request

An authorized Requester shall be able to submit a valid Draft IR.

## FR-IR-024 — Submitted Snapshot Context

Submission shall establish the applicable business context required for
subsequent governance, including Company, Requester, Request Type, and
applicable requirement quantities.

---

# B. Need Approval

## FR-IR-025 — Need Approval Purpose

The system shall support approval purpose `NEED` for authorization of the
business requirement.

## FR-IR-026 — Configurable Need Approval Policy

Need Approval routing shall be determined by configured Approval Policy and
Rules rather than hard-coded users.

## FR-IR-027 — Approval Organizational Scope

Need Approval policy resolution shall support applicable organizational scope
such as Department or configured responsibility context.

## FR-IR-028 — Request Type Approval Scope

Need Approval policy resolution shall support Request Type as an applicable
configuration dimension.

## FR-IR-029 — Need Approval Cycle

When Need Approval is required, the system shall create a structured
transactional Approval Cycle.

## FR-IR-030 — Sequential Approval Steps

The Approval Engine shall support sequential approval steps according to the
resolved rule.

## FR-IR-031 — Resolved Approver Snapshot

The system shall preserve the resolved approver/responsibility context for each
historical approval step.

Later master-data changes shall not rewrite historical approval meaning.

## FR-IR-032 — Need Approval Action

Only an authorized resolved approver or valid delegate shall be able to perform
the applicable Need Approval action.

## FR-IR-033 — Need Approval Quantity Reduction

An authorized Need Approver shall be able to reduce the quantity being
authorized, subject to quantity invariants.

## FR-IR-034 — No Approval Quantity Increase

A Need Approval action shall not increase quantity beyond the applicable
previously authorized/requested basis.

## FR-IR-035 — Approved Quantity

The system shall maintain Approved Quantity separately from Requested Quantity.

## FR-IR-036 — Not Approved Is Not Cancellation

The difference between Requested Quantity and Approved Quantity shall not be
recorded as Cancelled Quantity merely because it was not approved.

## FR-IR-037 — Need Rejection

An authorized approver shall be able to reject the applicable Need Approval
according to configured workflow rules.

The rejection shall be auditable.

## FR-IR-038 — Self-Approval Protection

A Requester shall not approve their own applicable approval step by default.

## FR-IR-039 — Self-Resolved Step Handling

Where the configured policy uses self-step skipping, a step resolving to the
Requester shall be skipped rather than self-approved, and the reason shall be
audited.

## FR-IR-040 — Missing Mandatory Approver

If a mandatory approver/responsibility cannot be resolved, the applicable
approval process shall block by default.

## FR-IR-041 — Approval Delegation

The system shall support controlled approval delegation and preserve both the
original authority and actual acting user in audit history.

# C. Review and Fulfillment Planning

## FR-IR-042 — Configurable Review Requirement

The system shall determine whether Review is required according to applicable
configuration.

Review shall not be universally mandatory for every Request Type unless the
applicable policy requires it.

## FR-IR-043 — Review Work Item

When Review is required, the system shall expose the IR to the applicable
authorized reviewer/worklist.

## FR-IR-044 — Reviewer Authorization

Only an applicable authorized reviewer shall be able to complete a required
Review action.

## FR-IR-045 — Review Requirement Validation

Review shall support evaluation of requirement clarity, specification,
product resolution, fulfillment feasibility, and other applicable planning
information.

## FR-IR-046 — Fulfillment Planning

The system shall support structured Fulfillment Planning for Approved
Quantity.

## FR-IR-047 — Purchase Allocation

The system shall support allocation of Approved Quantity to fulfillment type
`purchase`.

## FR-IR-048 — Internal Allocation

The system shall support allocation of eligible Approved Quantity to
fulfillment type `internal`.

## FR-IR-049 — Split Fulfillment

The system shall support splitting an eligible IR Line between Purchase and
Internal Allocation.

## FR-IR-050 — Allocation Quantity Limit

The aggregate effective Fulfillment Allocation for an IR Line shall not exceed
the applicable Approved Quantity.

## FR-IR-051 — Service Internal Fulfillment Restriction

A Service requirement shall not use physical Internal Stock fulfillment unless
a future approved design explicitly defines such behavior.

## FR-IR-052 — Fulfillment Plan Approval

Where applicable configuration requires approval of a Fulfillment Plan, the
system shall enforce that approval before controlled downstream execution.

---

# D. Price Validation and Financial Approval

## FR-IR-053 — Structured Price Validation

The system shall support structured Price Validation separate from Requester
Price Estimate.

## FR-IR-054 — Price Validation History

Price Validation shall preserve applicable validator, timestamp, currency,
price basis, and historical context.

## FR-IR-055 — Financial Approval Purpose

The system shall support approval purpose `FINANCIAL`.

## FR-IR-056 — Financial Approval Basis

Financial Approval shall use an explicitly defined financial basis derived
from applicable validated/commercial information according to policy.

## FR-IR-057 — Financial Threshold Configuration

Financial approval thresholds shall be configurable and shall not be
hard-coded into source code.

## FR-IR-058 — Financial Threshold Tax Basis

Applicable Financial Approval policy shall explicitly determine whether
threshold evaluation uses Untaxed or Tax Included amount.

## FR-IR-059 — Financial Approval Cycle

Where Financial Approval is required, the system shall create a structured
Approval Cycle using the common LIGHT Approval Engine.

## FR-IR-060 — Financial Approver Resolution

Financial approvers shall be resolved from applicable configured authority or
responsibility.

## FR-IR-061 — Financial Approval History

The system shall preserve historical Financial Approval authority, actor,
amount basis, currency, and applicable threshold context.

## FR-IR-062 — Financial Reauthorization

A material financial change that invalidates existing authorization shall
require applicable reauthorization before controlled commitment.

## FR-IR-063 — No Financial Self-Approval

Financial Approval shall follow the same self-approval protection principles
as other LIGHT approval purposes unless an explicitly approved policy states
otherwise.

---

# E. Purchase and Sourcing

## FR-IR-064 — Standard Odoo Purchase

Actual procurement execution shall use standard Odoo Purchase.

LIGHT shall not implement a duplicate Purchase Order engine.

## FR-IR-065 — Eligible Purchase Allocation

Only applicable authorized Purchase Allocation quantity shall be eligible for
LIGHT procurement attribution.

## FR-IR-066 — Create RFQ From Eligible Requirement

An authorized Procurement user shall be able to initiate RFQ sourcing from
eligible Purchase Allocation.

## FR-IR-067 — Multiple Vendor Quotations

The system shall support multiple vendor quotations for the same underlying
Purchase Allocation.

## FR-IR-068 — Sourcing Does Not Multiply Requirement

Competing RFQ quantities shall not multiply or consume the authorized Purchase
Allocation merely because multiple vendors are being evaluated.

## FR-IR-069 — Vendor Award

The system shall support identifying the selected sourcing outcome/award used
for intended procurement commitment.

## FR-IR-070 — Split Vendor Award

An eligible Purchase Allocation may be awarded across more than one vendor,
provided aggregate awarded/committed quantity does not exceed the authorized
basis.

## FR-IR-071 — Multiple IR Consolidation

Compatible Purchase requirements from multiple IRs may be consolidated into a
common RFQ, PO, or PO Line where applicable.

## FR-IR-072 — One IR Multiple Purchase Transactions

One IR requirement may be fulfilled through multiple Purchase Orders or PO
Lines.

## FR-IR-073 — Procurement Compatibility Validation

The system shall validate applicable compatibility before consolidating
Purchase requirements, including Company and other required commercial/product
dimensions.

## FR-IR-074 — Explicit Purchase Attribution

The system shall maintain explicit quantity-aware attribution between LIGHT
Purchase Allocation/IR requirements and standard Odoo Purchase Order Lines.

Free-text `purchase.order.origin` shall not be the authoritative source of
LIGHT attribution.

---

# F. PO Financial Commitment

## FR-IR-075 — LIGHT-Linked PO Identification

The system shall distinguish Purchase Orders having valid LIGHT attribution
from ordinary non-LIGHT Purchase Orders.

## FR-IR-076 — PO Commitment Authority

Before applicable commercial commitment of a LIGHT-linked PO, the system shall
validate the required PO Commitment Authority.

## FR-IR-077 — Consolidated Commitment Evaluation

Where multiple LIGHT requirements are consolidated into a Purchase Order, the
applicable commitment authority shall consider the actual relevant consolidated
commitment rather than treating source IRs as an artificial threshold split.

## FR-IR-078 — Non-LIGHT Purchase Preservation

Purchase Orders without applicable LIGHT linkage shall retain standard Odoo
Purchase behavior and shall not be subjected to unrelated LIGHT approval
requirements.

# G. Internal Stock Fulfillment

## FR-IR-079 — Standard Odoo Inventory

Actual stock execution shall use standard Odoo Inventory.

LIGHT shall not implement a duplicate Inventory engine.

## FR-IR-080 — Eligible Internal Allocation

Only applicable authorized Internal Allocation quantity shall be eligible for
LIGHT internal stock execution.

## FR-IR-081 — Explicit Stock Attribution

The system shall maintain explicit quantity-aware attribution between LIGHT
Internal Allocation and applicable standard Odoo Stock Moves.

## FR-IR-082 — Create Internal Transfer

An authorized user shall be able to initiate an applicable standard Odoo
Internal Transfer from eligible Internal Allocation.

## FR-IR-083 — Multiple Transfers Per IR

One IR requirement may be fulfilled through multiple Stock Moves/Transfers.

## FR-IR-084 — Consolidated Internal Transfer

Compatible Internal Allocations from multiple IRs may be consolidated into
common stock execution where applicable.

## FR-IR-085 — Internal Transfer Compatibility

Consolidation shall validate applicable Company, source/destination, Product,
UoM, and other required stock compatibility.

## FR-IR-086 — Partial Stock Execution

The system shall support partial internal stock execution without falsely
completing the entire attributed IR requirement.

## FR-IR-087 — Backorder Attribution

When standard Odoo creates a backorder for LIGHT-attributed execution,
remaining attribution shall be preserved deterministically.

## FR-IR-088 — Stock Quantity Integrity

Effective stock attribution shall not exceed the applicable eligible Internal
Allocation.

## FR-IR-089 — Lot and Serial Authority

Where lot/serial tracking applies, standard Odoo Inventory shall remain
authoritative for lot/serial execution.

## FR-IR-090 — Non-LIGHT Stock Preservation

Stock transactions without applicable LIGHT linkage shall retain standard Odoo
Inventory behavior.

---

# H. Delivery and Acceptance

## FR-IR-091 — Delivered Quantity

The system shall derive or maintain Delivered Quantity from applicable
structured downstream execution and attribution.

## FR-IR-092 — Delivery Is Not Acceptance

Delivered Quantity and Accepted Quantity shall remain separate business
concepts.

## FR-IR-093 — Acceptance Policy

The system shall support configurable Acceptance behavior.

V1 modes shall include:

- Manual
- Automatic
- Not Required

## FR-IR-094 — Manual Acceptance Default

Manual Acceptance shall be the default V1 behavior unless an applicable
policy specifies otherwise.

## FR-IR-095 — Acceptance Event

Business Acceptance shall be represented by a structured Acceptance Event.

## FR-IR-096 — Acceptance Actor

An Acceptance Event shall preserve the actual authorized actor and timestamp.

## FR-IR-097 — Receiver Authorization

Manual Acceptance shall validate that the acting user has applicable authority
to accept for the IR/Receiver context.

## FR-IR-098 — Partial Acceptance

The system shall support partial Acceptance.

## FR-IR-099 — Acceptance Quantity Limit

Accepted Quantity shall not exceed the quantity eligible for Acceptance under
the applicable fulfillment context.

## FR-IR-100 — Automatic Acceptance

Where applicable policy uses Automatic Acceptance, the system shall create the
corresponding structured Acceptance Event automatically.

## FR-IR-101 — Automatic Acceptance Idempotency

Repeated processing of the same eligible automatic-acceptance trigger shall
not duplicate the effective Accepted Quantity.

---

# I. Fulfillment Exceptions

## FR-IR-102 — Create Fulfillment Exception

An authorized user shall be able to record a Fulfillment Exception against
applicable fulfillment.

## FR-IR-103 — Exception Quantity

Where quantity is applicable, a Fulfillment Exception shall identify the
affected quantity.

## FR-IR-104 — Exception Reason

The system shall support structured exception reasons such as damaged, wrong
product, wrong specification, shortage, quality issue, service incomplete, or
other configured reason.

## FR-IR-105 — Exception Is Not Cancellation

Recording a Fulfillment Exception shall not automatically increase Cancelled
Quantity.

## FR-IR-106 — Exception Resolution

The system shall support controlled resolution of an Exception.

## FR-IR-107 — Replacement Resolution

An Exception may be resolved through Replacement where applicable.

## FR-IR-108 — Return and Replace Resolution

An Exception may be resolved through applicable standard Odoo return execution
followed by replacement where required.

## FR-IR-109 — Redelivery Resolution

The system shall support Redelivery as an applicable exception-resolution
outcome.

## FR-IR-110 — Accept As Is Resolution

An authorized business actor may resolve an applicable Exception by accepting
the fulfillment as-is according to policy.

## FR-IR-111 — Cancel Remaining Resolution

An applicable Exception may result in controlled cancellation of remaining
requirement quantity through the Cancellation process.

## FR-IR-112 — No Replacement Double Counting

Replacement or Redelivery shall not create duplicate authorized requirement
quantity.

## FR-IR-113 — Service Exception

Fulfillment Exception and resolution shall support Service requirements
without requiring Stock Moves.

---

# J. Cancellation

## FR-IR-114 — Cancellation Request

The system shall support controlled request for partial or full Cancellation
of remaining authorized requirement.

## FR-IR-115 — Cancellation Reason

Cancellation shall require an auditable business reason.

## FR-IR-116 — Cancellation Authority

Effective Cancellation shall require applicable authorization according to
configured governance.

## FR-IR-117 — Cancellation Quantity Limit

Cancelled Quantity shall not exceed the quantity eligible for Cancellation.

## FR-IR-118 — Downstream Cancellation Resolution

Where downstream Purchase or Stock execution already exists, the system shall
require applicable downstream resolution before treating the affected
Cancellation as fully effective where necessary.

## FR-IR-119 — Cancellation History

Cancellation shall preserve structured historical quantity, actor, reason,
timestamp, and applicable authorization context.

# K. Revision and Reversal

## FR-IR-120 — Material Revision

The system shall support formal Revision for applicable material changes after
submission.

## FR-IR-121 — Draft Change Is Not Revision

Normal changes while the IR remains Draft shall not require formal Revision.

## FR-IR-122 — Revision Reason

A material Revision shall require an auditable reason.

## FR-IR-123 — Revision Snapshot

Revision processing shall preserve the applicable before/after business
context required for audit.

## FR-IR-124 — Revision Impact Evaluation

The system shall evaluate the impact of a material Revision on existing
authorization, fulfillment planning, and downstream execution.

## FR-IR-125 — Quantity Increase Reapproval

An applicable post-submission quantity increase shall require reauthorization
where existing approval does not cover the increased requirement.

## FR-IR-126 — Revision Below Realized Quantity

A Revision shall not reduce an authorized requirement below already realized
business quantities in a manner that corrupts historical integrity.

## FR-IR-127 — Product Change

An applicable post-submission Product change shall be treated as a material
Revision.

## FR-IR-128 — Specification Change

A significant post-submission Specification change shall be treated as a
material Revision where it changes the authorized business requirement.

## FR-IR-129 — Financial Basis Change

A material change to the financial basis shall trigger applicable financial
impact evaluation and reauthorization.

## FR-IR-130 — Fulfillment Replanning

A material Revision shall trigger Fulfillment Replanning where existing
allocations are no longer valid.

## FR-IR-131 — Superseded Approval

Where reapproval is required, previous Approval Cycles shall remain historical
and shall be superseded rather than destructively rewritten.

## FR-IR-132 — Company Change Block

Company shall not be changed through normal post-submission Revision in V1.

## FR-IR-133 — Single Active Material Revision

The system shall prevent conflicting simultaneous active material Revisions
for the same applicable IR context.

## FR-IR-134 — Acceptance Reversal

The system shall support structured reversal/correction of finalized
Acceptance without destructively rewriting the original Acceptance Event.

---

# L. Completion and Outcome

## FR-IR-135 — Outstanding Quantity

The system shall calculate canonical Outstanding Quantity as:

`approved_qty - accepted_qty - cancelled_qty`

## FR-IR-136 — Completion Eligibility

An IR Line shall be eligible for completion when its applicable Outstanding
Quantity is zero and no blocking business process remains unresolved.

## FR-IR-137 — IR Completion

An Internal Request shall become Done only when all applicable lines satisfy
completion criteria.

## FR-IR-138 — Cancellation Completion

A fully cancelled IR may reach the applicable terminal cancellation outcome
when required downstream resolution and authorization are complete.

## FR-IR-139 — No Generic Reopen

The system shall not provide an unrestricted generic Reopen action for
terminal IRs.

## FR-IR-140 — Controlled Reactivation

A terminal IR may become active again only through an applicable controlled
business event such as authorized Revision, Reversal, or Exception Resolution.

## FR-IR-141 — Completion Is Business Outcome

PO confirmation, receipt completion, or Stock Move completion alone shall not
be sufficient to mark an IR Done where required Acceptance or other business
outcomes remain pending.

## FR-IR-142 — Completion Outcome Visibility

The system shall expose the applicable completion outcome/status to authorized
users.

## FR-IR-143 — Blocking Exception Prevents Completion

An unresolved blocking Fulfillment Exception shall prevent applicable
completion.

## FR-IR-144 — Pending Acceptance Prevents Completion

Where Acceptance is required, pending eligible Acceptance shall prevent
applicable completion.

## FR-IR-145 — Accepted Plus Cancelled Completion

Where:

`accepted_qty + cancelled_qty = approved_qty`

and no blocking process remains, the applicable IR Line may satisfy its
business quantity completion condition.

---

# M. State and Process Status

## FR-IR-146 — Primary IR State

The system shall use a compact primary IR state model.

Canonical V1 states:

- Draft
- Submitted
- In Process
- Done
- Cancelled

## FR-IR-147 — Subprocess Status

Detailed process progress shall be represented using dedicated subprocess
status rather than uncontrolled expansion of the primary IR state.

## FR-IR-148 — State Transition Validation

Primary state transitions shall be controlled by applicable server-side
business rules.

---

# N. Configuration and Master Governance

## FR-IR-149 — Request Type Configuration

Authorized administrators shall be able to configure Request Types.

## FR-IR-150 — Responsibility Assignment

The system shall support configurable responsibility assignment for governance
roles used in policy resolution.

## FR-IR-151 — Company-Scoped Governance

Governance configuration shall support Company scope where applicable.

## FR-IR-152 — Organizational Governance Scope

Governance configuration shall support applicable organizational scope,
including Department/responsibility context where enabled.

## FR-IR-153 — Effective-Dated Policy

Governance-critical policies shall support applicable effective-date
management.

## FR-IR-154 — Approval Threshold Range

Approval Rules shall support configured financial threshold ranges where
applicable.

## FR-IR-155 — Threshold Boundary Determinism

Threshold ranges shall resolve deterministically and shall not produce
ambiguous authority at boundary values.

## FR-IR-156 — Missing Critical Configuration

Missing required governance-critical configuration shall block the applicable
business action.

## FR-IR-157 — Ambiguous Critical Configuration

Ambiguous governance-critical configuration shall block rather than silently
selecting an arbitrary policy.

## FR-IR-158 — No Arbitrary Policy Code

Business administrators shall not require arbitrary Python/eval scripting to
define ordinary LIGHT governance policy.

---

# O. Security and Authority

## FR-IR-159 — Requester Capability

The system shall provide an appropriate Requester capability/security role.

## FR-IR-160 — Approver Capability

The system shall provide an appropriate approval capability/security role
without treating group membership alone as transaction authority.

## FR-IR-161 — Reviewer Capability

The system shall provide an appropriate Reviewer capability/security role.

## FR-IR-162 — Procurement Capability

The system shall provide an appropriate Procurement capability/security role.

## FR-IR-163 — Internal Fulfillment Capability

The system shall provide an appropriate Internal Fulfillment
capability/security role.

## FR-IR-164 — Receiver / Acceptor Capability

The system shall support applicable Receiver/Acceptor authorization for
business Acceptance.

## FR-IR-165 — Auditor Capability

The system shall support an Auditor capability with read-only access to
authorized audit information.

## FR-IR-166 — LIGHT Administrator Capability

The system shall support a LIGHT Administrator capability for authorized
configuration and administration.

Administrator capability shall not automatically grant business approval
authority.

## FR-IR-167 — Server-Side Authorization

Critical business actions shall enforce authorization server-side.

## FR-IR-168 — Company Record Isolation

Applicable LIGHT records shall enforce authorized Company visibility and
access.

## FR-IR-169 — Cross-Company Link Prevention

The system shall block unauthorized or invalid cross-company transaction
relationships.

## FR-IR-170 — Delegation Validity

Delegated authority shall only be valid within its configured validity period
and applicable scope.

## FR-IR-171 — Expired Delegation Block

An expired or otherwise invalid delegation shall not authorize an approval
action.

## FR-IR-172 — Historical Record Protection

Finalized governance/audit records shall be protected from unauthorized
destructive modification or deletion.

## FR-IR-173 — Configuration Authority Separation

Authority to configure governance shall be distinct from authority to perform
business approval unless both authorities are explicitly granted.

## FR-IR-174 — Unauthorized Approval Block

A user who is not the applicable resolved approver or valid delegate shall not
be able to perform the approval action.

## FR-IR-175 — Unauthorized Review Block

A user without applicable Review authority shall not be able to complete a
required Review action.

## FR-IR-176 — Unauthorized Acceptance Block

A user without applicable Acceptance authority shall not be able to create the
applicable manual Acceptance.

## FR-IR-177 — Unauthorized Procurement Action Block

LIGHT-specific procurement actions shall validate applicable Procurement
authority.

## FR-IR-178 — Unauthorized Fulfillment Action Block

LIGHT-specific internal fulfillment actions shall validate applicable
fulfillment authority.

---

# P. Audit and Traceability

## FR-IR-179 — Structured Critical Audit

Critical LIGHT business decisions and events shall be stored in structured,
queryable records.

## FR-IR-180 — Audit Actor and Timestamp

Critical audit events shall preserve applicable actor and server-determined
timestamp.

## FR-IR-181 — Audit Reason

Events requiring a business reason, including applicable Revision,
Cancellation, Rejection, Exception, or Reversal, shall preserve that reason.

## FR-IR-182 — Historical Approval Traceability

Authorized users shall be able to trace historical approval cycles and their
resolved actors/authority.

## FR-IR-183 — Purchase Traceability

Authorized users shall be able to navigate between applicable IR requirements
and attributed Purchase transactions.

## FR-IR-184 — Stock Traceability

Authorized users shall be able to navigate between applicable IR requirements
and attributed Stock execution.

## FR-IR-185 — Acceptance Traceability

Authorized users shall be able to trace applicable Acceptance, Reversal, and
Exception history.

---

# Q. Operational Worklists and Monitoring

## FR-IR-186 — My Requests

The system shall provide an authorized user with an operational view of their
applicable Internal Requests.

## FR-IR-187 — My Actions

The system shall provide authorized users with visibility of applicable
pending actions assigned/resolved to them.

## FR-IR-188 — Approval Worklist

Authorized approvers shall be able to view applicable pending approval work.

## FR-IR-189 — Review Worklist

Authorized reviewers shall be able to view applicable pending Review work.

## FR-IR-190 — Fulfillment Worklists

Authorized Procurement and Internal Fulfillment users shall be able to view
applicable pending fulfillment work.

## FR-IR-191 — Acceptance Worklist

Authorized Receivers/Acceptors shall be able to view applicable pending
Acceptance work.

## FR-IR-192 — Exception Monitoring

Authorized users shall be able to identify applicable unresolved Fulfillment
Exceptions.

## FR-IR-193 — Quantity Monitoring

Authorized users shall be able to view applicable Requested, Approved,
Allocated, Delivered, Accepted, Cancelled, and Outstanding quantities.

## FR-IR-194 — Downstream Document Navigation

Authorized users shall be able to navigate to applicable attributed RFQ/PO
and Stock execution documents subject to Odoo security.

## FR-IR-195 — Standard Odoo Activity Integration

The system should use standard Odoo activities/worklist mechanisms where
appropriate instead of requiring a separate custom task engine.

## FR-IR-196 — Non-LIGHT Regression Preservation

LIGHT functionality shall not intentionally alter unrelated standard Odoo
Purchase, Inventory, or other business transactions that have no applicable
LIGHT relationship.

---

# Functional Baseline Statement

`FR-IR-001` through `FR-IR-196` constitute the LIGHT Internal Request V1
Functional Requirements baseline.

Implementation must maintain traceability from these requirements through
Technical Requirements, ADRs where applicable, automated tests, milestones,
and implementation.

If implementation reveals a contradiction, the affected implementation must
stop and the baseline conflict must be reported rather than silently resolved
by changing requirement semantics.
