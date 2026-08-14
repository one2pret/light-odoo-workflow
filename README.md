# LIGHT Internal Request — Odoo 18

LIGHT Internal Request is an Odoo 18 business governance layer for internal goods and service requests, approval, fulfillment planning, procurement/internal stock attribution, financial authority, acceptance, exception handling, revision, and audit.

## Status

Documentation Baseline V1 preparation.

Implementation must not begin until the baseline consistency audit passes and the documentation baseline is committed/tagged.

## Architecture

V1 custom addon family:

- `light_ir_approval`
- `light_internal_request`
- `light_ir_hr`
- `light_ir_purchase`
- `light_ir_stock`

Core architecture is intended to remain Community-compatible while initial deployment targets Odoo 18 Enterprise.

## Documentation

Read in this order:

1. `AGENTS.md`
2. `CLAUDE.md`
3. `IMPLEMENTATION_PLAN.md`
4. `docs/00_governance/TERMINOLOGY.md`
5. `docs/00_governance/DECISION_REGISTER.md`
6. `docs/01_product/PRD.md`
7. `docs/01_product/FRS.md`
8. `docs/01_product/NFR.md`
9. `docs/03_architecture/ADR.md`
10. relevant file under `docs/02_technical/`
11. `docs/04_traceability/RTM.md`

## Implementation Rule

Implementation is milestone-based.

Do not implement the complete system in one task.

Start with M01 only after documentation baseline validation and consistency audit pass.

## Key Principles

- Standard Odoo First
- Community-compatible core
- Server-side governance
- Explicit quantity attribution
- Structured historical audit
- No silent fallback for critical governance configuration
- Tests are part of Definition of Done
