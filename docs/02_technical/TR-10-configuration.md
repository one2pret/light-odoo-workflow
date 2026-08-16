# LIGHT Internal Request
## TR-10 — Configuration and Policy Resolution

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

## Purpose

This document defines configuration architecture and deterministic policy
resolution.

## Principle

Business governance shall be configuration-driven where organization-specific
behavior may vary.

Configuration is not permission to build arbitrary executable workflow scripts.

## Configuration Domains

Configuration may include:

- Request Type
- Approval Policy
- Approval Rules
- Approval Steps
- Responsibility Assignment
- Delegation
- Review Requirement
- Fulfillment Behavior
- Financial Threshold
- Acceptance Policy
- Cancellation Authority

## Scope Dimensions

Applicable configuration may consider:

- Company
- Department / Organizational Scope
- Request Type
- Purpose
- Effective Period
- Amount Range where applicable

## Request Type

Request Type is master/configuration data.

Examples:

- Asset Medical
- Asset Non Medical
- Stock
- Supplies
- Service
- Others

These names shall not be hard-coded into Python branching.

Bad:

`if request_type.name == "Asset Medical": ...`

Preferred:

resolve applicable configured policy.

## Review Requirement and Fulfillment Plan Approval Requirement

`light.ir.policy` (TR-02) is the sole configuration owner for whether
Review (FR-IR-042..045) and Fulfillment Plan Approval (FR-IR-052) are
required. It is not represented on `light.ir.request.type` (which carries
no behavioral fields, DEC-006/DEC-009) and it is not represented on
`light.ir.approval.policy` (whose purpose remains NEED/FINANCIAL only,
ADR-005/DEC-010, and which must remain generic/purpose-agnostic,
ADR-003/DEC-004).

Resolution dimensions for this V1 use case:

- Company (required)
- Request Type (optional — exact match or global)
- Effective Period (optional)

Precedence: an active `light.ir.policy` with an exact `request_type_id`
match takes precedence over an active global (`request_type_id` empty)
policy for the same Company and effective date. This mirrors the
precedence already established for `light.ir.approval.policy` Request Type
resolution.

Resolution outcome:

- No applicable `light.ir.policy` for the Company/Request Type/date
  context: BLOCK with an actionable configuration error (TR-CFG-007).
- More than one equally applicable `light.ir.policy` at the same
  specificity tier (both exact-Request-Type, or both global): BLOCK as
  ambiguous (TR-CFG-008).
- An expired or not-yet-effective policy is not applicable and is excluded
  from resolution, not silently substituted for the current one.
- No configuration silently defaults `review_required` or
  `plan_approval_required` to any value (ADR-025, DEC-019): once a policy
  resolves, its own explicit Boolean value is always what applies —
  including an explicit `False`, which is a deliberate configuration
  decision, not a fallback.

## Responsibility

Approval responsibility shall not be represented by hard-coded user IDs.

Responsibilities may conceptually represent:

- Department Head
- Related C-Level
- CFO
- CEO
- other configured authority

The actual user shall be resolved through applicable responsibility/HR
configuration.

## Effective Dating

Governance-critical configuration shall support an effective period where
historical/current policy distinction matters.

Historical transactions shall preserve the policy context actually applied.

## Deterministic Resolution

Policy resolution must result in:

- exactly one applicable policy/rule where one is required; or
- an explicit controlled no-policy outcome where policy is genuinely optional.

Critical ambiguity shall block.

## No Silent Fallback

The system shall not silently choose:

- global policy
- first database record
- lowest threshold
- arbitrary approver

merely because intended configuration is missing or ambiguous.

Fallback behavior is permitted only when explicitly defined as part of the
approved policy-resolution design.

## Threshold Ranges

Financial ranges shall be deterministic.

Example:

0 <= amount < 50m
50m <= amount < 100m
amount >= 100m

Boundary semantics shall be explicit.

Overlapping active applicable ranges shall be invalid.

## Configuration Validation

Where practical, invalid configuration shall be prevented at configuration
time.

Runtime guards remain required because configuration may change over time.

---

## TR-CFG-001 — Configuration Over Hard-Code

Organization-specific governance behavior shall be configuration-driven.

## TR-CFG-002 — Company Scope

Applicable governance configuration shall support Company scope.

## TR-CFG-003 — Organizational Scope

Applicable governance configuration shall support organizational scope where
required.

## TR-CFG-004 — Request Type Scope

Applicable governance configuration shall support Request Type as a resolution
dimension where required.

## TR-CFG-005 — Effective Period

Governance-critical policy shall support applicable effective dating.

## TR-CFG-006 — Deterministic Resolution

Critical policy resolution shall be deterministic.

## TR-CFG-007 — Missing Critical Policy Blocks

Missing mandatory governance-critical configuration shall block the applicable
business action with an actionable error.

## TR-CFG-008 — Ambiguous Critical Policy Blocks

Multiple equally applicable critical policies/rules shall block rather than
being arbitrarily selected.

## TR-CFG-009 — No Arbitrary Python Policy Scripts

Ordinary business configuration shall not require arbitrary Python/eval
scripts.

## TR-CFG-010 — Historical Policy Preservation

Historical transactions shall preserve sufficient policy context so later
configuration changes do not rewrite prior governance meaning.

## TR-CFG-011 — Threshold Validation

Applicable threshold ranges shall be validated against ambiguity/overlap.

## TR-CFG-012 — Configuration Security

Only appropriately authorized users shall be able to modify governance-critical
configuration.

---

## Error Example

Preferred:

`No active NEED Approval Policy was found for Company X, Department Y and
Request Type Z on 2026-08-14.`

Avoid:

`Configuration error.`

Errors shall remain security-safe.

## Configuration Baseline Statement

`TR-CFG-001` through `TR-CFG-012` constitute the V1 Configuration baseline.
