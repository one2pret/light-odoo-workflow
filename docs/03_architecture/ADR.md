# LIGHT Internal Request
## Architecture Decision Records

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

This document contains binding architecture decisions for LIGHT Internal
Request V1.

Coding agents shall not silently override an ACCEPTED ADR.

---

## ADR-001 — Standard Odoo First

**Status:** ACCEPTED

Use standard Odoo business objects whenever Odoo already owns the relevant ERP
domain.

Examples:

- Purchase → `purchase.order`, `purchase.order.line`
- Inventory → `stock.picking`, `stock.move`, `stock.move.line`
- Product → `product.product`, `product.template`
- Company → `res.company`
- User → `res.users`
- Partner/Vendor → `res.partner`
- UoM → `uom.uom`

LIGHT shall not duplicate these objects merely for convenience.

---

## ADR-002 — Community-Compatible Core

**Status:** ACCEPTED

Core LIGHT architecture shall remain Community-compatible.

Initial deployment may use Odoo Enterprise.

Enterprise-only dependencies must remain optional and explicitly isolated.

---

## ADR-003 — Modular Integration Boundary

**Status:** ACCEPTED

V1 addon family:

- `light_ir_approval`
- `light_internal_request`
- `light_ir_hr`
- `light_ir_purchase`
- `light_ir_stock`

The architecture shall avoid circular addon dependencies.

---

## ADR-004 — HR Integration Is Optional

**Status:** ACCEPTED

LIGHT core shall not require HR unconditionally.

Where HR is installed:

Department / Division
→ `hr.department`

through:

`light_ir_hr`

---

## ADR-005 — Single Approval Engine

**Status:** ACCEPTED

LIGHT shall use one configurable Approval Engine.

V1 approval purposes:

- `NEED`
- `FINANCIAL`

Do not create separate unrelated approval engines for each purpose.

---

## ADR-006 — Approval Master and Transaction Are Separate

**Status:** ACCEPTED

Approval configuration and Approval transaction history shall remain separate.

Policy / Rule / Step
→ resolve
→ Approval Cycle
→ Approval Cycle Step

Later master changes shall not rewrite historical approvals.

---

## ADR-007 — Capability Is Not Authority

**Status:** ACCEPTED

Security groups grant capability.

Transaction context grants actual authority.

Approver group membership alone shall not authorize approval of every IR.

---

## ADR-008 — Server-Side Governance

**Status:** ACCEPTED

Critical governance shall be enforced server-side.

UI properties such as:

- readonly
- invisible
- domain
- button visibility

are UX controls, not authoritative security controls.

---

## ADR-009 — Explicit Quantity Semantics

**Status:** ACCEPTED

Canonical quantity concepts are:

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

Do not introduce ambiguous `fulfilled_qty` as an authoritative business
quantity.

---

## ADR-010 — Allocation Is Separate From Execution

**Status:** ACCEPTED

Fulfillment planning shall remain separate from ERP execution.

Purchase Allocation
!= Purchase Order

Internal Allocation
!= Stock Move

---

## ADR-011 — Many-to-Many Purchase Attribution

**Status:** ACCEPTED

Purchase attribution shall support:

- one IR to multiple PO Lines / POs
- multiple IRs to one PO Line

Attribution shall be explicit and quantity-aware.

`purchase.order.origin` is informational only.

---

## ADR-012 — Sourcing Is Not Commitment

**Status:** ACCEPTED

Multiple RFQs may represent the same Purchase Allocation.

RFQ sourcing quantity shall not multiply the authorized requirement.

Vendor quotation and awarded/committed quantity are different concepts.

---

## ADR-013 — PO Commitment Authority

**Status:** ACCEPTED

LIGHT financial governance shall evaluate actual PO commitment for LIGHT-linked
procurement.

Example:

IR A = 30m
IR B = 30m
IR C = 20m

Merged PO = 80m

Applicable commitment authority must be evaluated against the real commitment.

This prevents approval splitting.

---

## ADR-014 — LIGHT Guards Are Scoped

**Status:** ACCEPTED

LIGHT-specific Purchase and Stock governance shall apply only to valid
LIGHT-linked transactions.

Non-LIGHT standard Odoo transactions shall retain standard behavior.

---

## ADR-015 — Stock Attribution Uses Standard Execution

**Status:** ACCEPTED

Actual stock execution remains standard Odoo Inventory.

LIGHT attribution shall primarily connect to `stock.move`.

`stock.picking` remains the operational container/navigation object.

---

## ADR-016 — Delivery Is Not Acceptance

