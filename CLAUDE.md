# LIGHT Odoo — Claude Code Instructions

## Project

LIGHT Internal Request
Target: Odoo 18

Before changing code, read and follow `AGENTS.md`.

## Required Reading Order

For every implementation task:

1. Read `AGENTS.md`.
2. Read the current milestone in `IMPLEMENTATION_PLAN.md`.
3. Read governance and product baseline documents:
   - `docs/00_governance/TERMINOLOGY.md`
   - `docs/00_governance/DECISION_REGISTER.md`
   - `docs/01_product/PRD.md`
   - `docs/01_product/FRS.md`
   - `docs/01_product/NFR.md`
4. Read applicable ADRs:
   - `docs/03_architecture/ADR.md`
5. Read the relevant Technical Requirement file:
   - `docs/02_technical/`
6. Read the applicable RTM entries:
   - `docs/04_traceability/RTM.md`
7. Inspect existing implementation and tests before editing.

## Scope Discipline

Implement only the active milestone.

Do not implement future milestones early.

Do not add features because they appear useful.

Do not change module boundaries without an approved ADR.

Do not introduce Enterprise-only dependencies into core addons.

Do not redesign the approval, quantity, Purchase, Stock, or audit architecture without explicit approval.

## Before Coding

For the active milestone, identify:

- requirement IDs
- technical requirement IDs
- ADRs involved
- models affected
- standard Odoo models extended
- tests required

If any of these are unclear, stop and report ambiguity before implementing.

## Implementation Style

Prefer the smallest compliant implementation.

Reuse existing Odoo models and APIs.

Do not clone standard Odoo functionality.

Keep business logic in Python model/service methods, not only in views or JavaScript.

Critical validation must execute server-side.

Use explicit relations rather than free-text references.

Preserve structured historical audit records.

## Purchase Integration

Do not replace `purchase.order` or `purchase.order.line`.

LIGHT-specific PO validation applies only to LIGHT-linked procurement.

A standard non-LIGHT PO must continue to behave normally.

Do not use `origin` as authoritative IR attribution.

## Stock Integration

Do not replace `stock.picking`, `stock.move`, or `stock.move.line`.

LIGHT-specific stock controls apply only to LIGHT-linked stock execution.

Backorders and partial moves must preserve attribution.

## Approval

Do not use named-user conditions.

Do not hard-code Head, CFO, CEO, C-Level, Department, Company, or amount thresholds.

Resolve approval through configured policy and responsibility.

Approval history must remain immutable through normal business operations.

## Security

Do not use UI visibility as the only authorization control.

Do not assume group membership gives transaction authority.

Do not use broad `sudo()` to make failing security checks disappear.

## Testing

Add or update tests in the same milestone.

Run the relevant test suite before declaring completion.

Do not mark a milestone complete if applicable tests are missing or failing.

## Required Completion Report

At the end of each milestone, report:

### Requirements Covered
- FR IDs
- TR IDs
- ADR IDs

### Files Changed
List changed files.

### Tests
List:
- tests added
- tests executed
- results

### Standard Odoo Impact
State which Odoo models/methods were extended.

### Regression
Confirm that unrelated standard Odoo behavior was not intentionally modified.

### Open Issues
List ambiguities, technical debt, or deferred work.

Do not hide unresolved issues.

## Conflict Handling

If documentation conflicts:

- Do not choose whichever document is easiest to implement.
- Stop implementation of the conflicting area.
- Cite the conflicting IDs and text.
- Request a decision or ADR update.

## Completion Rule

A task is complete only when:

- implementation matches baseline requirements;
- architecture rules are respected;
- security validation exists where required;
- applicable tests exist;
- applicable tests pass;
- no unresolved baseline conflict remains.

## Source Code Location

This repository contains documentation, governance, and implementation
instructions.

Do not create production Odoo addons inside this repository.

The Odoo addon source root is:

`../custom-addons/`

Approved V1 addon directories:

- `../custom-addons/light_ir_approval`
- `../custom-addons/light_internal_request`
- `../custom-addons/light_ir_hr`
- `../custom-addons/light_ir_purchase`
- `../custom-addons/light_ir_stock`

Before implementing any milestone, verify that the target path resolves to the
expected sibling `custom-addons` directory.

Do not create a nested `light-odoo/addons/` implementation tree.
