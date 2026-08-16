# LIGHT Odoo Development Rules

## Project

Product: LIGHT Internal Request
Target Platform: Odoo 18
Initial Deployment: Odoo Enterprise
Architecture Goal: Community-Compatible Core

## Mandatory Architecture Rules

1. Standard Odoo First.
2. Do not create a custom Purchase engine.
3. Do not create a custom Inventory engine.
4. Do not create a generic BPM/workflow engine.
5. Core LIGHT must remain Community-compatible.
6. Enterprise-specific dependencies must remain optional.
7. HR integration must remain isolated in `light_ir_hr`.
8. LIGHT controls only LIGHT-linked Purchase/Stock transactions.
9. Non-LIGHT Odoo transactions must retain standard behavior.
10. Do not introduce circular addon dependencies.

## Authoritative Odoo Models

Use standard Odoo models where applicable:

- Company: `res.company`
- User: `res.users`
- Product: `product.product` / `product.template`
- UoM: `uom.uom`
- Department: `hr.department` through `light_ir_hr`
- Vendor: `res.partner`
- Purchase Order: `purchase.order`
- Purchase Order Line: `purchase.order.line`
- Stock Transfer: `stock.picking`
- Stock Move: `stock.move`
- Stock Move Line: `stock.move.line`
- Attachments: `ir.attachment`
- Activities: `mail.activity`

Do not duplicate these models in LIGHT.

## Security Rules

1. Never rely only on XML `readonly`, `invisible`, or button visibility.
2. Critical business actions must be validated server-side.
3. Capability group membership does not imply transaction authority.
4. Approval actions must validate the resolved approval assignment.
5. Multi-company boundaries must be enforced server-side.
6. Do not hard-code user, company, department, warehouse, request type, or policy database IDs.
7. Do not use `sudo()` to bypass governance unless explicitly justified and documented.
8. LIGHT Administrator is not automatically a business approver.
9. Delegated approval must retain original approver and actual actor.
10. Self-approval is forbidden by default.

## Data Integrity Rules

1. Requested Qty and Approved Qty are separate concepts.
2. Allocation and execution are separate concepts.
3. Delivered Qty and Accepted Qty are separate concepts.
4. Not-approved Qty is not Cancellation.
5. Exception is not Cancellation.
6. Do not use ambiguous `fulfilled_qty` as an authoritative business quantity.
7. Canonical Outstanding Qty:

   `approved_qty - accepted_qty - cancelled_qty`

8. Finalized business history must not be destructively rewritten.
9. Corrections to finalized events must use reversal/superseding events.
10. Submitted business transactions must not normally be hard-deleted.

## Approval Rules

1. NEED and FINANCIAL use the same approval engine with different purposes.
2. Approval configuration must be deterministic.
3. Missing critical approval configuration must block.
4. Missing mandatory approver blocks by default.
5. Self-resolved approval step is skipped only according to policy and must be audited.
6. Historical approval cycles must preserve resolved approver snapshots.
7. Later master-data changes must not rewrite historical approval meaning.
8. Financial threshold basis may be Untaxed or Tax Included according to policy.
9. PO commitment authority must prevent approval splitting.

## Purchase Rules

1. Actual procurement uses standard Odoo Purchase.
2. Purchase Allocation is not a Purchase Order.
3. Explicit Purchase Attribution is authoritative.
4. `purchase.order.origin` is informational only.
5. One IR may map to multiple PO lines/orders.
6. One PO line may map to multiple IR lines.
7. Sourcing quotation quantity must not multiply the authorized requirement.
8. LIGHT-linked PO confirmation must validate financial authority.
9. Non-LIGHT PO confirmation must retain standard Odoo behavior.

## Stock Rules

1. Actual stock execution uses standard Odoo Inventory.
2. Internal Allocation is not a Stock Move.
3. Stock attribution must be quantity-aware.
4. Partial Done and backorders must preserve IR attribution.
5. Consolidated partial execution must use deterministic attribution.
6. Lot/Serial remains authoritative in standard Odoo Inventory.
7. Cross-company fulfillment is outside V1.
8. Non-LIGHT stock transactions retain standard Odoo behavior.

## Acceptance Rules

