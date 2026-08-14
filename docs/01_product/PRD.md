# LIGHT Internal Request
## Product Requirements Document

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Version: 1.0
- Status: BASELINED / LOCKED
- Initial Deployment: Odoo Enterprise
- Architecture Goal: Community-Compatible Core

## Document Control

This document is the authoritative Product Requirements baseline for
LIGHT Internal Request V1.

Changes after baseline must be classified as one of:

- Clarification
- Defect Correction
- Change Request
- Future Scope

Implementation agents must not introduce new product requirements without
approved change control.

---

# 1. Product Overview

LIGHT Internal Request is an Odoo-based system for managing internal requests
for goods and services across LIGHT Group.

The intended end-to-end lifecycle is:

Request
→ Need Approval
→ Review / Fulfillment Planning
→ Price / Financial Validation
→ Procurement and/or Internal Transfer
→ Receipt / Delivery
→ Business Acceptance
→ Completion

LIGHT does not replace Odoo Purchase, Inventory, HR, Accounting, or other
standard business engines.

LIGHT acts as a governance, orchestration, traceability, and authorization
layer around standard Odoo execution.

---

# 2. Business Problem

The organization requires end-to-end control and traceability over internal
requests.

Authorized users must be able to answer:

- Who requested the goods/service?
- What was requested?
- For which Company and Department?
- What quantity was requested?
- What quantity was approved?
- Who approved it?
- How will the requirement be fulfilled?
- Is fulfillment Internal, Purchase, or Split?
- Has sourcing started?
- Has an RFQ/PO been created?
- Has the applicable financial authority been obtained?
- Has the goods/service been delivered?
- Has it been accepted by the business recipient?
- Is there an exception?
- What quantity remains outstanding?
- Has the request been revised or partially cancelled?
- Which standard Odoo transactions are linked to the IR?

Without this integration, information becomes fragmented between request,
approval, procurement, inventory, and manual communication.

---

# 3. Product Objective

LIGHT Internal Request V1 must provide one auditable traceability chain:

Internal Request
→ Need Authorization
→ Fulfillment Decision
→ Financial Authorization
→ Purchase / Internal Execution
→ Delivery
→ Acceptance
→ Completion

Historical business decisions must remain traceable.

---

# 4. Product Principles

LIGHT V1 follows these principles:

- Standard Odoo First
- Configuration over Hard-code
- Server-side Governance
- Explicit Traceability
- Historical Preservation
- Multi-company Safety
- Community-compatible Core
- Controlled Extension
- Automated Tests as Definition of Done

---

# 5. Scope V1

V1 supports internal requests for:

- Goods
- Services

Goods may be fulfilled through:

- Internal Stock
- Purchase
- Split Internal + Purchase

Service is fulfilled through purchase/service fulfillment and does not
require physical Internal Transfer.

---

# 6. Company Scope

Each Internal Request belongs to one authoritative Company.

All V1 execution must remain within the same Company.

Valid examples:

IR Company A
→ PO Company A

IR Company A
→ Warehouse/Stock Company A

Invalid V1 example:

IR Company A
→ Stock Company B

Cross-company/intercompany fulfillment is outside V1.

---

# 7. Organizational Context

An Internal Request may carry organizational context such as Department or
Division.

When Odoo HR integration is enabled, organizational context uses standard:

`hr.department`

HR-dependent behavior must remain isolated in the optional `light_ir_hr`
integration addon.

The core LIGHT architecture must not require HR unconditionally.

---

# 8. Request Type

Request Type is configurable master data.

Examples may include:

- Asset Medical
- Asset Non Medical
- Stock
- Supplies
- Service
- Others

These are examples only and must not become hard-coded business behavior.

Request Type may participate in applicable policy resolution.

---

# 9. Item Nature

Item Nature is distinct from Request Type.

Canonical Item Nature values are:

- Goods
- Service

Technical/business eligibility such as physical stock fulfillment must not be
determined merely by the Request Type name.

---

# 10. Internal Request

