# LIGHT Internal Request
## TR-04 — Approval Engine

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

## Purpose

This document defines the common configurable Approval Engine used by LIGHT.

V1 approval purposes:

- `NEED`
- `FINANCIAL`

The system shall use one approval architecture rather than separate unrelated
workflow engines.

## Approval Configuration Models

Approved master models:

- `light.ir.approval.policy`
- `light.ir.approval.rule`
- `light.ir.approval.rule.step`
- `light.ir.responsibility.assignment`
- `light.ir.approval.delegation`

## Approval Transaction Models

Approved transactional history models:

- `light.ir.approval.cycle`
- `light.ir.approval.cycle.step`
- `light.ir.approval.cycle.step.line`

Master configuration and transaction history shall remain separate.

## Resolution Context

Approval policy resolution may consider:

- Company
- Organizational Scope
- Request Type
- Approval Purpose
- Effective Date
- Financial Basis where applicable

## Approval Step Sources

Approver resolution shall support configured sources such as:

- specific configured authority
- Department Manager / Head through HR integration
- configured Responsibility
- applicable C-Level responsibility
- CFO responsibility
- CEO responsibility

No business logic shall depend on hard-coded named users.

## Threshold Basis

Where financial thresholds apply, policy shall explicitly define whether the
amount basis is:

- Untaxed
- Tax Included

## Sequential Execution

V1 baseline approval execution is sequential.

Advanced quorum/team/parallel approval is outside baseline unless explicitly
introduced through approved change control.

## Self Approval

Default:

FORBIDDEN

Where the Requester resolves as the applicable approver and the configured
self-approval behavior is skip:

- the step shall not be self-approved;
- the step shall be marked skipped;
- reason shall be stored;
- processing continues to the next applicable step.

## Missing Approver

Default:

BLOCK

A missing mandatory approver may only be skipped where explicitly permitted by
the applicable policy.

## Delegation

Delegation shall preserve:

- Original Approver / Responsibility
- Actual Actor
- Delegation Reference
- Validity Period
- Applicable Scope

Expired delegation shall not authorize action.

---

## TR-APR-001 — Common Approval Engine

NEED and FINANCIAL shall use one common configurable Approval Engine.

## TR-APR-002 — Explicit Approval Purpose

Each Approval Cycle shall explicitly identify its approval purpose.

## TR-APR-003 — Deterministic Policy Resolution

Approval Policy and Rule resolution shall be deterministic for the same
authoritative business context.

Missing or ambiguous governance-critical configuration shall block.

## TR-APR-004 — Approval Cycle Snapshot

When an Approval Cycle is created, applicable policy/rule/business basis shall
be preserved as transaction history.

## TR-APR-005 — Resolved Authority Snapshot

Each transactional Approval Step shall preserve the resolved authority/user
context needed for audit.

Later master changes shall not rewrite historical approval meaning.

## TR-APR-006 — Deterministic Threshold Boundaries

Financial threshold ranges shall resolve unambiguously at boundary values.

Overlapping or ambiguous applicable ranges shall be rejected.

## TR-APR-007 — Configurable Tax Basis

Financial threshold evaluation shall use the configured Untaxed or Tax Included
basis.

## TR-APR-008 — Server-Side Self-Approval Protection

Self-approval protection shall be enforced server-side.

UI button visibility alone is insufficient.

## TR-APR-009 — Audited Self-Step Skip

If policy allows a self-resolved step to be skipped, the system shall preserve
the skipped step and the reason.

Skipped shall not be silently treated as an approval decision.

## TR-APR-010 — Missing Mandatory Approver Blocks

If a mandatory approver cannot be resolved, approval shall block by default.

## TR-APR-011 — Delegation Audit

Delegated approval shall preserve both original authority and actual acting
user, together with applicable delegation context.

## TR-APR-012 — Financial Reauthorization

If applicable financial basis exceeds or invalidates existing authorization,
new applicable Financial Approval shall be required before commitment.

## TR-APR-013 — PO Commitment Authority

LIGHT-linked PO commitment shall be evaluated against applicable commitment
authority so consolidation/splitting cannot be used to evade a higher approval
threshold.

---

## Approval Cycle States

Recommended transactional cycle states include:

- `draft`
- `active`
- `approved`
- `rejected`
- `superseded`
- `cancelled`

Exact implementation may be refined without changing semantics.

## Approval Step States

Recommended step states include:

- `waiting`
- `approved`
- `rejected`
- `returned`
- `skipped`
- `superseded`

A skipped step must have an auditable reason.

## Approval Actions

Critical actions shall be executed through server-side business methods.

Examples:

- approve
- reject
- return where applicable
- delegate resolution
- skip according to policy
- supersede

Direct unrestricted state editing shall not be authoritative.

## Historical Immutability

Finalized approval history shall not be modified through normal business edit.

Correction or changed business basis shall use new/superseding cycles.

## Approval Engine Baseline Statement

`TR-APR-001` through `TR-APR-013` constitute the V1 Approval Engine baseline.