1. Delivery and Acceptance are separate concepts.
2. Default Acceptance mode is Manual.
3. Automatic Acceptance must still create structured Acceptance Events.
4. Automatic Acceptance must be idempotent.
5. Partial Acceptance must be supported.
6. Accepted Qty must not exceed eligible Delivered Qty.
7. Replacement fulfillment must not double-count the requirement.
8. Service Acceptance must work without Stock Moves.

## Revision and Audit Rules

1. Draft changes do not require formal Revision.
2. Material changes after submission require Revision.
3. Material Revision requires a reason.
4. Company cannot be changed through normal post-submit Revision.
5. No generic `Reopen IR` action.
6. Historical approval/acceptance/cancellation data must remain auditable.
7. Critical actor and timestamp must be determined server-side.
8. Chatter is supplementary, not authoritative audit storage.

## Configuration Rules

1. No critical business thresholds may be hard-coded in Python.
2. Governance-critical policy resolution has no silent fallback.
3. Missing or ambiguous critical configuration must block.
4. Used governance policies should be versioned/effective-dated.
5. Do not expose arbitrary Python or `eval()` scripting to business administrators.
6. Configuration authority is separate from business approval authority.

## Odoo Coding Rules

1. Use Odoo ORM by default.
2. Do not bypass ORM/business rules with direct SQL for normal transaction processing.
3. Use `_inherit` and supported extension points.
4. Call `super()` when extending standard Odoo behavior.
5. Do not copy large standard Odoo methods merely to insert LIGHT logic.
6. Keep LIGHT-specific checks conditional on valid LIGHT linkage.
7. Use standard Odoo currency APIs.
8. Use standard Odoo UoM conversion and float comparison mechanisms.
9. Use translatable user-facing strings.
10. Avoid obvious N+1 query patterns.

## Testing Rules

1. Automated tests are part of Definition of Done.
2. Business-critical methods require tests.
3. Security and multi-company behavior require tests.
4. Non-LIGHT Purchase and Stock regression tests are mandatory.
5. Every milestone must identify applicable tests.
6. A milestone is not Done while applicable tests fail.

## Change Control

Do not invent new product requirements.

If PRD, FRS, ADR, TR, or implementation behavior conflict:

1. Stop.
2. Report the conflict.
3. Cite the affected requirement/ADR/TR IDs.
4. Propose options.
5. Do not silently redesign the system.

Architecture changes require an approved new or superseding ADR.

## Repository Boundary

The LIGHT documentation and governance repository is:

`odoo18-dev/light-odoo`

The Odoo addon source directory is the sibling directory:

`odoo18-dev/custom-addons`

From this repository, addon source code must be created under:

`../custom-addons/`

Do not create production Odoo addon source inside `light-odoo/`.

The approved V1 addon locations are:

- `../custom-addons/light_ir_approval`
- `../custom-addons/light_internal_request`
- `../custom-addons/light_ir_hr`
- `../custom-addons/light_ir_purchase`
- `../custom-addons/light_ir_stock`

Documentation remains authoritative in `light-odoo/`.

## Git Repository Ownership

This project uses two separate Git repositories.

Documentation and governance repository:

`light-odoo/`

Odoo source-code repository:

`../custom-addons/`

Rules:

1. Documentation baseline changes belong only in the `light-odoo` repository.
2. Odoo addon implementation changes belong only in the `../custom-addons` repository.
3. Coding commits for milestones M01..M20 must be created in `../custom-addons`.
4. Do not commit addon source code into `light-odoo`.
5. Do not commit documentation baseline changes into `../custom-addons`.
6. A milestone implementation commit must be made only after the milestone reaches READY_FOR_REVIEW and passes review.
7. Documentation baseline changes require explicit change control and are committed separately in `light-odoo`.


LANGUAGE CONVENTION

Source-code technical language MUST remain English:
- Python identifiers
- model/field technical names
- method names
- XML IDs
- docstrings
- technical comments
- test names
- developer-facing technical logs

Indonesian MUST be used for end-user-facing LIGHT ERP content:
- field labels
- menu labels
- action/view titles
- button labels
- help text
- business UserError/ValidationError messages
- operational instructions shown to users

Do not translate Odoo/Python technical identifiers into Indonesian.

Use Odoo translation-compatible strings (_()) for user-facing messages.

Technical milestone reports may be written in Indonesian while retaining
English technical identifiers.
