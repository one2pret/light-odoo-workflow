# Graph Report - ../custom-addons  (2026-08-26)

## Corpus Check
- 134 files · ~86,620 words
- Verdict: corpus is large enough that graph structure adds value.

## Summary
- 2908 nodes · 5885 edges · 154 communities (128 shown, 26 thin omitted)
- Extraction: 99% EXTRACTED · 1% INFERRED · 0% AMBIGUOUS · INFERRED: 54 edges (avg confidence: 0.83)
- Token cost: 0 input · 0 output

## Community Hubs (Navigation)
- Product FRS Requirements
- Governance Decision Register
- LIGHT Addon Modules Overview
- Stock Attribution Tests
- Internal Request Core Tests
- Product PRD
- Fulfillment Exception Tests
- Acceptance Event Tests
- Governance Terminology
- Purchase Attribution Tests
- Purchase Sourcing Tests
- Purchase M20 Golden Scenarios
- Internal Request Actions (Approve/Submit)
- PO Commitment Guard Tests
- Accept Wizard
- Community 15
- Community 16
- Community 17
- Community 18
- Community 19
- Community 20
- Community 21
- Community 22
- Community 23
- Community 24
- Community 25
- Community 26
- Community 27
- Community 28
- Community 29
- Community 30
- Community 31
- Community 32
- Community 33
- Community 34
- Community 35
- Community 36
- Community 37
- Community 38
- Community 39
- Community 40
- Community 41
- Community 42
- Community 43
- Community 44
- Community 45
- Community 46
- Community 47
- Community 48
- Community 49
- Community 50
- Community 51
- Community 52
- Community 53
- Community 54
- Community 56
- Community 57
- Community 58
- Community 59
- Community 60
- Community 61
- Community 62
- Community 63
- Community 64
- Community 65
- Community 66
- Community 67
- Community 68
- Community 69
- Community 70
- Community 71
- Community 72
- Community 73
- Community 74
- Community 75
- Community 76
- Community 77
- Community 78
- Community 79
- Community 80
- Community 81
- Community 82
- Community 83
- Community 84
- Community 85
- Community 86
- Community 87
- Community 88
- Community 89
- Community 90
- Community 91
- Community 92
- Community 93
- Community 94
- Community 95
- Community 96
- Community 97
- Community 98
- Community 99
- Community 100
- Community 101
- Community 102
- Community 103
- Community 104
- Community 105
- Community 106
- Community 107
- Community 108
- Community 109
- Community 110
- Community 111
- Community 112
- Community 113
- Community 114
- Community 115
- Community 116
- Community 117
- Community 118
- Community 119
- Community 120
- Community 121
- Community 122
- Community 123
- Community 124
- Community 126
- Community 128
- Community 129
- Community 130
- Community 131
- Community 132
- Community 133
- Community 134
- Community 135
- Community 136
- Community 137
- Community 138
- Community 139
- Community 140
- Community 141
- Community 142
- Community 143
- Community 144
- Community 145
- Community 146
- Community 147
- Community 148

## God Nodes (most connected - your core abstractions)
1. `LIGHT IR Requirement Traceability Matrix` - 198 edges
2. `ADR-025` - 76 edges
3. `ADR-023` - 76 edges
4. `TestLightIrRevision` - 63 edges
5. `TestLightIrStockAttribution` - 63 edges
6. `TestLightInternalRequest` - 60 edges
7. `ADR-028` - 59 edges
8. `ADR-007` - 57 edges
9. `UAT Test Cases (44 cards)` - 49 edges
10. `TestLightIrAcceptanceEvent` - 48 edges

## Surprising Connections (you probably didn't know these)
- `PRE-UAT Functional Walkthrough (light-ir-m20)` --semantically_similar_to--> `Problem: LIGHT menus not found in main DB (solved)`  [INFERRED] [semantically similar]
  docs/05_uat/PRE_UAT_FUNCTIONAL_WALKTHROUGH.md → issues-backlogs/catatan/Problem-menu-solved.md
- `Decision 04 — Amount boundary explicit definition` --conceptually_related_to--> `TR-CFG-011 — Threshold Validation`  [INFERRED]
  issues-backlogs/meeting-plan-brd.md → docs/02_technical/TR-10-configuration.md
- `Meeting Plan BRD — Approval Matrix Business Rules Draft` --conceptually_related_to--> `LIGHT IR Requirement Traceability Matrix`  [AMBIGUOUS]
  issues-backlogs/meeting-plan-brd.md → docs/04_traceability/RTM.md
- `M20 — End-to-End Regression & Release Candidate` --implements--> `DEC-037 — No Generic Reopen`  [EXTRACTED]
  IMPLEMENTATION_PLAN.md → docs/00_governance/DECISION_REGISTER.md
- `M20 — End-to-End Regression & Release Candidate` --implements--> `DEC-041 — IR Completion Predicate and Controlled Reactivation`  [EXTRACTED]
  IMPLEMENTATION_PLAN.md → docs/00_governance/DECISION_REGISTER.md

## Import Cycles
- None detected.

## Hyperedges (group relationships)
- **Canonical Outstanding Quantity Formula Across Baseline Docs** — docs_00_governance_decision_register_dec_035, docs_00_governance_terminology_outstanding_quantity, docs_02_technical_tr_03_quantity_state_tr_qty_006, docs_01_product_frs_fr_ir_135, docs_01_product_prd_outstanding_quantity [INFERRED 0.90]
- **One Configurable Approval Engine for NEED and FINANCIAL** — docs_00_governance_decision_register_dec_010, docs_02_technical_tr_04_approval_engine_tr_apr_001, docs_01_product_frs_fr_ir_025, docs_01_product_frs_fr_ir_055 [EXTRACTED 0.95]
- **V1 Modular Addon Family Architecture** — docs_02_technical_tr_01_architecture_light_ir_approval, docs_02_technical_tr_01_architecture_light_ir_hr, docs_02_technical_tr_01_architecture_light_ir_purchase, docs_02_technical_tr_01_architecture_light_ir_stock, docs_00_governance_decision_register_dec_004, docs_02_technical_tr_01_architecture_tr_arc_010 [EXTRACTED 0.95]
- **Completion / Outstanding Quantity Governance** — docs_03_architecture_adr_adr_019, docs_03_architecture_adr_adr_020, outstanding_qty_formula, docs_05_uat_uat_glossary_controlled_reactivation [INFERRED 0.85]
- **Historical Correction Mechanisms (Revision/Cancellation/Reversal/Supersede)** — docs_02_technical_tr_09_revision_audit_revision, docs_02_technical_tr_09_revision_audit_cancellation, docs_02_technical_tr_09_revision_audit_reversal, docs_02_technical_tr_09_revision_audit_supersede, docs_03_architecture_adr_adr_028 [EXTRACTED 0.95]
- **UAT V1 Documentation Package (7 documents)** — docs_05_uat_uat_master_plan, docs_05_uat_uat_test_cases, docs_05_uat_uat_master_data_checklist, docs_05_uat_uat_role_matrix, docs_05_uat_uat_glossary, docs_05_uat_uat_defect_log_template, docs_05_uat_uat_signoff_template [EXTRACTED 1.00]

## Communities (154 total, 26 thin omitted)

