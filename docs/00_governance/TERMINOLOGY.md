# LIGHT Internal Request — Canonical Terminology

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED

This document defines canonical terminology for LIGHT Internal Request V1.

Implementation, requirements, tests, UI labels, and technical documentation
must use these terms consistently unless an approved change explicitly
supersedes them.

---

## 1. Internal Request (IR)

An Internal Request (IR) is the LIGHT business document used by an internal
requester to request goods or services for organizational needs.

An IR is not:

- a Purchase Order;
- an RFQ;
- a Stock Transfer;
- a Vendor Bill;
- a generic approval request.

An IR may eventually be fulfilled through Purchase, Internal Fulfillment,
or a controlled combination of both.

Canonical model:

`light.internal.request`

---

## 2. IR Line

An IR Line represents one requested goods/service requirement inside an IR.

Canonical model:

`light.internal.request.line`

An IR may contain multiple IR Lines.

Quantity authorization, allocation, execution, acceptance, cancellation,
and outstanding quantity are tracked at the appropriate line level.

---

## 3. Requester

Requester is the user/person who creates or owns the business request.

Requester is not automatically:

- approver;
- reviewer;
- procurement officer;
- receiver;
- financial approver.

Requester Price Estimate is optional while Draft; mandatory and greater
than zero on every line before Submit (M21, DEC-042 — supersedes DEC-011
at Submit time only).

A requester is not expected to know the final procurement price, but may
be seeded a Product Cost-derived default while Draft (DEC-043) and may
override it.

---

## 4. Receiver

Receiver is the intended business recipient or responsible recipient of the
requested goods/service.

Receiver may participate in Acceptance according to applicable policy.

Receiver is not necessarily the Requester.

---

## 5. Request Type

Request Type classifies the business purpose/type of Internal Request and may
participate in configurable governance resolution.

Examples may include organization-specific request classifications.

Request Type must not hard-code approvers.

Approval/review behavior is resolved through configuration.

Canonical configuration model:

`light.ir.request.type`

---

## 6. Item Nature

Item Nature identifies whether the requirement represents:

- Goods
- Service

Item Nature is distinct from Request Type.

Goods may be fulfilled through Purchase and/or Internal Stock depending on
eligibility.

Service does not require a Stock Move merely to satisfy LIGHT fulfillment.

---

## 7. Registered Requirement

A Registered Requirement refers to a requirement represented by an existing
standard Odoo Product.

Authoritative Odoo models:

- `product.product`
- `product.template`

LIGHT must not create a duplicate product master.

---

## 8. Unregistered Requirement

An Unregistered Requirement represents a request that is not yet associated
with a registered Odoo Product.

It allows the business requirement to be captured without forcing the
Requester to create Product master data.

Subsequent procurement/master-data handling must follow authorized business
processes.

---

## 9. Requester Price Estimate

Requester Price Estimate is an estimate entered by the Requester, optional
while Draft and mandatory (greater than zero, every line) before Submit
(M21, DEC-042).

It is not authoritative procurement pricing.

Unknown price at request time is valid while Draft; it must be resolved
(entered manually, or seeded from Product Cost per DEC-043 and optionally
overridden) before Submit.

---

## 10. Reference Price

Reference Price is a price used as supporting information for evaluation.

It may originate from an authorized reference source.

Reference Price is not automatically the final vendor commitment price.

---

## 11. Validated Price

Validated Price is a structured price basis validated by an authorized role
or process.

It may become an input to Financial Approval according to applicable policy.

Validated Price is distinct from:

- Requester Price Estimate;
- Vendor Quotation;
- final PO commitment.

---

## 12. Price Status

Price Status represents the system/process understanding of price maturity.

The Requester is not required by default to manually determine a technical
Price Status that they cannot reasonably know.

Price-related status should be derived or maintained by the applicable
business process wherever possible.

---

## 13. Need Approval

Need Approval authorizes whether the requested business need, including the
applicable approved quantity, may proceed.

Approval purpose:

`NEED`

Need Approval is distinct from Financial Approval.

Need Approval may reduce quantity.

A Need Approver must not increase quantity beyond the currently authorized
basis through an approval action.

---

## 14. Financial Approval

Financial Approval authorizes financial exposure/value according to
configured authority.

Approval purpose:

`FINANCIAL`

