# LIGHT Internal Request
## TR-01 — Module Architecture

- Product: LIGHT Internal Request
- Target Platform: Odoo 18
- Baseline Version: 1.0
- Status: BASELINED / LOCKED

## Purpose

This document defines the V1 addon/module architecture and dependency boundaries.

## V1 Addons

The approved addon family is:

- `light_ir_approval`
- `light_internal_request`
- `light_ir_hr`
- `light_ir_purchase`
- `light_ir_stock`

## Dependency Principles

The architecture shall preserve clear boundaries:

`light_ir_approval`
→ approval engine and approval governance

`light_internal_request`
→ core Internal Request business domain

`light_ir_hr`
→ optional HR/organizational integration

`light_ir_purchase`
→ standard Odoo Purchase integration

`light_ir_stock`
→ standard Odoo Inventory integration

## Core Compatibility

Core LIGHT architecture shall remain Community-compatible.

Initial deployment may use Odoo Enterprise.

Enterprise-only dependencies shall not be introduced into core unless explicitly
approved.

## Standard Odoo First

LIGHT shall reuse standard Odoo models where applicable.

Examples:

- `res.company`
- `res.users`
- `res.partner`
- `product.product`
- `product.template`
- `uom.uom`
- `purchase.order`
- `purchase.order.line`
- `stock.picking`
- `stock.move`
- `stock.move.line`
- `hr.department` through `light_ir_hr`

## Integration Boundary

LIGHT shall not create replacement engines for:

- Purchase
- Inventory
- Product
- HR Department
- Accounting
- Asset Management

LIGHT is a governance/orchestration layer.

## LIGHT-Linked Scope

LIGHT-specific Purchase and Inventory governance shall apply only where a valid
structured LIGHT relationship exists.

Non-LIGHT Odoo transactions shall retain standard behavior.

---

## TR-ARC-001 — Standard Odoo Object Reuse

Standard Odoo business objects shall be reused when they already represent the
required ERP transaction or master data.

## TR-ARC-002 — Community-Compatible Core

Core LIGHT addons shall remain compatible with Odoo Community unless an
explicitly approved integration requires otherwise.

## TR-ARC-003 — Optional HR Integration

HR integration shall be isolated in `light_ir_hr`.

The core Approval Engine shall not require HR as an unconditional dependency.

## TR-ARC-004 — Purchase Integration Isolation

Purchase-specific logic shall be isolated in `light_ir_purchase`.

Core IR shall not depend on standard Purchase execution unless the purchase
integration addon is installed.

## TR-ARC-005 — Stock Integration Isolation

Inventory-specific logic shall be isolated in `light_ir_stock`.

Core IR shall not depend on standard Inventory execution unless the stock
integration addon is installed.

## TR-ARC-006 — No Custom Purchase Engine

LIGHT shall not create a custom replacement for standard Odoo Purchase Order
execution.

## TR-ARC-007 — No Custom Inventory Engine

LIGHT shall not create a custom replacement for standard Odoo Inventory
execution.

## TR-ARC-008 — No Generic BPM Engine

LIGHT V1 shall not introduce a generic BPM/workflow scripting engine.

Business workflow shall be implemented using explicit LIGHT domain models and
configured policies.

## TR-ARC-009 — Scoped LIGHT Guards

LIGHT-specific downstream guards shall only affect valid LIGHT-linked
transactions.

Non-LIGHT Purchase and Stock transactions shall retain standard Odoo behavior.

## TR-ARC-010 — No Circular Addon Dependencies

Addon dependencies shall remain acyclic.

Any proposed dependency that creates a circular module relationship shall be
rejected or redesigned through approved architecture change control.

---

## Approved Dependency Intent

Conceptually:

`light_ir_approval`
→ foundational governance capability

`light_internal_request`
→ uses LIGHT approval capability

`light_ir_hr`
→ extends organizational resolution

`light_ir_purchase`
→ extends LIGHT IR into standard Purchase

`light_ir_stock`
→ extends LIGHT IR into standard Inventory

Exact manifest dependencies shall follow this direction without circular
coupling.

---

## Architecture Baseline Statement

`TR-ARC-001` through `TR-ARC-010` constitute the V1 module architecture
baseline.

Coding agents shall not alter module boundaries or dependency direction merely
for implementation convenience.

Material architecture changes require approved ADR/change control.