### Community 0 - "Product FRS Requirements"
Cohesion: 0.06
Nodes (175): FR-IR-001 — Create Internal Request, FR-IR-002 — Unique IR Number, FR-IR-003 — IR Company, FR-IR-004 — Requester, FR-IR-005 — Organizational Context, FR-IR-006 — Request Type, FR-IR-007 — Receiver, FR-IR-008 — Required Date (+167 more)

### Community 1 - "Governance Decision Register"
Cohesion: 0.08
Nodes (112): DEC-031 — Delivery Is Not Acceptance, DEC-033 — Exception Is Not Cancellation, DEC-035 — Canonical Outstanding Quantity, 37. Fulfillment Exception, 51. Outstanding Quantity, FR-IR-091 — Delivered Quantity, FR-IR-092 — Delivery Is Not Acceptance, FR-IR-093 — Acceptance Policy (+104 more)

### Community 2 - "LIGHT Addon Modules Overview"
Cohesion: 0.07
Nodes (79): light_internal_request, light_ir_approval, light_ir_hr, light_ir_purchase, light_ir_stock, light.internal.request, light.internal.request.line, light.ir.acceptance.event (+71 more)

### Community 3 - "Stock Attribution Tests"
Cohesion: 0.07
Nodes (15): tagged, TransactionCase, Submitted + NEED-approved + governance policy configured + Internal route…, Test-only precondition forcing: sets stock.move.state directly via raw SQL…, LIGHT Inventory capability alone does not require, and is not equivalent to,…, Stock Move in Dozen, IR Line in Unit: attributed_qty (Unit) must be converted…, Locked M12 direction: ceiling is product_uom_qty (planned demand), never…, Attribution is a LIGHT linkage concept; it must not touch Odoo's own… (+7 more)

### Community 4 - "Internal Request Core Tests"
Cohesion: 0.05
Nodes (13): tagged, TransactionCase, ADR-009 / TR-03: the ambiguous `fulfilled_qty` must never be introduced.…, Even the Draft's own owner cannot write `state` directly., Submit and submission validation are wired from M06 onward; full behavioral…, Ownership blocks ALL non-su writes on someone else's Draft, not only the fields…, The narrow internal/system escape hatch remains available for a later…, The engine bypass must keep working for legitimate system paths. (+5 more)

### Community 5 - "Product PRD"
Cohesion: 0.03
Nodes (61): 42. Acceptance Reversal, 14. Approval Governance, 17. Approval History, 15. Approval Purpose, 16. Approval Threshold, 62. Architecture Boundary, 63. Baseline Statement, 35. Business Acceptance (+53 more)