Need Approval and Financial Approval use the same configurable Approval
Engine but different approval purposes.

---

## 15. Approval Policy

Approval Policy is governance configuration used to determine which approval
rules apply for a business context.

Policy resolution may consider configured dimensions such as:

- Company;
- organizational scope;
- Request Type;
- approval purpose;
- effective date.

Governance-critical policy resolution must be deterministic.

---

## 16. Approval Rule

Approval Rule defines applicable approval behavior, including configured
threshold conditions and approval steps.

Rules are configuration, not historical transaction records.

---

## 17. Approval Step

Approval Step is one configured stage in an Approval Rule.

Approver resolution must use configured responsibility/authority and must not
depend on hard-coded named users.

---

## 18. Approval Cycle

Approval Cycle is the transactional snapshot of an approval process resolved
for a specific business transaction.

Canonical model:

`light.ir.approval.cycle`

Historical Approval Cycles preserve their resolved meaning even if master
configuration changes later.

---

## 19. Approval Cycle Step

Approval Cycle Step is the historical transactional representation of a
resolved Approval Step.

It records applicable responsibility, resolved approver context, status,
actor, timestamp, and other required audit information.

---

## 20. Responsibility

Responsibility is a configurable business authority/responsibility used to
resolve an approver or other governance actor.

Examples may conceptually represent:

- Department Head;
- C-Level responsibility;
- CFO responsibility;
- CEO responsibility.

These are responsibilities, not hard-coded usernames.

---

## 21. Delegation

Delegation temporarily permits an authorized delegate to act for an original
responsibility holder according to configured validity and scope.

Audit must preserve:

- original approver/responsibility;
- actual actor;
- delegation context.

Delegation does not erase the original authority chain.

---

## 22. Self-Approval

Self-Approval occurs when the person requesting/owning a transaction would
also resolve as an approver for the same approval step.

Self-approval is forbidden by default.

Where policy specifies a valid self-step skip behavior, the skip must be
explicit and auditable.

A capability/security group alone never grants unrestricted self-approval.

---

## 23. Review

Review is a business evaluation step used where applicable before or during
fulfillment planning.

Review applicability must be configurable.

Not every Request Type must require Review.

Review is distinct from Need Approval and Financial Approval.

---

## 24. Fulfillment Planning

Fulfillment Planning determines how an approved requirement is intended to be
fulfilled.

V1 fulfillment allocation types:

- Purchase
- Internal

A line may use one or both where allowed.

Planning is not execution.

---

## 25. Fulfillment Allocation

Fulfillment Allocation is the structured quantity plan that assigns approved
requirement quantity to a fulfillment route.

Canonical model:

`light.ir.fulfillment.allocation`

V1 allocation types:

`purchase`

`internal`

Allocation is distinct from Purchase Order and Stock Move.

---

## 26. Purchase Allocation

Purchase Allocation is the quantity of an approved requirement planned for
procurement.

Purchase Allocation is not:

- RFQ quantity;
- Purchase Order;
- Purchase Order Line;
- vendor commitment.

---

## 27. Internal Allocation

Internal Allocation is the quantity of an approved requirement planned for
internal stock fulfillment.

Internal Allocation is not a Stock Move.

---

## 28. Sourcing

Sourcing is the procurement process of obtaining/evaluating vendor
quotations before commitment.

Multiple RFQs may represent the same authorized requirement.

Therefore quotation quantities must not multiply the authorized Purchase
Allocation.

---

## 29. RFQ

RFQ is a standard Odoo Purchase document in quotation stage used for vendor
sourcing.

Authoritative standard model:

`purchase.order`

An RFQ candidate is not automatically a committed fulfillment quantity.

---

## 30. Vendor Award

Vendor Award is the sourcing decision that determines which vendor
quotation/quantity is selected for intended procurement commitment.

Award semantics must prevent multiple competing quotations from consuming
the same authorized requirement multiple times.

---

## 31. Purchase Attribution

Purchase Attribution is the explicit, quantity-aware relationship between
LIGHT Purchase Allocation/IR requirements and standard Odoo Purchase Order
Lines.

Canonical LIGHT model:

`light.ir.purchase.attribution`

It supports:

- one IR to multiple PO lines/orders;
- multiple IRs to one PO line.

`purchase.order.origin` is informational only and is not authoritative
attribution.