A Requester may create one Internal Request containing one or more requirement
lines.

A requirement line may include:

- Requirement/Product
- Description/Specification
- Requested Quantity
- Unit of Measure
- Required Date
- Business Justification
- Supporting Attachments
- Optional Requester Price Estimate

Applicable policy may define additional mandatory information.

---

# 11. Registered and Unregistered Requirement

LIGHT must support both:

- Registered Requirement using an existing Odoo Product
- Unregistered Requirement where no suitable Odoo Product has yet been resolved

An Unregistered Requirement may be submitted with sufficient description and
specification.

Before execution requiring a standard Odoo Product, the requirement must be
resolved to an applicable Product.

LIGHT must not create a competing product master.

---

# 12. Requester Price

Requester Price Estimate is optional by default.

A requester is not expected to know the final vendor/commercial price.

Unknown requester price must not automatically prevent a valid request.

Price maturity may later come from:

- reference price;
- validated price;
- vendor quotation;
- sourcing;
- actual PO commercial value.

---

# 13. Need Approval

After submission, the requirement must follow applicable Need Approval
governance.

Need Approval answers:

"Is this business need authorized?"

Need Approval may reduce the requested quantity.

Example:

Requested Qty = 10
Approved Qty = 8

The quantity difference of 2 is not Cancellation.

---

# 14. Approval Governance

Approval authority must be configuration-driven.

Applicable approval policy may consider:

- Company
- Department / Organizational Scope
- Request Type
- Approval Purpose
- Financial Threshold
- Effective Period

Approval responsibilities may conceptually include:

- Department Head
- Related C-Level
- CFO
- CEO
- Specific Configured Authority

No named individual must be hard-coded into business logic.

---

# 15. Approval Purpose

LIGHT uses one configurable approval architecture with multiple purposes.

V1 purposes:

- NEED
- FINANCIAL

Need Approval and Financial Approval are distinct business authorizations.

---

# 16. Approval Threshold

Financial threshold evaluation must be configurable.

Applicable policy may use:

- Untaxed Amount
- Tax Included Amount

Thresholds must not be hard-coded in Python.

---

# 17. Approval History

Approval cycles must preserve the applicable business basis and resolved
authority at the time the cycle is created.

Later master-data changes must not rewrite the meaning of historical approval.

---

# 18. Self Approval

Self Approval is forbidden by default.

If the Requester resolves as the approver for an applicable approval step,
the configured self-approval behavior applies.

The default V1 governance behavior is:

- the self-resolved step is not self-approved;
- the step is skipped according to policy;
- the reason is audited;
- processing continues to the next applicable step.

---

# 19. Missing Approver

If a mandatory approver/responsibility cannot be resolved, the default
behavior is:

BLOCK

A missing approver may only be skipped where an explicitly applicable policy
allows that behavior.

There must be no silent under-approval.

---

# 20. Delegation

Approval delegation must be supported in a controlled and auditable manner.

Audit must preserve:

- Original Approver / Authority
- Actual Actor
- Delegation Reference
- Validity Context

Delegation must not erase the original authority chain.

---

# 21. Review

Review is distinct from Approval.

Applicable policy determines whether Review is required.

Review may evaluate:

- requirement clarity;
- product resolution;
- available stock;
- fulfillment route;
- required date;
- operational feasibility;
- applicable price information.

Not every Request Type must require Review.

---

# 22. Fulfillment Planning

After applicable authorization, Approved Quantity may be allocated to:

- Internal
- Purchase
- both, where Split Fulfillment is allowed

Example:

Approved Qty = 10

Internal Allocation = 4
Purchase Allocation = 6

Total effective allocation must not exceed the authorized quantity.

Planning is separate from execution.

---

# 23. Internal Allocation

Internal Allocation represents the quantity planned to be fulfilled through
internal stock.

Internal Allocation is not a Stock Move.

Actual inventory execution remains standard Odoo Inventory.

---

# 24. Purchase Allocation

Purchase Allocation represents the quantity planned to be fulfilled through
procurement.