### Community 6 - "Fulfillment Exception Tests"
Cohesion: 0.08
Nodes (13): tagged, TransactionCase, A resolution decision only becomes effective through action_resolve()'s own…, Locked M15 decision: cancel_remaining is a recorded decision only. M16…, White-box guard (mirrors M13/M14's own no-heuristic tests): production…, Locked M15 decision: core must not consume…, delivered=10, accepted=6 alone must never auto-create an Exception (TR-08…, M16 legitimately adds outstanding_qty (approved_qty - accepted_qty -… (+5 more)

### Community 7 - "Acceptance Event Tests"
Cohesion: 0.09
Nodes (11): tagged, TransactionCase, LOCKED: no Approval-style self-acceptance prohibition exists in baseline for…, Receiver has ONLY the LIGHT Receiver group — no standard Odoo…, White-box guard (mirrors M13's own no-heuristic tests): production Acceptance…, Manually setting delivered_qty must never itself create an Acceptance Event —…, M15 adds light.ir.fulfillment.exception, but a delivered/ accepted gap alone…, Superseded by M20: this test's original premise ("action_ accept() never… (+3 more)

### Community 8 - "Governance Terminology"
Cohesion: 0.04
Nodes (52): 36. Acceptance, 49. Accepted Quantity, 46. Allocated Quantity, 18. Approval Cycle, 19. Approval Cycle Step, 15. Approval Policy, 16. Approval Rule, 17. Approval Step (+44 more)

### Community 9 - "Purchase Attribution Tests"
Cohesion: 0.10
Nodes (11): tagged, TransactionCase, Submitted + NEED-approved + governance policy configured + Purchase route…, Test-only precondition forcing (M11 SECURITY FINAL): the LIGHT PO Commitment…, PO Line in Dozen, IR Line in Unit: attributed_qty (Unit) must be converted to…, Fail-closed for 'cancel' too (final review): M09 must not invent cancellation…, Create AND quantity-update both remain blocked post-commitment (regression…, No automatic release/reconciliation/reauthorization is triggered merely by a PO… (+3 more)

### Community 10 - "Purchase Sourcing Tests"
Cohesion: 0.07
Nodes (11): tagged, TransactionCase, Submitted + NEED-approved + governance policy configured + Purchase route…, Test-only precondition forcing (M11 SECURITY FINAL): the LIGHT PO Commitment…, LIGHT capability check passes, but creating the standard Odoo purchase.order is…, Holding standard Odoo Purchase rights alone must not grant authority to execute…, End-to-end with the fully-permissioned user: capability + standard Purchase ACL…, ADR-012 example: Allocation=10, three 10-unit RFQ candidates — effective… (+3 more)

### Community 11 - "Purchase M20 Golden Scenarios"
Cohesion: 0.06
Nodes (18): tagged, TransactionCase, Mirrors the established `test_po_commitment_guard.py`/…, No M07 Price Validation exists at all — the price is DISCOVERED via…, Two separate, individually low-value IRs consolidated onto ONE PO Line cross a…, A standard PO with NO LIGHT Purchase Attribution at all must confirm exactly as…, M20: golden end-to-end scenarios requiring a real standard Odoo Purchase Order…, TestLightIrPurchaseM20GoldenScenarios (+10 more)

### Community 12 - "Internal Request Actions (Approve/Submit)"
Cohesion: 0.06
Nodes (24): LightInternalRequest, constrains, depends, model, model_create_multi, Server-side create guard for ordinary (non-su) callers. `readonly=True` on…, Internal Request (IR) header — the LIGHT business document used by an internal…, Allocate the reference from the applicable Company sequence.… (+16 more)

### Community 13 - "PO Commitment Guard Tests"
Cohesion: 0.13
Nodes (12): tagged, TransactionCase, Single unbounded tier, mandatory CFO step -> never self-skips for the…, M11 Tier-2: PO Commitment Authority (purchase.order write() guard). Distinct…, Odoo TransactionCase's `assertRaises` context manager wraps the block in a…, Quantity stays fully within its ceiling (M09 invariant holds: attributed 10 <=…, The resolved FINANCIAL approver IS the confirming user himself, with…, Pinned to a fixed instant via `freeze_time` (project's supported test-time-… (+4 more)

### Community 14 - "Accept Wizard"
Cohesion: 0.04
Nodes (23): LightIrAcceptWizard, model, M19 UX: thin parameter-collection wizard over…, LightIrCancelWizard, model, M19 UX: thin parameter-collection wizard over…, LightIrExceptionReportWizard, M19 UX: thin parameter-collection wizard over… (+15 more)

### Community 15 - "Community 15"
Cohesion: 0.07
Nodes (15): tagged, TransactionCase, LIGHT Cancellation is deliberately independent from LIGHT Receiver (locked…, Unconditional-security pattern (M11-M15 precedent): the business action itself…, Baseline positive path, restated explicitly for the locked Authority decision…, The Assignment model's own _check_overlap constraint makes an ambiguous state…, Locked M16 Authority decision: reuses only the standalone Responsibility…, NEED/FINANCIAL remain the only Approval Engine purposes (ADR-005/DEC-010) — the… (+7 more)

### Community 16 - "Community 16"
Cohesion: 0.07
Nodes (14): tagged, TransactionCase, Submitted but NOT yet NEED-approved: request.state == 'submitted',…, No invented self-validation prohibition (unlike NEED/FINANCIAL Approval self-…, A. Price MAY be validated while NEED Approval is still pending (operational…, B. Once NEED Approval completes, approved_qty becomes authoritative and…, C. The financial basis M11 would snapshot uses the EFFECTIVE (currently active)…, D. requested_qty remains unchanged throughout early (pending) Price Validation… (+6 more)

### Community 17 - "Community 17"
Cohesion: 0.09
Nodes (7): tagged, TransactionCase, Submitted but NOT yet NEED-approved: approved_qty not authoritative., Rejection does NOT freeze allocations: the Reviewer may still adjust, and the…, M08: Fulfillment Allocation & Planning (light.ir.fulfillment.allocation),…, Submitted + fully NEED-approved line: approved_qty authoritative., TestLightFulfillmentAllocation

### Community 18 - "Community 18"
Cohesion: 0.10
Nodes (4): tagged, TransactionCase, M17: Revision (light.ir.revision,…, TestLightIrRevision

### Community 19 - "Community 19"
Cohesion: 0.08
Nodes (13): tagged, TransactionCase, Confirming with every line's approved_qty left at its default (==…, The wizard adds no authority: an unresolved user's confirm() must still be…, FINANCIAL cycles use a deliberately separate financial_internal_request_id…, is_pending_acceptance == delivered_qty > accepted_qty exactly (M14's own…, The exact scenario flagged in final verification: approved=10, delivered=5,…, M21 manual UI review defect correction: 'Mark Reviewed', 'Approve Plan' and… (+5 more)

### Community 20 - "Community 20"
Cohesion: 0.10
Nodes (4): tagged, TransactionCase, M06: Submit and NEED Approval integration — submission validation and…, TestLightInternalRequestSubmit

### Community 21 - "Community 21"
Cohesion: 0.06
Nodes (34): DEC-001 — Odoo Version, DEC-002 — Initial Deployment Edition, DEC-003 — Standard Odoo First, DEC-004 — Modular Addon Architecture, DEC-005 — HR Integration Boundary, DEC-006 — Request Type Is Configurable Governance Context, DEC-007 — Department/Division Uses Organizational Master, DEC-008 — Company Governance Can Differ (+26 more)

### Community 22 - "Community 22"
Cohesion: 0.06
Nodes (35): UAT Test Cases (44 cards), UAT-CP-02, UAT-CP-03, UAT-CP-04, UAT-CP-05, UAT-CP-06, UAT-CP-08, UAT-CP-10 (+27 more)

### Community 23 - "Community 23"
Cohesion: 0.08
Nodes (15): LightInternalRequestLine, constrains, depends, model_create_multi, TR-SEC-001/003/004: LIGHT Reviewer capability (V.4 — the sole M08 planning…, FR-IR-046..050. Public, transaction-aware entry point: runs the full…, The SOLE authoritative source for the Financial Amount Basis (FR-IR-056):…, One-time default-seed source for price_estimate (M21): Product Cost only, never… (+7 more)

### Community 24 - "Community 24"
Cohesion: 0.12
Nodes (6): tagged, TransactionCase, A Request Type shared across Companies (company_id=False) must not let one…, Defense-in-depth: even though light.ir.fulfillment.allocation's own…, M08 correction pass: light.ir.policy (request-level governance configuration…, TestLightGovernancePolicy

### Community 25 - "Community 25"
Cohesion: 0.11
Nodes (9): tagged, TransactionCase, A requester outside the resolved Company must fail closed: the read-only sudo()…, Requester legitimately belongs to the Company but has no employee record there:…, light_ir_hr stays the only HR-coupled LIGHT module (TR-ARC-003, ADR-004): the…, ADR-006 / TR-APR-005: reassigning the department manager after the cycle exists…, M04: HR-derived responsibility resolution plugged into the M02/M03 resolution…, M02 behaviour must be untouched where no HR rule applies. (+1 more)

### Community 26 - "Community 26"
Cohesion: 0.06
Nodes (5): tagged, TransactionCase, A user may not drive the elevated engine creator into a Company outside their…, M03: Approval Cycle Engine — eager full-snapshot generation, sequential…, TestLightIrApprovalCycle

### Community 27 - "Community 27"
Cohesion: 0.11
Nodes (6): tagged, TransactionCase, Even where base.group_user's own ACL grants broad write on…, Regression: Administrator capability alone must not grant NEED approval…, M18: Security / Configuration Hardening. Two locked deliverables tested here:…, TestLightIrSecurityHardening

### Community 28 - "Community 28"
Cohesion: 0.16
Nodes (10): tagged, TransactionCase, M13b: Purchase Receipt Attribution — execution-level linkage between a realized…, Locked M13b decision #8: gross/historical — a later vendor return must never…, IR Line uses dozen while the product/PO line/stock move stay in the product's…, LIGHT Procurement alone, with NO standard Odoo Purchase or Inventory rights at…, Standard Purchase + Inventory rights, NO LIGHT Procurement: CAN perform the…, IR Line + Purchase Attribution in Company A; a receipt stock.move constructed… (+2 more)

### Community 29 - "Community 29"
Cohesion: 0.08
Nodes (10): tagged, TransactionCase, M04: user -> employee -> department -> manager resolution over standard Odoo…, Only ACTIVE employees may resolve (explicit active=True domain)., The active filter is stated explicitly in the domain, so a caller carrying…, Archiving in one Company must not disturb resolution in another., The genuinely possible multi-match case is one employee per Company: the…, Standard Odoo enforces `unique (user_id, company_id)` on hr.employee, so two… (+2 more)

### Community 30 - "Community 30"
Cohesion: 0.07
Nodes (28): Responsibility Assignment — light.ir.responsibility.assignment, UAT Role Matrix, Administrator, Auditor, Cancellation Authority, Delegate, Financial Approver, Financial Officer (+20 more)

### Community 31 - "Community 31"
Cohesion: 0.14
Nodes (8): tagged, TransactionCase, TR-03's own 'Cancellation Example' (accepted=6, cancelled=4, outstanding=0) is…, Isolates the Exception blocker specifically: delivered exactly matches accepted…, Cancellation is not a reactivation trigger — its own ceiling check makes it a…, Documented M20 design-closure edge case: a line whose approved_qty is 0…, M20: IR Completion / Controlled Reactivation (FR-IR-136..145, ADR-020, ADR-022,…, TestLightIrM20Completion

### Community 32 - "Community 32"
Cohesion: 0.15
Nodes (9): tagged, TransactionCase, Standard Odoo's own manual entry for service qty_received must have zero effect…, M14 adds `light.ir.acceptance.event`, but confirming Service Delivery (M13b)…, Standard Odoo Purchase rights alone (NO LIGHT Procurement) must not be…, `sudo()` does not change `self.env.user` (Odoo's own contract) — an…, Model-level Company guard (`_check_company_boundary`), hit directly via sudo…, M13b: Service Delivery Event — structured, immutable evidence that a purchased… (+1 more)

### Community 33 - "Community 33"
Cohesion: 0.12
Nodes (8): tagged, TransactionCase, `create_backorder == 'never'`: standard Odoo discards the undelivered remainder…, Proves `_reconcile_realized_move()`'s explicit `modified(["attributed_qty"])`…, Regression guard for `delivered_qty` specifically (the definitive move.state-…, Exercises the GENUINE default `create_backorder == 'ask'` path (no…, M13: Backorder Attribution Reconciliation (FR-IR-086/087, TR-STK-006/007) via…, TestLightIrBackorderReconciliation

### Community 34 - "Community 34"
Cohesion: 0.08
Nodes (11): tagged, TransactionCase, DEC-006 / PRD §8: Request Type must not encode approvers, thresholds, review…, Business logic must never branch on a Request Type name/code (TR-CFG-001: 'if…, M05: Request Type is pure configuration/master data (FR-IR-006, FR-IR-149,…, Application-level ValidationError fires (deterministic, actionable message —…, Issue 2: `company_id IS NULL` is its own uniqueness scope — two shared Request…, The partial unique index is a race-condition backstop: even a raw SQL insert… (+3 more)

### Community 35 - "Community 35"
Cohesion: 0.12
Nodes (13): LightInternalRequestLine, depends, Compatibility for an EXISTING candidate PO Line target: RFQ lifecycle…, FR-IR-066. Creates a NEW draft `purchase.order` + `purchase.order.line`…, FR-IR-071 (Consolidate compatible IR into an existing draft RFQ). If…, FR-IR-069/070 (Vendor Award / Split Award). Selects a 'candidate'…, Explicit, non-cascading Loser marking (FR-IR-069 counterpart): never triggered…, TR-SEC-001/003/004/008: LIGHT Procurement capability, Company access (both the… (+5 more)

### Community 36 - "Community 36"
Cohesion: 0.18
Nodes (6): tagged, TransactionCase, A line with no Purchase route contributes nothing (no vendor commercial…, Tier-2 (light_ir_purchase) now resolves FINANCIAL Policy with request_type_id…, M11 Tier-1: IR Financial Authorization…, TestLightFinancialAuthorization

### Community 37 - "Community 37"
Cohesion: 0.15
Nodes (6): tagged, TransactionCase, LIGHT Inventory capability alone must NOT grant standard Odoo…, Two lines with different, category-compatible UoM consolidated onto one move…, M13: Internal Transfer Execution — Create Transfer (FR-IR-082..086) and…, TestLightIrInternalTransfer

### Community 38 - "Community 38"
Cohesion: 0.13
Nodes (10): tagged, TransactionCase, Request -> NEED -> internal Allocation -> simulated internal delivery ->…, One Approved Quantity split across both routes; both routes' delivery…, DEC-034 (Replacement Must Not Double Count): resolving with…, A valid, currently-effective Delegation lets the delegate act with the SAME…, A NEED step whose resolved approver IS the requester is skipped, not self-…, A user in a DIFFERENT Company cannot read the IR/Line, cannot act on it even… (+2 more)

### Community 39 - "Community 39"
Cohesion: 0.11
Nodes (6): tagged, TransactionCase, Reproduces the exact reported browser defect using Form(), which follows the…, M21: department_id is a Draft-resolvable, Submit-frozen snapshot on…, Manual UI defect correction: department_id must be persisted at create() time,…, TestM21DepartmentSnapshot

### Community 40 - "Community 40"
Cohesion: 0.10
Nodes (20): NFR-IR-001 — Odoo 18 Compatibility, NFR-IR-002 — Standard Odoo First, NFR-IR-003 — Community-Compatible Core, NFR-IR-004 — Modular Architecture, NFR-IR-005 — Server-Side Business Integrity, NFR-IR-006 — Security by Design, NFR-IR-007 — Multi-Company Isolation, NFR-IR-008 — Auditability (+12 more)

### Community 41 - "Community 41"
Cohesion: 0.20
Nodes (5): tagged, TransactionCase, Different specificity tiers are never merged: an exact match together with an…, M06 correction (ISSUE 2): Request Type as an explicit, deterministic Approval…, TestLightIrApprovalPolicyRequestType

### Community 42 - "Community 42"
Cohesion: 0.13
Nodes (11): LightIrPurchaseAttribution, constrains, model, model_create_multi, IR-side invariant (Decision 3): sum(EFFECTIVE attributed_qty for the IR Line)…, PO-Line-side invariant (Decision 3): sum(EFFECTIVE attributed_qty across IR…, CENTRALIZED PO-lifecycle guard — the single source of truth for every M09…, PRIVATE ENGINE API — deliberately not RPC-callable directly. Elevates… (+3 more)

### Community 43 - "Community 43"
Cohesion: 0.11
Nodes (11): LightInternalRequestLine, LightIrServiceDeliveryEvent, constrains, depends, model_create_multi, Cumulative Service Delivery for (IR Line, PO Line) shall not exceed that IR…, M13b: Service Delivery business action + `service_delivered_qty` derivation.…, TR-SEC-001/003/004/008 pattern (mirrors… (+3 more)

### Community 44 - "Community 44"
Cohesion: 0.13
Nodes (10): LightIrRevision, constrains, model_create_multi, FR-IR-133: at most one active material Revision per line., Locked M17 decision #4: proposed quantity must never go below accepted_qty +…, M17 final verification (PROBE): a trusted `sudo()` caller could otherwise…, Locked M17 decision #9: a Revision becomes `rejected` automatically the moment…, Locked M17 decision #10 — atomic apply, single transaction, no partially-… (+2 more)

### Community 45 - "Community 45"
Cohesion: 0.13
Nodes (10): LightIrPurchaseSourcing, constrains, model, model_create_multi, Partial unique index: at most one non-'lost' sourcing record per (line_id,…, Individual cap only — each candidate must not exceed purchase_allocated_qty on…, PRIVATE ENGINE API — deliberately not RPC-callable directly. Real authorization…, PRIVATE ENGINE API. Marks a 'candidate' record as the effective sourcing… (+2 more)

### Community 46 - "Community 46"
Cohesion: 0.15
Nodes (9): Simulation 6: employee changes Department after Submit must not retroactively…, M21: price_estimate mandatory>0 at Submit (DEC-011 superseded), Estimated…, Simulation 1/4: qty x price_estimate per line, summed at header., Product Cost seeds price_estimate via onchange when a catalog Product is…, Differing UoM at seed time is real (line UoM set before Product is picked, or…, Simulation 2: override — the value present at Submit is the approval evidence,…, Simulation 7: Product Cost changing after Submit never changes the frozen…, Simulation 3: unregistered requirement (no Product) has no Product Cost to seed… (+1 more)

### Community 47 - "Community 47"
Cohesion: 0.16
Nodes (4): M21 worked simulation: Aji (Growth Marketing) x tiered NEED Rules [0,5m)…, Simulation 5: Esty is Department Head; her own Head step is skipped (existing…, Two lines individually below [5m,10m)'s lower bound (3m + 3m = 6m) must…, TestM21GrowthMarketingSimulation

### Community 48 - "Community 48"
Cohesion: 0.13
Nodes (10): BaseCase, mute_logger, tagged, Runs one side of a concurrent Accept/Reversal race. `barrier` has a bounded…, delivered=10, accepted=6 (seeded). Two threads both try +4. Exactly one…, delivered=10, accepted=0. Two threads both try +7 (only one can fit; together…, accepted=10 (seeded). Two threads both try to reverse 7 (together they would…, Two DIFFERENT IR Lines, accepted concurrently, must both succeed quickly — the… (+2 more)

### Community 49 - "Community 49"
Cohesion: 0.14
Nodes (9): LightIrPurchaseReceiptAttribution, constrains, model, model_create_multi, Defense-in-depth: cumulative Receipt Attribution for (IR Line, PO Line) shall…, Cumulative Receipt Attribution for a given receipt move shall not exceed that…, PRIVATE ENGINE API — trusted, system-triggered post-…, M13b: Purchase Receipt Attribution — the execution-level linkage between a… (+1 more)

### Community 50 - "Community 50"
Cohesion: 0.18
Nodes (17): Deterministic Policy Resolution, Delegation — light.ir.approval.delegation, Meeting Plan BRD — Approval Matrix Business Rules Draft, BR-APR-001 — Approval configuration harus dapat berbeda untuk setiap divisi, BR-APR-003 — Head merupakan approval level pertama apabila posisi Head tersedia, BR-APR-005 — Jika posisi Head vacant, request diteruskan ke C-Level terkait, BR-APR-006 — Setiap divisi memiliki C-Level terkait, BR-APR-008 — Jenis pengeluaran dapat diklasifikasikan budgeted/unbudgeted (+9 more)

### Community 51 - "Community 51"
Cohesion: 0.12
Nodes (8): LightIrApprovalCyclePurchaseLink, PRIVATE ENGINE-EXTENSION API — self-guarded. Refuses to (a) re-link an already-…, M11: PO Commitment Authority (Tier-2) linkage. Purpose-specific, exactly like…, LightInternalRequestLine, depends, M13b: `purchase_delivered_qty` derivation (TR-06 "Receipt", TERMINOLOGY #48…, LightIrPurchaseAttributionSourcingTraceability, M10 non-invasive extension of the LOCKED M09 model: adds only a read-only…

### Community 52 - "Community 52"
Cohesion: 0.12
Nodes (16): action_request_financial_authorization (internal_request_financial.py), PRE-UAT Functional Walkthrough (light-ir-m20), DEF-PU-01 — FINANCIAL Authorization Tier-1 has no UI entry point, F01 — Buat & Submit Internal Request, F02 — NEED Approval (Penuh), F03 — NEED Approval (Sebagian / Quantity Reduction), F04 — Review + Fulfillment Allocation, F05 — Plan Approval (+8 more)

### Community 53 - "Community 53"
Cohesion: 0.12
Nodes (8): LightIrApprovalCycleFinancial, PRIVATE ENGINE-EXTENSION API — self-guarded, mirrors M06's…, M11: IR Financial Authorization (Tier-1) linkage. Deliberately a SEPARATE field…, LightIrApprovalCycleStep, M19 UX: read-only pass-through to the NEED-only `internal_request_id` added by…, LightIrApprovalPolicy, model, Adds Request Type as an explicit, deterministic Approval Policy resolution…

### Community 54 - "Community 54"
Cohesion: 0.15
Nodes (8): LightIrFulfillmentException, constrains, model_create_multi, M15 final verification (PROBE A): `status`/`resolution_type` must agree in both…, Narrow, per-exception concurrency serialization for `action_resolve()` —…, FR-IR-106..111. Authorization mirrors reporting (same…, M15 final verification (PROBES B/C): a resolved Exception is frozen, full stop…, M15: Fulfillment Exception — structured record of a business problem with a…

### Community 56 - "Community 56"
Cohesion: 0.17
Nodes (8): PurchaseOrderCommitmentGuard, Tier-2 FINANCIAL Policy resolution: Company + Purpose('financial') + date, with…, Deterministic 'harmless change' test (TR-APR-012), identical in shape to…, FR-IR-076/077, TR-PUR-010/011, ADR-013. Fail-closed: blocks unless a valid,…, SINGLE centralized guard point for every path that can move a LIGHT-linked PO…, Deterministic LIGHT-vs-non-LIGHT test (FR-IR-075): at least one ACTIVE…, M11: PO Commitment Authority (Tier-2), distinct from Tier-1 IR Financial…, The PO's real, actual standard-Odoo commercial value, proportionally attributed…

### Community 57 - "Community 57"
Cohesion: 0.16
Nodes (7): tagged, TransactionCase, A user with standard Odoo Inventory rights but NO LIGHT Inventory capability…, A sudo-wrapped `button_validate()` call must still run the SAME reconciliation…, A picking/move with NO LIGHT Attribution at all must behave exactly like…, M13 SECURITY: `stock.picking.button_validate()`'s LIGHT reconciliation must be…, TestLightIrButtonValidateSecurity

### Community 58 - "Community 58"
Cohesion: 0.13
Nodes (14): DEC-010 — One Approval Engine, 13. Need Approval, TR-APR-001 — Common Approval Engine, TR-APR-002 — Explicit Approval Purpose, TR-APR-003 — Deterministic Policy Resolution, TR-APR-004 — Approval Cycle Snapshot, TR-APR-006 — Deterministic Threshold Boundaries, TR-APR-007 — Configurable Tax Basis (+6 more)

### Community 59 - "Community 59"
Cohesion: 0.13
Nodes (14): DEC-025 — Purchase Attribution Is Authoritative, 31. Purchase Attribution, TR-PUR-001 — Reuse Standard Odoo Purchase, TR-PUR-002 — Purchase Allocation Separate From Execution, TR-PUR-003 — Structured Purchase Attribution, TR-PUR-004 — origin Is Informational, TR-PUR-005 — One IR to Multiple Purchase Lines, TR-PUR-006 — Multiple IRs to One PO Line (+6 more)

### Community 60 - "Community 60"
Cohesion: 0.14
Nodes (15): TR-11 Testing Strategy and Definition of Done, Milestone Gate, TR-TST-001 — Tests Are Definition of Done, TR-TST-002 — Server-Side Governance Tests, TR-TST-003 — Negative Tests Required, TR-TST-004 — Threshold Boundary Tests, TR-TST-005 — Multi-Company Tests, TR-TST-006 — Idempotency Tests (+7 more)

### Community 61 - "Community 61"
Cohesion: 0.13
Nodes (15): UAT Bilingual Glossary, Acceptance — light.ir.acceptance.event, Acceptance Reversal, Approval Cycle — light.ir.approval.cycle, Approval Cycle Step — light.ir.approval.cycle.step, Fulfillment Allocation — light.ir.fulfillment.allocation, Fulfillment Exception — light.ir.fulfillment.exception, Internal Request (IR) — light.internal.request (+7 more)

### Community 62 - "Community 62"
Cohesion: 0.18
Nodes (8): LightInternalRequestLine, depends, Narrow, per-line concurrency serialization for Acceptance/ Reversal. Mirrors…, FR-IR-095/098 (TR-08 "Acceptance Event"). Creates ONE immutable…, TR-ACC-006 (Reversal Preserves Original Event): a correction creates a NEW…, M14: Acceptance Engine — `action_accept()`/ `action_reverse_acceptance()` and…, PRD #36: Receiver may default to Requester but is not hard-coded as the…, TR-SEC-001/003/004/008 pattern (mirrors `_check_purchase_…

### Community 63 - "Community 63"
Cohesion: 0.15
Nodes (8): BaseCase, mute_logger, tagged, Two threads both try to resolve the SAME open Exception with the SAME…, Two threads race to resolve the SAME open Exception with DIFFERENT resolution…, Two DIFFERENT Exception records, resolved concurrently, must both succeed…, M15 concurrency proof: two concurrent action_resolve() calls on the SAME open…, TestFulfillmentExceptionConcurrency

### Community 64 - "Community 64"
Cohesion: 0.19
Nodes (8): LightIrStockAttribution, model, model_create_multi, CENTRALIZED stock.move-lifecycle guard — the single source of truth for every…, PRIVATE ENGINE API — deliberately not RPC-callable directly. Elevates…, PRIVATE ENGINE API. Marks EFFECTIVE attribution(s) as released. Release is NOT…, Stock Attribution — explicit, quantity-aware relationship between LIGHT…, Partial unique index: at most one ACTIVE attribution per (line_id,…

### Community 65 - "Community 65"
Cohesion: 0.18
Nodes (7): LightIrFulfillmentAllocation, constrains, model, model_create_multi, PRIVATE ENGINE API — deliberately not RPC-callable directly. Elevates…, Fulfillment Allocation — structured quantity plan assigning Approved Quantity…, FR-IR-050: the aggregate EFFECTIVE Fulfillment Allocation for an IR Line shall…

### Community 66 - "Community 66"
Cohesion: 0.21
Nodes (7): LightInternalRequestFinancial, Deterministic 'harmless change' test (TR-APR-012): the existing Cycle's own…, FR-IR-055..063. Explicit Tier-1 entry point. Idempotent/lazy: a second call…, TR-SEC-001/003/004: LIGHT Financial Officer capability and applicable Company…, M11: IR Financial Authorization (Tier-1). Explicit, user-triggered business…, Lines with a Purchase route (purchase_allocated_qty > 0) — see class docstring…, Resolves the applicable FINANCIAL Policy (Company + Request Type context,…

### Community 67 - "Community 67"
Cohesion: 0.20
Nodes (6): tagged, TransactionCase, M13b: canonical, route-agnostic `delivered_qty` lives in core…, No stock/purchase addon is installed in this test suite, so the three route…, Delivery != Acceptance (ADR-016); delivered_qty is not derived from…, TestLightDeliveredQtyCore

### Community 68 - "Community 68"
Cohesion: 0.21
Nodes (7): LightInternalRequestLine, Locked M17 decision #5. Blocks Product change once ANY execution/attribution…, Evaluates the effect of a proposed quantity on the IR-level FINANCIAL basis,…, FR-IR-120..130. Creates ONE `active` `light.ir.revision` record. Does NOT…, Locked M17 Authority order: LIGHT Revision Authority capability -> Company…, M17: Revision — `action_propose_revision()`/`action_apply_ revision()` (the…, Narrow, per-line concurrency serialization for Revision propose/apply. Mirrors…

### Community 69 - "Community 69"
Cohesion: 0.17
Nodes (7): BaseCase, mute_logger, tagged, approved=10, cancelled=0. Two threads both try -6 (only one can fit; together…, Two DIFFERENT IR Lines, cancelled concurrently, must both succeed quickly — the…, M16 concurrency-closure: genuine multi-transaction proof that the per-line…, TestCancellationConcurrency

### Community 70 - "Community 70"
Cohesion: 0.17
Nodes (7): BaseCase, mute_logger, tagged, Two threads both try to propose a Revision on the SAME line at the same…, Two DIFFERENT IR Lines, Revision proposed concurrently, must both succeed…, M17 concurrency-closure: genuine multi-transaction proof that the per-line…, TestRevisionConcurrency

### Community 71 - "Community 71"
Cohesion: 0.26
Nodes (5): LightIrApprovalCycleStep, model_create_multi, State validity + sequential actionability + company boundary. Enforced…, Approval Cycle Step — historical transactional representation of one resolved,…, Resolve the effective actor: either the resolved approver themselves, or a…

### Community 72 - "Community 72"
Cohesion: 0.15
Nodes (4): tagged, TransactionCase, M01 smoke test: module installs and registry is reachable. No business model…, TestLightIrStockSkeleton

### Community 73 - "Community 73"
Cohesion: 0.17
Nodes (11): DEC-016 — Capability Does Not Equal Transaction Authority, TR-SEC-001 — Server-Side Critical Authorization, TR-SEC-002 — UI Restrictions Are Not Security, TR-SEC-003 — Transaction-Specific Authority, TR-SEC-004 — Company Boundary Enforcement, TR-SEC-005 — Admin Is Not Automatic Approver, TR-SEC-006 — Auditor Is Read-Only, TR-SEC-007 — Standard Odoo Security Still Applies (+3 more)

### Community 74 - "Community 74"
Cohesion: 0.17
Nodes (11): DEC-029 — Standard Odoo Inventory Executes Stock, TR-STK-001 — Reuse Standard Odoo Inventory, TR-STK-002 — Internal Allocation Separate From Stock Move, TR-STK-003 — Explicit Stock Attribution, TR-STK-004 — Attribution Quantity Integrity, TR-STK-005 — Partial Done Drives Delivery, TR-STK-006 — Backorder Preserves Attribution, TR-STK-007 — Deterministic Consolidated Attribution (+3 more)

### Community 75 - "Community 75"
Cohesion: 0.20
Nodes (5): LightIrAcceptanceEvent, constrains, model_create_multi, M14: Acceptance Event — structured historical business acknowledgement that a…, Single unified invariant, enforced on every event regardless of type: `0 <=…

### Community 76 - "Community 76"
Cohesion: 0.20
Nodes (5): LightIrCancellation, constrains, model_create_multi, M16: Cancellation — structured, immutable business event that removes remaining…, FR-IR-117 / locked M16 decision #4: `accepted_qty + cancelled_qty <=…

### Community 77 - "Community 77"
Cohesion: 0.21
Nodes (6): LightInternalRequestLine, depends, Narrow, per-line concurrency serialization for Cancellation. Mirrors…, FR-IR-114..117. Creates ONE immutable Cancellation event. Does not mutate…, M16: Cancellation — `action_cancel()` and the canonical…, Locked M16 Authority order: LIGHT Cancellation capability (coarse gate) ->…

### Community 78 - "Community 78"
Cohesion: 0.20
Nodes (6): LightIrPriceValidation, constrains, model, model_create_multi, PRIVATE ENGINE API — deliberately not RPC-callable directly. Elevates…, Price Validation — structured historical event recording an authoritative price…

### Community 79 - "Community 79"
Cohesion: 0.20
Nodes (6): LightIrRequestType, constrains, model_create_multi, Physical, race-condition-safe backstop for `_check_code_uniqueness`…, Deterministic uniqueness that does not rely on PostgreSQL NULL semantics: a…, Request Type — configurable master data classifying the business purpose of an…

### Community 80 - "Community 80"
Cohesion: 0.17
Nodes (4): tagged, TransactionCase, M01 smoke test: module installs and registry is reachable. No business model…, TestLightIrApprovalSkeleton

### Community 81 - "Community 81"
Cohesion: 0.17
Nodes (4): tagged, TransactionCase, M02: deterministic policy resolution (FR-IR-026, TR-APR-003, TR-…, TestLightIrApprovalPolicy

### Community 82 - "Community 82"
Cohesion: 0.20
Nodes (6): LightIrHrResponsibilityRule, constrains, model, Derive the applicable approver user from standard Odoo HR., Maps a configured Responsibility code to an HR-derived approver instead of a…, Return applicable active HR rules (0, 1, or — invalidly — more).

### Community 83 - "Community 83"
Cohesion: 0.17
Nodes (4): tagged, TransactionCase, M01 smoke test: module installs and registry is reachable. No business model…, TestLightIrHrSkeleton

### Community 84 - "Community 84"
Cohesion: 0.30
Nodes (3): LOCKED M21 governance-defect correction: Department resolution is mandatory…, Standard Odoo enforces `hr_employee_user_uniq` (user_id, company_id) at the DB…, TestM21GovernanceRelevance

### Community 85 - "Community 85"
Cohesion: 0.30
Nodes (4): tagged, TransactionCase, M18: `group_light_auditor` read-only coverage for `light_ir_stock`'s own…, TestLightIrStockSecurityHardening

### Community 86 - "Community 86"
Cohesion: 0.22
Nodes (6): BaseCase, mute_logger, tagged, Both lines' Acceptance completes concurrently. Either both connections succeed…, M20 concurrency closure: genuine multi-transaction proof that the IR-header…, TestCompletionConcurrency

### Community 88 - "Community 88"
Cohesion: 0.24
Nodes (6): LightIrApprovalCycle, model, model_create_multi, PRIVATE ENGINE API — deliberately not RPC-callable. This method elevates…, Move `current_step_id` to the next still-waiting step in sequence, or complete…, Approval Cycle — transactional snapshot of a resolved approval process for a…

### Community 89 - "Community 89"
Cohesion: 0.29
Nodes (7): LightIrHrResolver, model, Full chain: requester user -> their department's manager user., res.users -> active hr.employee within `company`. `active = True` is stated…, Organizational resolution service over standard Odoo HR. Resolves,…, hr.employee -> hr.department, Company-checked., hr.department -> manager hr.employee -> res.users, Company-checked.

### Community 91 - "Community 91"
Cohesion: 0.20
Nodes (6): LightInternalRequestLine, depends, FR-IR-079..081/088. Public, transaction-aware entry point: runs the full…, Releases the ACTIVE attribution (if any) for (self, stock_move). A full…, TR-SEC-001/003/004/008: LIGHT Inventory capability, Company access (both the…, Stock-specific extension (TR-ARC-005: isolated in `light_ir_stock` — core…

### Community 92 - "Community 92"
Cohesion: 0.20
Nodes (9): TR-QTY-001 — Server-Side Quantity Invariants, TR-QTY-002 — Requested and Approved Separate, TR-QTY-003 — Not Approved Is Not Cancellation, TR-QTY-004 — Allocation Separate From Execution, TR-QTY-005 — Delivered Separate From Accepted, TR-QTY-007 — Aggregate Quantities From Authoritative Records, TR-QTY-008 — Partial Execution Supported, TR-QTY-009 — Standard Odoo UoM Semantics (+1 more)

### Community 93 - "Community 93"
Cohesion: 0.22
Nodes (10): Cancellation, Revision, F12 — Revision (Reaktivasi) & Cancel Outstanding, Cancellation — light.ir.cancellation, Revision — light.ir.revision, UAT-CP-09, UAT-GS-09, UAT-GS-10 (+2 more)

### Community 94 - "Community 94"
Cohesion: 0.24
Nodes (5): LightIrApprovalRule, constrains, model, Deterministic rule resolution within a Policy by threshold amount., Approval Rule — applicable approval behavior under a Policy, including…

### Community 95 - "Community 95"
Cohesion: 0.24
Nodes (5): LightIrResponsibilityAssignment, constrains, model, Responsibility Assignment — resolves a configured responsibility code to an…, Deterministic responsibility resolution (TR-CFG-006/007/008).

### Community 96 - "Community 96"
Cohesion: 0.20
Nodes (4): tagged, TransactionCase, M02: deterministic threshold-based rule resolution (FR-IR-154, FR-IR-155, TR-…, TestLightIrApprovalRule

### Community 97 - "Community 97"
Cohesion: 0.22
Nodes (5): LightInternalRequest, depends, Resolve the Requester's HR Department for a single Internal Request. Returns an…, Department Approval-Routing Dimension (M21). `department_id` is an immutable…, Evaluate Department-aware NEED routing relevance and supply the already-…

### Community 98 - "Community 98"
Cohesion: 0.31
Nodes (4): tagged, TransactionCase, M06 x M04 integration: `light.internal.request.action_submit()` /…, TestLightInternalRequestHrSubmit

### Community 99 - "Community 99"
Cohesion: 0.27
Nodes (5): LightInternalRequestLine, depends, FR-IR-080 eligibility (only authorized/eligible remaining Internal Allocation)…, M13: Internal Transfer Execution (FR-IR-082..090, TR-STK-005/006/007).…, FR-IR-082. Creates standard Odoo `stock.picking` + `stock.move` (draft,…

### Community 100 - "Community 100"
Cohesion: 0.25
Nodes (5): LightIrApprovalPolicy, constrains, model, Approval Policy — governance configuration determining which Approval Rules…, Deterministic policy resolution (TR-APR-003, TR-CFG-006/007/008). Returns…

### Community 101 - "Community 101"
Cohesion: 0.22
Nodes (4): tagged, TransactionCase, M02: delegation validity and audit-preserving fields (FR-IR-041, FR-IR-170, FR-…, TestLightIrApprovalDelegation

### Community 102 - "Community 102"
Cohesion: 0.22
Nodes (4): tagged, TransactionCase, M02: deterministic responsibility resolution (FR-IR-150, TR-CFG-006)., TestLightIrResponsibilityAssignment

### Community 103 - "Community 103"
Cohesion: 0.25
Nodes (8): FINANCIAL Approval — authorizes financial exposure (Tier-1/Tier-2), NEED Approval — authorizes approved_qty, UAT-AP-01, UAT-AP-02, UAT-AP-03, BR-APR-002 — Approval route dapat ditentukan berdasarkan nilai permintaan, BR-APR-007 — CFO/CEO dapat ditambahkan ke approval chain berdasarkan threshold amount, Decision 01 — NEED vs FINANCIAL split for Head/C-Level/CFO/CEO

### Community 104 - "Community 104"
Cohesion: 0.25
Nodes (5): LightIrPolicy, constrains, model, Request-level governance configuration owner for Review Requirement (FR-…, Deterministic two-tier resolution (TR-10). `request_type` may be an empty…

### Community 105 - "Community 105"
Cohesion: 0.29
Nodes (4): LightIrApprovalDelegation, constrains, Expired or otherwise invalid delegation shall not authorize action (TR-04, FR-…, Approval Delegation — permits a delegate to act for an original responsibility…

### Community 106 - "Community 106"
Cohesion: 0.25
Nodes (3): LightIrApprovalCycle, model, Optional HR extension seam for the Approval Cycle Engine. The M03 engine…

### Community 107 - "Community 107"
Cohesion: 0.32
Nodes (5): LightIrApprovalPolicyCommitment, model, Narrow, explicit M11 override: when the caller has set the named context flag…, M11 Tier-2: explicit GLOBAL-ONLY FINANCIAL Policy resolution for PO Commitment…, Deterministic Tier-2 resolution: exactly one active Policy with…

### Community 109 - "Community 109"
Cohesion: 0.29
Nodes (4): tagged, TransactionCase, M20: non-LIGHT Stock regression — a standard internal transfer with NO…, TestLightIrStockM20GoldenScenarios

### Community 110 - "Community 110"
Cohesion: 0.29
Nodes (4): tagged, TransactionCase, M01 smoke test: module installs and registry is reachable. No business model…, TestLightInternalRequestSkeleton

### Community 111 - "Community 111"
Cohesion: 0.29
Nodes (3): LightIrApprovalCycleStepLine, model_create_multi, Approval Cycle Step Line — one immutable, append-only recorded action event…

### Community 112 - "Community 112"
Cohesion: 0.33
Nodes (4): LightIrApprovalPolicy, model, LOCKED M21 governance-defect correction. Department resolution is mandatory…, Adds Department as a second, independent Approval Policy resolution dimension…

### Community 113 - "Community 113"
Cohesion: 0.38
Nodes (4): LightIrResponsibilityAssignment, model, Fail closed on the requester context before any elevated HR read. The context…, Extends LIGHT responsibility resolution with optional HR derivation. Resolution…

### Community 114 - "Community 114"
Cohesion: 0.33
Nodes (4): M13b extension point, mirroring M13's `stock.picking. button_validate()`…, M13b technical helper — mirrors `light_ir_stock`'s…, StockMove, StockPicking

### Community 115 - "Community 115"
Cohesion: 0.40
Nodes (3): LightInternalRequest, Locks, in order: (1) the IR header row — serializes concurrent writers of…, M20: IR Completion / Controlled Reactivation (FR-IR-136..142, FR-IR-145;…

### Community 116 - "Community 116"
Cohesion: 0.40
Nodes (3): LightInternalRequestLine, M15: Fulfillment Exception reporting — `action_report_exception()` on…, FR-IR-102/103/104. Creates ONE Fulfillment Exception record in `open` status.…

### Community 117 - "Community 117"
Cohesion: 0.33
Nodes (3): LightInternalRequestLine, depends, M19 UX: a non-stored, search-only field for the Pending Acceptance worklist.…

### Community 120 - "Community 120"
Cohesion: 0.33
Nodes (3): constrains, IR-side invariant (TR-STK-004): sum(EFFECTIVE attributed_qty for the IR Line)…, Move-side invariant (locked M12 direction): sum(EFFECTIVE attributed_qty across…

### Community 121 - "Community 121"
Cohesion: 0.33
Nodes (4): LightIrStockAttribution, model, PRIVATE ENGINE API — trusted, system-triggered post-…, M13 extension of the locked M12 model (`stock_attribution.py` stays byte-…

### Community 122 - "Community 122"
Cohesion: 0.40
Nodes (3): Whether `self` (the SOURCE move about to be split) currently carries EFFECTIVE…, Narrow technical `_inherit` on standard Odoo `stock.move` (NOT a new LIGHT…, StockMove

### Community 123 - "Community 123"
Cohesion: 0.40
Nodes (3): LightIrApprovalCycle, PRIVATE ENGINE-EXTENSION API — self-guarded, not a general-purpose setter:…, Adds the source-transaction reference the M03 engine deliberately left out (see…

### Community 124 - "Community 124"
Cohesion: 0.40
Nodes (3): LightInternalRequestLine, depends, M13b: Delivery Authority Unification (TR-QTY-005, TR-ACC-012, AGENTS.md…

### Community 128 - "Community 128"
Cohesion: 0.67
Nodes (3): Cleanup Test Database PostgreSQL (Odoo Dev), pg_terminate_backend before DROP DATABASE, odoo18-dev-db (postgres:16 container)

## Ambiguous Edges - Review These
- `LIGHT IR Requirement Traceability Matrix` → `PRE-UAT Functional Walkthrough (light-ir-m20)`  [AMBIGUOUS]
  docs/05_uat/PRE_UAT_FUNCTIONAL_WALKTHROUGH.md · relation: references
- `LIGHT IR Requirement Traceability Matrix` → `Meeting Plan BRD — Approval Matrix Business Rules Draft`  [AMBIGUOUS]
  issues-backlogs/meeting-plan-brd.md · relation: conceptually_related_to
- `BUG` → `CHANGE REQUEST`  [AMBIGUOUS]
  docs/05_uat/UAT_DEFECT_LOG_TEMPLATE.md · relation: conceptually_related_to

## Knowledge Gaps
- **278 isolated node(s):** `1. Internal Request (IR)`, `2. IR Line`, `3. Requester`, `4. Receiver`, `5. Request Type` (+273 more)
  These have ≤1 connection - possible missing edges or undocumented components.
- **26 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **What is the exact relationship between `LIGHT IR Requirement Traceability Matrix` and `PRE-UAT Functional Walkthrough (light-ir-m20)`?**
  _Edge tagged AMBIGUOUS (relation: references) - confidence is low._
- **What is the exact relationship between `LIGHT IR Requirement Traceability Matrix` and `Meeting Plan BRD — Approval Matrix Business Rules Draft`?**
  _Edge tagged AMBIGUOUS (relation: conceptually_related_to) - confidence is low._
- **What is the exact relationship between `BUG` and `CHANGE REQUEST`?**
  _Edge tagged AMBIGUOUS (relation: conceptually_related_to) - confidence is low._
- **Why does `LightInternalRequestLine` connect `Community 23` to `Stock Attribution Tests`, `Community 53`?**
  _High betweenness centrality (0.021) - this node is a cross-community bridge._
- **Why does `TestLightIrStockAttribution` connect `Stock Attribution Tests` to `Community 72`, `Community 23`?**
  _High betweenness centrality (0.021) - this node is a cross-community bridge._
- **Why does `TestPurchaseOrderCommitmentGuard` connect `PO Commitment Guard Tests` to `Community 56`, `Purchase M20 Golden Scenarios`?**
  _High betweenness centrality (0.015) - this node is a cross-community bridge._
- **Are the 2 inferred relationships involving `ADR-025` (e.g. with `Deterministic Policy Resolution` and `BR-APR-005 — Jika posisi Head vacant, request diteruskan ke C-Level terkait`) actually correct?**
  _`ADR-025` has 2 INFERRED edges - model-reasoned connections that need verification._