---

## 32. PO Commitment

PO Commitment represents actual commercial commitment through standard Odoo
Purchase according to applicable confirmation/commitment semantics.

For LIGHT-linked procurement, commitment must satisfy applicable LIGHT
financial authority.

A merged PO may require higher authority than each source IR individually.

---

## 33. PO Commitment Authority

PO Commitment Authority is the applicable authority required for the actual
commercial commitment represented by a LIGHT-linked PO.

It exists to prevent threshold circumvention, including approval splitting.

It is distinct from merely having individual IR Financial Approval.

---

## 34. Stock Attribution

Stock Attribution is the explicit quantity-aware relationship between LIGHT
Internal Allocation/IR requirements and actual standard Odoo stock execution.

Primary execution-level object:

`stock.move`

Canonical LIGHT relation:

`light.ir.stock.attribution`

---

## 35. Delivery

Delivery represents applicable operational/physical fulfillment execution.

Delivery is not Acceptance.

A quantity may be Delivered but not yet Accepted.

---

## 36. Acceptance

Acceptance is the business acknowledgement that an eligible delivered or
service quantity is accepted for the Internal Request.

Canonical event model:

`light.ir.acceptance.event`

Acceptance modes may include:

- Manual
- Automatic
- Not Required

Default mode is Manual unless applicable policy says otherwise.

Automatic Acceptance must still produce structured, auditable events and
must be idempotent.

---

## 37. Fulfillment Exception

Fulfillment Exception records a business problem with fulfillment.

Examples:

- damaged;
- wrong product;
- wrong specification;
- shortage;
- quality issue;
- incomplete service.

Exception is not Cancellation.

An Exception requires controlled resolution.

---

## 38. Replacement

Replacement is a resolution path where deficient fulfillment is replaced or
redelivered.

Replacement execution must not double-count the original authorized
requirement.

---

## 39. Cancellation

Cancellation is an authorized reduction of the remaining business
requirement.

Cancellation is distinct from:

- quantity not approved during Need Approval;
- Fulfillment Exception;
- rejection;
- delivery shortage.

Cancellation may be partial or full.

---

## 40. Revision

Revision is the formal mechanism for material post-submission change.

Draft edits are not formal Revisions.

Revision preserves historical authorization and may trigger:

- impact evaluation;
- reapproval;
- replanning;
- superseding approval cycles.

Company change after submission is not a normal Revision in V1.

---

## 41. Reversal

Reversal is a structured corrective event used to offset or correct a
finalized historical business event without destructively rewriting it.

Example:

Acceptance +10

followed by:

Acceptance Reversal -2

rather than overwriting the original Acceptance to 8.

---

## 42. Requested Quantity

Canonical field concept:

`requested_qty`

Quantity originally requested by the Requester.

---

## 43. Approved Quantity

Canonical field concept:

`approved_qty`

Quantity authorized by Need Approval.

Approved Quantity may be lower than Requested Quantity.

The difference between Requested and Approved Quantity is not automatically
Cancellation.

---

## 44. Internal Allocated Quantity

Canonical field concept:

`internal_allocated_qty`

Approved quantity assigned to Internal Fulfillment.

---

## 45. Purchase Allocated Quantity

Canonical field concept:

`purchase_allocated_qty`

Approved quantity assigned to Purchase Fulfillment.

---

## 46. Allocated Quantity

Canonical field concept:

`allocated_qty`

Aggregate quantity allocated across applicable fulfillment routes.

Where both routes apply:

`allocated_qty = internal_allocated_qty + purchase_allocated_qty`

subject to applicable business invariants.

---

## 47. Committed Quantity

Canonical field concept:

`committed_qty`

Quantity represented by applicable committed downstream execution according
to the defined Purchase/fulfillment semantics.

Sourcing quotation quantity alone is not Committed Quantity.

---

## 48. Delivered Quantity

Canonical field concept:

`delivered_qty`

Quantity operationally delivered/executed and eligible for downstream
business outcome processing.

Delivered Quantity is not Accepted Quantity.

---

## 49. Accepted Quantity

Canonical field concept:

`accepted_qty`

Aggregate quantity represented by valid Acceptance events net of applicable
Reversal events.

It must not be freely edited as authoritative history.

---

## 50. Cancelled Quantity

Canonical field concept:

`cancelled_qty`

Quantity formally cancelled through authorized Cancellation.

Not-approved quantity is not Cancelled Quantity.

Exception quantity is not automatically Cancelled Quantity.

---

## 51. Outstanding Quantity

Canonical field concept:

`outstanding_qty`

Canonical V1 formula:

`outstanding_qty = approved_qty - accepted_qty - cancelled_qty`

Outstanding Quantity represents the remaining approved business outcome that
has not been accepted or formally cancelled.

---

## 52. Primary IR State

Canonical primary states:

- `draft`
- `submitted`
- `in_process`
- `done`
- `cancelled`

Detailed subprocess statuses must not cause uncontrolled primary-state
explosion.

---

## 53. Subprocess Status

Subprocess status describes a specific business process without replacing the
simple primary IR state.

Examples:

- `approval_status`
- `review_status`
- `purchase_status`
- `stock_status`
- `acceptance_status`
- `exception_status`
- `completion_outcome`

---

## 54. Done

Done means the applicable business outcome has been completed according to
the IR completion rules.

A PO confirmation or completed Stock Move alone is not sufficient where
Acceptance or another applicable outcome remains outstanding.

---

## 55. Company Boundary

Company is a hard accounting/security/execution boundary in V1.

Post-submission Company change is not an ordinary Revision.

Cross-company fulfillment is outside V1.

---

## 56. LIGHT-Linked Transaction

A LIGHT-Linked Transaction is a standard Odoo Purchase/Stock transaction with
a valid structured relationship to LIGHT.

LIGHT-specific Purchase/Stock governance applies only where such relationship
exists.

Non-LIGHT Odoo transactions retain standard Odoo behavior.

---

## 57. Authoritative Relationship

An Authoritative Relationship is a structured model relation used as the
source of truth for transaction attribution.

Free-text fields such as `purchase.order.origin` may support navigation or
human readability but are not authoritative LIGHT relationships.

---

## 58. Structured Audit

Structured Audit means critical business history is stored in queryable
records/events containing relevant actor, timestamp, source, reason, and
business context.

Odoo Chatter supplements Structured Audit but does not replace it.

---

## 59. Governance Configuration

Governance Configuration includes policies/rules that determine business
authority or mandatory processing.

Used governance configuration must preserve historical meaning through
effective dating/versioning as applicable.

Critical configuration must not silently fall back when missing or
ambiguous.

---

## 60. Community-Compatible Core

Community-Compatible Core means the core LIGHT architecture does not
unnecessarily require Enterprise-only modules.

Initial deployment may use Odoo Enterprise.

Community-compatible does not mean production must run Odoo Community.

---

## 61. Department

Department is an organizational Approval Policy resolution dimension,
independent from Request Type (M21, DEC-044/045).

Department is resolved from the Requester's HR Employee record within
the Internal Request's own Company, stored as an immutable submission-time
snapshot, and never derived from Request Type.

Department resolution is mandatory only when Department-aware Approval
Policy governance is actually configured as relevant to the current
Company/Purpose/Request Type (DEC-045); otherwise legacy/global
governance applies unchanged.

---

## 62. Estimated Line Amount

Estimated Line Amount is `requested_qty x price_estimate` on a
requirement line (M21).

It is a derived amount, not itself an authoritative procurement or
Financial Approval amount.

---

## 63. Estimated Submission Amount

Estimated Submission Amount is the sum of Estimated Line Amount across
every requirement line of an Internal Request (M21).

It is the amount basis supplied to NEED Approval Cycle creation for
amount-based routing. It does not turn NEED into FINANCIAL and does not
introduce a third Approval Purpose.

---

## Canonical Quantity Summary

Use:

- `requested_qty`
- `approved_qty`
- `internal_allocated_qty`
- `purchase_allocated_qty`
- `allocated_qty`
- `committed_qty`
- `delivered_qty`
- `accepted_qty`
- `cancelled_qty`
- `outstanding_qty`

Do not introduce ambiguous authoritative terms such as `fulfilled_qty`
without an approved architecture change.

---

## Terminology Governance

If implementation requires a new term that changes business semantics:

1. Do not silently introduce it.
2. Compare it against this canonical terminology.
3. Identify affected FR/TR/ADR entries.
4. Request clarification where semantics differ.
5. Update the baseline only through approved change control.