Purchase Allocation is not a Purchase Order.

Actual procurement execution remains standard Odoo Purchase.

---

# 25. Sourcing

LIGHT must support sourcing where final price/vendor is not yet known.

A single Purchase Allocation may be used to obtain multiple vendor quotations.

Example:

Purchase Allocation = 10

Vendor A RFQ Qty = 10
Vendor B RFQ Qty = 10
Vendor C RFQ Qty = 10

This must not cause the effective requirement to become 30.

Sourcing quantity and awarded/committed quantity are different concepts.

---

# 26. Procurement Consolidation

Multiple compatible IR requirements may be consolidated into one procurement
transaction.

Example:

IR001 Laptop = 3
IR002 Laptop = 2
IR003 Laptop = 5

Consolidated PO Line = 10

The source attribution must remain explicit:

PO Line Qty 10
- IR001 Qty 3
- IR002 Qty 2
- IR003 Qty 5

Free-text `origin` is not authoritative traceability.

---

# 27. Split Procurement

One IR requirement may be fulfilled using multiple vendors or Purchase Orders.

Example:

IR Approved Purchase Qty = 10

Vendor A = 6
Vendor B = 4

Each portion must remain explicitly attributable to the source IR.

---

# 28. Financial Approval

Financial Approval answers:

"Is this financial exposure/commitment authorized?"

It is distinct from Need Approval.

Financial Approval uses the common LIGHT Approval Engine with purpose
`FINANCIAL`.

---

# 29. PO Commitment Control

LIGHT-linked Purchase Orders must satisfy applicable financial authority before
actual commitment.

Financial control uses both:

- IR Financial Authorization
- PO Commitment Authority

Example:

IR001 = 30m
IR002 = 30m
IR003 = 20m

Merged PO = 80m

The actual PO commitment must satisfy the applicable authority for 80m if that
is required by the configured policy.

Consolidation must not become a mechanism to avoid higher approval thresholds.

---

# 30. Purchase Integration

Actual procurement must use standard Odoo:

- `purchase.order`
- `purchase.order.line`

LIGHT must not build a duplicate Purchase engine.

LIGHT-specific controls apply only to LIGHT-linked Purchase transactions.

Non-LIGHT Purchase Orders must retain standard Odoo behavior.

---

# 31. Internal Stock Fulfillment

Actual internal stock execution must use standard Odoo:

- `stock.picking`
- `stock.move`
- `stock.move.line`

LIGHT must not build a duplicate Inventory engine.

---

# 32. Internal Transfer Consolidation

One IR may be fulfilled through multiple transfers.

Multiple compatible IR allocations may also be consolidated into stock
execution.

Source IR attribution must remain deterministic and quantity-aware.

---

# 33. Partial Execution

LIGHT must support partial execution, including:

- Partial Purchase Receipt
- Partial Internal Transfer
- Backorder
- Partial Service Fulfillment

Partial execution must not incorrectly complete the entire IR.

---

# 34. Delivered Quantity

Delivered Quantity represents applicable operational fulfillment that has been
executed and attributed to the IR.

Delivered Quantity is distinct from Accepted Quantity.

Delivered does not automatically mean accepted.

---

# 35. Business Acceptance

LIGHT provides a Business Acceptance layer.

Supported policy modes:

- Manual
- Automatic
- Not Required

Default V1 Acceptance mode:

Manual

Automatic Acceptance must still create a structured Acceptance Event and must
be idempotent.

---

# 36. Receiver

Receiver is the party responsible for business receipt/acceptance according to
applicable policy.

Receiver may default to Requester but is not hard-coded as the Requester.

---

# 37. Partial Acceptance

Partial Acceptance is supported.

Example:

Delivered Qty = 10
Accepted Qty = 8
Exception Qty = 2

The IR must not be considered complete merely because physical delivery
reached 10.

---

# 38. Fulfillment Exception

A Fulfillment Exception represents a problem with delivered goods/service.

Examples:

- Damaged
- Wrong Product
- Wrong Specification
- Quantity Shortage
- Quality Issue
- Service Incomplete
- Other

Exception is not Cancellation.

---

# 39. Exception Resolution

Exception resolution may include:

- Replacement
- Return and Replace
- Redelivery
- Accept As Is
- Cancel Remaining
- Other

Actual stock/purchase return execution must use standard Odoo mechanisms.

---

# 40. Replacement

Replacement fulfillment must not double-count the original authorized
requirement.

A replacement addresses an existing exception; it does not create a second
authorized quantity for the same business need.

---

# 41. Service Fulfillment

Service fulfillment must work without requiring physical Stock Moves.

A service may follow:

Purchase
→ Service Fulfillment
→ Business Acceptance

Supporting evidence such as service reports or BAST may be attached according
to applicable policy.

---

# 42. Acceptance Reversal

Finalized Acceptance history must not be destructively edited.

Example correction:

Acceptance +10
Reversal -2
Effective Accepted = 8

The original Acceptance event must remain historical.

---

# 43. Cancellation

Cancellation is distinct from:

- quantity not approved during Need Approval;
- Fulfillment Exception;
- rejection;
- delivery shortage.

Cancellation may be partial or full and must follow applicable authority.

Example:

Approved Qty = 10
Accepted Qty = 6
Cancelled Qty = 4

Outstanding Qty = 0

---

# 44. Revision

Material changes after submission must use controlled Revision.

Examples of material change:

- Quantity
- Product
- Significant Specification
- Financial Basis
- Fulfillment Requirement

Revision may trigger:

- Need Reapproval
- Fulfillment Replanning
- Financial Reapproval

Historical authorization must remain preserved.

---

# 45. Company Revision

Company is a hard accounting/security/execution boundary.

Post-submission Company change is not an ordinary Revision.

The appropriate business approach is controlled cancellation and creation of
a new IR where required.

---

# 46. Historical Preservation

Finalized approval, acceptance, cancellation, attribution, and revision history
must not be destructively rewritten by normal business operations.

Critical history must use structured auditable records.

Odoo Chatter remains supplementary communication/history.

---

# 47. Canonical Quantity Model

Canonical quantities include:

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

Do not use ambiguous authoritative concepts such as `fulfilled_qty` without an
approved architecture change.

---

# 48. Outstanding Quantity

Canonical V1 formula:

`outstanding_qty = approved_qty - accepted_qty - cancelled_qty`

Outstanding Quantity reflects remaining authorized business outcome.

---

# 49. Completion

An IR may become Done only when applicable business outcome conditions are
satisfied.

At minimum:

- all effective lines have zero Outstanding Quantity;
- no blocking unresolved Exception remains;
- no required Acceptance remains pending;
- no mandatory workflow remains unresolved.

PO creation, PO confirmation, Receipt completion, or Stock Transfer completion
alone must not automatically imply business completion.

---

# 50. Monitoring

Authorized users must be able to monitor relevant progress, for example:

- Need Approval
- Review
- Financial Approval
- Purchase Allocation
- Internal Allocation
- RFQ Created
- PO Created
- PO Confirmed
- Internal Transfer Created
- Delivered Quantity
- Accepted Quantity
- Exception Quantity
- Outstanding Quantity

Visibility remains subject to security and confidentiality.

---

# 51. Primary State

LIGHT must avoid excessive primary document states.

Canonical V1 primary states:

- `draft`
- `submitted`
- `in_process`
- `done`
- `cancelled`

Detailed subprocess progress belongs in dedicated process indicators.

---

# 52. Process Indicators

Examples include:

- Approval Status
- Review Status
- Purchase Status
- Stock Status
- Acceptance Status
- Exception Status
- Completion Outcome

These provide visibility without turning the primary state into a large
workflow-state catalog.

---

# 53. Configuration

Business behavior must be configuration-driven at the appropriate scope.

Applicable scope may include:

- Company
- Department / Organizational Scope
- Request Type
- Effective Period