**Status:** ACCEPTED

Delivered Quantity and Accepted Quantity are separate business concepts.

Example:

Delivered = 10
Accepted = 8
Exception = 2

is valid.

---

## ADR-017 — Acceptance Is Event-Based

**Status:** ACCEPTED

Acceptance shall use structured historical events.

Canonical candidate:

`light.ir.acceptance.event`

Accepted Quantity shall derive from effective Acceptance and Reversal events.

---

## ADR-018 — Exception Is Not Cancellation

**Status:** ACCEPTED

Fulfillment Exception shall not automatically become Cancellation.

Damaged or incorrect fulfillment still belongs to the authorized requirement
until resolved.

---

## ADR-019 — Completion Uses Business Outcome

**Status:** ACCEPTED

Canonical Outstanding Quantity:

`outstanding_qty = approved_qty - accepted_qty - cancelled_qty`

PO confirmation or stock completion alone is not sufficient for IR completion
where further business outcome is required.

---

## ADR-020 — Compact Primary State

**Status:** ACCEPTED

Canonical primary IR states:

- `draft`
- `submitted`
- `in_process`
- `done`
- `cancelled`

Detailed subprocess status shall be separate.

---

## ADR-021 — Revision Instead of Destructive Editing

**Status:** ACCEPTED

Material post-submission changes shall use controlled Revision.

Historical authorization shall remain preserved.

---

## ADR-022 — No Generic Reopen

**Status:** ACCEPTED

No unrestricted generic `Reopen IR` action shall exist.

Reactivation shall only occur through a valid controlled business event.

---

## ADR-023 — Company Is a Hard Boundary

**Status:** ACCEPTED

Company is a V1 security/accounting/execution boundary.

Post-submission Company change is not an ordinary Revision.

Cross-company fulfillment is outside V1.

---

## ADR-024 — Governance Configuration Is Versioned

**Status:** ACCEPTED

Governance configuration that has been used operationally shall preserve
historical meaning.

Use effective dating/versioning rather than destructive editing where
applicable.

---

## ADR-025 — No Silent Critical Fallback

**Status:** ACCEPTED

Missing or ambiguous governance-critical configuration shall block.

The system shall not silently select an arbitrary weaker policy.

---

## ADR-026 — No Arbitrary Administrative Code

**Status:** ACCEPTED

Business administrators shall not configure ordinary LIGHT governance through
arbitrary Python, `eval()`, or unrestricted scripts.

Use controlled configuration models and operators.

---

## ADR-027 — Structured Audit

**Status:** ACCEPTED

Critical governance/business history shall use structured records.

Odoo Chatter is supplementary.

---

## ADR-028 — Historical Correction Uses Reversal / Supersede

**Status:** ACCEPTED

Finalized historical events shall not be destructively rewritten.

Use:

- Reversal
- Superseding Cycle
- Revision

according to the type of correction.

---

## ADR-029 — Standard Odoo UoM Authority

**Status:** ACCEPTED

Quantity conversion and float precision shall use standard Odoo UoM and float
comparison semantics.

LIGHT shall not create an independent UoM engine.

---

## ADR-030 — Tests Are Part of Implementation

**Status:** ACCEPTED

Feature implementation and applicable automated tests belong to the same
milestone.

A milestone is not Done while required tests are missing or failing.

---

## ADR-031 — Coding Agent Cannot Redesign Architecture

**Status:** ACCEPTED

A coding agent shall not modify architecture merely because an alternative is
easier to implement.

If the baseline creates a material conflict:

1. stop;
2. identify affected IDs;
3. report the conflict;
4. propose options;
5. wait for approved resolution.

---

## ADR-032 — Architecture Change Requires ADR

**Status:** ACCEPTED

Material architecture changes require a new or superseding ADR.

Examples:

- new addon dependency direction
- new authoritative quantity
- new approval engine architecture
- replacement of standard Odoo Purchase/Inventory objects
- cross-company execution architecture
- new generic workflow engine
- Enterprise-only core dependency

---

# ADR Lifecycle

ADR status values:

- PROPOSED
- ACCEPTED
- SUPERSEDED
- REJECTED

All ADRs in this V1 baseline are ACCEPTED unless explicitly stated otherwise.

When an ADR changes, preserve the historical ADR and reference the superseding
ADR rather than rewriting architectural history.

---

# Architecture Baseline Statement

`ADR-001` through `ADR-032` constitute the LIGHT Internal Request V1
Architecture Decision baseline.

Implementation shall conform to these decisions unless an approved later ADR
explicitly supersedes them.
