# LIGHT Internal Request
## TR-05 — Security, ACL, Record Rules, and Business Authorization

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

## Purpose

This document defines the security principles for LIGHT V1.

Security shall combine:

- Odoo ACL
- Record Rules
- Business Authorization
- Company Validation

Capability and transaction authority are separate concepts.

## Capability Groups

V1 may include groups such as:

- LIGHT IR User
- LIGHT Need Approver
- LIGHT Price Validator
- LIGHT Reviewer
- LIGHT Plan Approver
- LIGHT Procurement
- LIGHT Inventory
- LIGHT Receiver
- LIGHT Financial Approver
- LIGHT Auditor
- LIGHT Manager
- LIGHT Administrator

Exact XML IDs may be refined during implementation.

## Capability vs Authority

Example:

A user belongs to an Approver group.

This means the user has approval capability.

It does not mean the user can approve every IR.

The business action must still verify:

- Company access
- applicable active approval cycle/step
- resolved authority
- valid delegation if used
- applicable transaction state

## Requester

Requester may:

- create IR
- edit own Draft where allowed
- submit
- respond to controlled revision/return
- view permitted progress

Requester does not automatically receive:

- Need Approval authority
- Financial Approval authority
- Review authority
- Price Validation authority
- Procurement authority

## Auditor

Auditor is read-only for applicable audit scope.

Auditor shall not gain business action authority merely from audit access.

## Administrator

LIGHT Administrator may manage governance configuration according to ACL.

Administrator is not automatically a business approver.

## Multi-Company

Transactional LIGHT models shall respect allowed Company boundaries.

Invalid cross-company attribution shall be blocked.

## Sensitive Commercial Information

Commercial/financial visibility may be more restricted than high-level process
visibility.

Requester may be allowed to see progress such as PO exists / receipt progress
without automatically seeing all commercial details.

---

## TR-SEC-001 — Server-Side Critical Authorization

Critical LIGHT business authorization shall be enforced server-side.

## TR-SEC-002 — UI Restrictions Are Not Security

XML `readonly`, `invisible`, domains, and button visibility shall not be the
only control for critical actions.

## TR-SEC-003 — Transaction-Specific Authority

Business actions shall validate applicable transaction authority at action
time.

## TR-SEC-004 — Company Boundary Enforcement

LIGHT shall enforce applicable allowed Company boundaries for transactions,
governance, and attribution.

## TR-SEC-005 — Admin Is Not Automatic Approver

LIGHT Administrator shall not automatically receive NEED or FINANCIAL approval
authority.

## TR-SEC-006 — Auditor Is Read-Only

LIGHT Auditor shall not receive normal operational write/approval authority.

## TR-SEC-007 — Standard Odoo Security Still Applies

LIGHT Procurement/Inventory capability shall not silently bypass standard Odoo
Purchase or Inventory access rights.

## TR-SEC-008 — Cross-Company Attribution Block

Purchase/Stock attribution between incompatible Companies shall be rejected.

## TR-SEC-009 — Controlled sudo Usage

`sudo()` shall not be used merely to make failing governance/security checks
disappear.

Any necessary elevated operation shall preserve the intended business security
boundary and be explicitly justified.

## TR-SEC-010 — Trustworthy Historical Actor

Critical historical actor identity shall be derived server-side from the
authenticated business action context.

Authoritative actor fields shall not be freely supplied by the client.

---

## Visibility Principle

Record visibility may be broader than action authority where operational
monitoring requires it.

The general principle is:

Can I see this IR?
→ record/security scope

Can I approve/edit/allocate/accept this IR?
→ strict business-method authorization

## Historical Records

Finalized approval, acceptance, cancellation, revision, and other structured
history shall not be directly writable by normal operational users.

## Unlink Policy

Draft business records may be deleted according to applicable permission.

Submitted/historical governed records shall normally use:

- cancel
- archive
- supersede
- reversal

rather than hard delete.

## Integration Security Boundary

Installing LIGHT integration addons shall not automatically grant unrelated
standard Odoo Purchase, Inventory, or HR permissions.

## Security Baseline Statement

`TR-SEC-001` through `TR-SEC-010` constitute the V1 Security baseline.