Configuration may determine:

- Approval
- Review Requirement
- Fulfillment Behavior
- Price Validation
- Financial Threshold
- Acceptance
- Cancellation
- Responsibility Assignment

---

# 54. Critical Configuration Safety

Governance-critical configuration has:

NO SILENT FALLBACK

If applicable configuration is:

- missing;
- ambiguous;
- invalid;
- expired;

the applicable business action must block with a clear error.

The system must not silently choose a weaker policy.

---

# 55. Security

Critical authorization must be enforced server-side.

LIGHT distinguishes:

Capability
from
Transaction Authority

Example:

A user belongs to an Approver group

does not automatically mean:

The user may approve every IR.

The user must be the applicable resolved authority for the transaction.

---

# 56. Multi-company Security

Company isolation applies to LIGHT governance and execution relationships.

Applicable records include:

- IR
- Approval
- Policy
- Attribution
- Purchase
- Stock
- Acceptance
- Revision

Unauthorized cross-company linkage must be blocked server-side.

---

# 57. Standard Odoo Preservation

LIGHT must not globally change standard Odoo Purchase or Inventory behavior.

Example:

Normal Purchase Order with no LIGHT attribution
→ standard Odoo behavior

LIGHT-linked Purchase Order
→ standard Odoo behavior plus applicable LIGHT governance

The same principle applies to Stock execution.

---

# 58. Fixed Asset Readiness

Goods requested through LIGHT may later become Fixed Assets.

V1 does not build a custom Asset engine.

LIGHT should preserve sufficient traceability for future integration:

IR
→ Purchase / Receipt
→ Product / Serial
→ Asset

Downstream accounting/asset treatment remains the responsibility of applicable
Odoo configuration and future integration.

---

# 59. Resale Goods Readiness

Goods obtained through an IR may later be:

- internally consumed;
- stocked;
- capitalized;
- resold.

LIGHT must not assume all requested goods are expense/internal-consumption
items.

Downstream treatment remains authoritative in Odoo Product, Inventory, and
Accounting configuration.

---

# 60. Out of Scope V1

The following are explicitly outside the baseline V1 core scope unless later
approved through change control:

- Custom Accounting Engine
- Custom Purchase Engine
- Custom Inventory Engine
- Custom Fixed Asset Engine
- Cross-company Fulfillment
- Intercompany Settlement
- Generic BPM/Workflow Builder
- Arbitrary Python Policy Scripts
- Advanced Automatic Escalation Engine
- Full Contract Management
- Full Vendor Evaluation System

---

# 61. Product Success Criteria

V1 is successful when the organization can perform an auditable end-to-end
process in Odoo:

Create IR
→ Approve Need
→ Review Fulfillment
→ Resolve Price / Financial Authority
→ Purchase and/or Internal Transfer
→ Receive / Deliver
→ Accept
→ Handle Exception / Cancellation where required
→ Complete IR

Authorized users must be able to answer:

- What was requested?
- What was approved?
- Who approved it?
- How is it being fulfilled?
- Which PO/Transfer is related?
- How much has been delivered?
- How much has been accepted?
- What remains outstanding?
- Were there revisions, cancellations, or exceptions?
- Why did those events occur and who performed them?

---

# 62. Architecture Boundary

LIGHT owns:

- Internal Request Governance
- Approval
- Fulfillment Planning
- Attribution
- Acceptance
- Exception Handling
- Revision
- Structured Audit

Standard Odoo continues to own:

- Purchase execution
- Inventory execution
- Product master
- Company/User/Partner masters
- HR master where applicable
- downstream Accounting/Asset processing

LIGHT is a governance/orchestration layer, not a second ERP inside Odoo.

---

# 63. Baseline Statement

This PRD represents the LIGHT Internal Request V1 Product Requirements
baseline.

Coding agents must not silently add, remove, or reinterpret product scope.

Any material change after baseline must be classified as:

- Clarification
- Defect Correction
- Change Request
- Future Scope

and handled through the approved documentation/architecture change process.
