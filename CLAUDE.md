# LIGHT Odoo — Claude Code Instructions

## Project

LIGHT Internal Request

Target:
- Odoo 18
- Community-compatible core architecture
- May be deployed on Odoo Enterprise 18

Before changing code, read and follow `AGENTS.md`.

---

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

Do not skip this reading order merely because the requested code change appears small.

---

## Odoo Edition Compatibility

The LIGHT core addons must remain compatible with Odoo 18 Community architecture.

The solution may run on Odoo Enterprise 18, but Enterprise availability must not
be used as justification for introducing Enterprise-only dependencies into the
approved LIGHT core addons.

Do not introduce Enterprise-only modules, APIs, models, mixins, or behavior into
core addons unless explicitly approved.

When inspecting Odoo documentation or source, distinguish between:

- Odoo 18 Community behavior
- Odoo 18 Enterprise-specific behavior

Prefer Community-compatible standard APIs whenever they satisfy the requirement.

---

## Odoo 18 Documentation and Source Verification

Before implementing behavior that depends on Odoo framework semantics, inspect
the available Odoo 18 documentation or MCP documentation first.

This applies especially to:

- ORM lifecycle
- fields
- computed fields
- related fields
- onchange
- create/write/unlink
- constraints
- views
- actions
- menus
- ACL
- record rules
- multi-company behavior
- currency
- UoM
- Product
- HR
- Purchase
- Stock
- module installation
- module upgrade
- mail/activity behavior

If MCP/documentation does not provide enough detail, inspect the installed Odoo
18 source.

Do not assume behavior from another Odoo version if Odoo 18 behavior can be
verified directly.

Do not claim documentation or source was checked unless it actually was.

When implementation relies on a specific standard Odoo mechanism, report that
mechanism in the completion report.

---

## Scope Discipline

Implement only the active milestone.

Do not implement future milestones early.

Do not add features merely because they appear useful.

Do not change module boundaries without an approved ADR.

Do not introduce Enterprise-only dependencies into core addons.

Do not redesign the approval, quantity, Purchase, Stock, security, or audit
architecture without explicit approval.

Do not opportunistically refactor unrelated code while implementing a milestone
or bug fix.

If an implementation unexpectedly requires changes outside the expected scope,
stop and explain why before continuing.

---

## Before Coding

For the active milestone, identify:

- requirement IDs
- technical requirement IDs
- ADRs involved
- DEC IDs involved where applicable
- models affected
- standard Odoo models extended
- views affected
- security affected
- tests required
- expected file-change surface

If any of these are unclear, stop and report the ambiguity before implementing.

Inspect the current source before making assumptions.

Do not implement from the specification alone without verifying how the current
code actually works.

---

## Implementation Style

Prefer the smallest compliant implementation.

Reuse existing Odoo models and APIs.

Do not clone standard Odoo functionality.

Keep business logic in Python model/service methods, not only in views or
JavaScript.

Critical validation must execute server-side.

Use explicit relations rather than free-text references.

Preserve structured historical audit records.

Prefer additive `_inherit` extensions over duplicated models.

Do not introduce a parallel business engine where the existing engine can be
extended safely.

---

## Odoo Code Readability

Production code must be idiomatic Odoo 18 and understandable by another
experienced Odoo developer.

Prefer:

- standard ORM operations
- small model methods
- one clear responsibility per method
- descriptive business-oriented method names
- straightforward recordset logic
- existing LIGHT implementation patterns
- explicit relations
- standard Odoo APIs

Avoid:

- clever abstractions
- unnecessary helper/service layers
- deeply nested branching
- generic utility frameworks for ordinary model behavior
- unexplained context keys
- duplicated resolution logic
- hidden side effects
- oversized methods
- non-standard conventions without justification

Private method names must describe their business purpose.

Prefer:

`_resolve_requester_department()`

over:

`_process_data()`

Prefer:

`_department_governance_relevant()`

over:

`_check_context()`

Comments should explain WHY a non-obvious decision exists.

Do not write comments that merely repeat what the Python statement already says.

---

## Standard Odoo Architecture First

Use standard Odoo mechanisms when they already solve the problem.

Prefer:

- `_inherit`
- ORM searches
- recordsets
- `@api.depends`
- `@api.onchange`
- `@api.constrains`
- `create()`
- `write()`
- standard ACLs
- record rules
- standard Odoo UoM conversion
- standard Odoo currency conversion
- XML IDs
- configuration records

Avoid:

- raw SQL when ORM is sufficient
- custom conversion formulas when Odoo has standard APIs
- client-side-only enforcement of business invariants
- duplicate fields representing the same concept
- duplicate approval engines
- hardcoded record IDs
- hardcoded business users

Raw SQL is acceptable only where standard ORM cannot safely satisfy a proven
requirement, such as concurrency locking or a database-level invariant, and the
reason must be documented.

---

## Onchange and Persistence

`@api.onchange` is a UI convenience mechanism.

Do not rely on onchange alone for authoritative business persistence.

If a value is required to survive:

New → Save → Reload → Submit

then server-side model behavior must guarantee that persistence.

For governance-critical values such as:

- routing dimensions
- approval evidence
- transaction snapshots
- historical values

the design should generally follow:

- onchange may provide immediate UI feedback
- server-side create/write/business methods must guarantee persistence
- post-submit immutability must be enforced server-side

A value appearing correctly in the browser before Save is not proof that it has
been persisted.

Where appropriate, tests should verify save/re-read behavior.

---

## Field Design

Before adding a field, answer:

1. Does standard Odoo already provide this value?
2. Does LIGHT already provide this value elsewhere?
3. Is the field a reference, compute, configuration value, or historical snapshot?
4. Must it survive later master-data changes?
5. Must it be searchable or reportable?
6. Does it belong on the header or line?
7. Is it authoritative or display-only?
8. Is a new stored field actually required?

Do not create duplicate fields without justification.

---

## Snapshot Rule

Historical approval evidence must not depend on mutable master data after
approval.

Where a business value forms part of approval routing or approval evidence,
determine whether it requires snapshot semantics.

Examples may include:

- Department at submission
- Approval Policy
- Approval Rule
- resolved approver
- submitted estimated amount
- transaction valuation

Master-data changes after Submit must not silently rewrite historical approval
meaning unless explicitly designed and approved.

---

## Purchase Integration

Do not replace `purchase.order` or `purchase.order.line`.

LIGHT-specific PO validation applies only to LIGHT-linked procurement.

A standard non-LIGHT PO must continue to behave normally.

Do not use `origin` as authoritative IR attribution.

Do not modify standard Purchase behavior outside explicit LIGHT-linked flows.

---

## Stock Integration

Do not replace `stock.picking`, `stock.move`, or `stock.move.line`.

LIGHT-specific stock controls apply only to LIGHT-linked stock execution.

Backorders and partial moves must preserve attribution.

Do not modify standard Stock behavior outside explicit LIGHT-linked flows.

---

## Approval

Do not use named-user conditions.

Do not hard-code:

- Head
- CFO
- CEO
- C-Level
- Department
- Company
- monetary thresholds
- personal user IDs

Resolve approval through configured Policy, Rule, Responsibility, and applicable
HR/configuration resolution.

Approval history must remain immutable through normal business operations.

Do not introduce a new Approval Purpose without explicit business and governance
approval.

Do not create a parallel approval engine if the existing LIGHT engine can be
extended.

---

## Security

Do not use UI visibility as the only authorization control.

Do not assume group membership gives transaction authority.

Do not use broad `sudo()` to make failing security checks disappear.

Server-side business actions must enforce applicable authorization independently
of view visibility.

When privileged actions apply, verify as appropriate:

- capability/group
- transaction-specific authority
- record ownership
- company boundary
- lifecycle state
- record rules

UI visibility is an additional UX layer only.

---

## sudo() Policy

Every new production `sudo()` must have a specific reason.

Never use `sudo()` to bypass business authorization.

Where privileged access is architecturally required:

- keep it narrow
- perform the required read/write only
- do not propagate sudoed recordsets unnecessarily
- return to the normal user security context where possible

Every new `sudo()` must be explained in the completion report.

---

## Multi-Company Safety

All LIGHT changes must preserve multi-company behavior.

Where applicable, resolve records using the transaction's explicit `company_id`,
not an accidental environment company.

Pay particular attention to:

- Employee
- Department
- Responsibility
- Approval Policy
- Product Cost
- Currency
- Purchase
- Stock

Do not hard-code Company IDs.

Company-dependent standard Odoo fields must be read in the appropriate company
context.

Cross-company ambiguity or unauthorized access must fail closed.

---

## Configuration Over Hardcoding

Approval and business routing must remain data-driven wherever the existing
architecture supports configuration.

Do not hardcode:

- Department names
- employee names
- C-Level names
- approver IDs
- threshold amounts
- Company IDs

Bad:

```python
if department.name == "Growth Marketing":
    approver = cmo_user
elif department.name == "People & Culture":
    approver = ceo_user
```

Also bad:

```python
if amount < 5_000_000:
    approvers = [head_user]
elif amount < 10_000_000:
    approvers = [head_user, c_level_user]
elif amount < 50_000_000:
    approvers = [head_user, c_level_user, cfo_user]
else:
    approvers = [head_user, c_level_user, cfo_user, ceo_user]
```

Preferred:

```text
Approval Policy
    ↓
Approval Rule
    ↓
Approval Rule Step
    ↓
Responsibility Code
    ↓
Responsibility Assignment / HR Resolver
    ↓
Resolved User
```

For example, business configuration may define:

```text
Department: Growth Marketing
Purpose: NEED

Rule [0, 5m)
    → GM_HEAD

Rule [5m, 10m)
    → GM_HEAD
    → GM_CLEVEL

Rule [10m, 50m)
    → GM_HEAD
    → GM_CLEVEL
    → CFO

Rule [50m, ∞)
    → GM_HEAD
    → GM_CLEVEL
    → CFO
    → CEO
```

The Python implementation should resolve these configured records rather than
contain Department-specific or amount-specific branching.

Adding a new Department, changing an approver, or changing an approval threshold
should normally be possible through configuration without modifying Python code.

Hardcoded business values are allowed only when they are genuine technical
constants rather than configurable business policy, and the reason must be
explicitly justified.



#####
Fail Closed for Governance

Governance-critical ambiguity must not silently select an arbitrary record.

Examples:

multiple applicable Policies
multiple applicable Rules
ambiguous Responsibility
unresolved mandatory Department
missing mandatory approver
incompatible multi-company configuration

Where Department-aware or other scoped governance is applicable, missing
routing context must not silently bypass the scoped governance and select an
unrelated global configuration.

Backward-compatible fallback is allowed only when explicitly defined by the
governance rules.

Testing

Add or update tests in the same milestone when new or changed business behavior
requires regression coverage.

Run the relevant test suite before declaring completion.

Do not mark a milestone complete if applicable tests are missing or failing.

Do not add tests merely to increase a test count.

Tests must prove meaningful contracts.

Prefer coverage for:

successful business flow
authorization failure
ambiguity/fail-closed behavior
exact boundaries
persistence after save/re-read
company isolation
historical snapshot stability
important regressions

Do not modify old tests merely to force them green.

If an old assertion conflicts with a newly locked requirement, report the
supersession explicitly.

Database and Test Environment

Default persistent development/UAT database:

main

The existence of a testing requirement does NOT authorize creating a database.

Do not automatically:

create a database
clone a database
drop a database
restore a database
rename a database
create disposable test databases

Database creation or deletion requires explicit user approval.

main may be used for:

read-only ORM inspection
configuration inspection
module-state inspection
XML-ID inspection
group-membership inspection
safe persistence verification
user-directed manual/UAT simulation

Do not run destructive automated test fixtures against main.

Automated Test Database Rule

Do not assume every task requires an automated test database.

If automated verification requires:

a pristine database
module fresh installation
upgrade-path verification
destructive fixtures
explicit transaction commits
concurrency
multiple database connections
database-breaking tests

do NOT run it against main.

Do not create a new database automatically.

STOP and report:

ISOLATED_TEST_DB_REQUIRED

The report must include:

why main is unsuitable
proposed database name
exact command
whether a database will be created
whether it will be dropped afterward
expected test scope

Wait for explicit approval before creating, cloning, or dropping any database.

Testing Depth

Use the smallest verification level appropriate to the change.

Level 1 — Source Inspection

Use for:

read-only analysis
code-path tracing
configuration analysis
Level 2 — Safe Inspection Against main

Use for:

ORM inspection
menu/action/XML-ID inspection
group membership
safe persisted-value checks
user-directed manual verification
Level 3 — Targeted Tests

Run only the tests directly related to the changed behavior.

Level 4 — Affected Addon Regression

Run the affected addon's tests when justified.

Level 5 — Full LIGHT Regression

Run all LIGHT tests for:

milestone finalization
release sign-off
large cross-cutting changes
explicit user request
Level 6 — Fresh Install / Upgrade / Concurrency

Requires:

an isolated database
explicit user approval

Do not automatically jump to Level 5 or Level 6 after every small change.

Manual UI Findings

A passing automated test suite does not invalidate a reproducible browser defect.

When the user reports behavior such as:

a value disappearing after Save
incorrect button visibility
missing menus
incorrect workflow transitions
unexpected readonly/editable behavior
incorrect computed display

investigate the actual Odoo web/model lifecycle.

Do not dismiss a manual finding merely because automated tests pass.

Classify the root cause before editing:

code defect
configuration issue
data issue
security/group issue
view/UX issue
expected behavior
Views and Buttons

Button visibility should reflect capability and lifecycle where practical.

Example:

Requester:

Submit

Reviewer:

Mark Reviewed

Plan Approver:

Approve Plan
Reject Plan

However, view visibility is UX only.

The corresponding Python method must still enforce server-side authorization.

Before changing button visibility, inspect the actual user's group membership.

A button visible because the user legitimately owns the capability is not
automatically a view defect.

Do not weaken Python authorization merely to simplify UI behavior.

Bug-Fix Workflow

For a reported defect:

reproduce the issue;
inspect the relevant source;
identify the root cause;
classify the defect;
determine the smallest safe fix;
implement only after the cause is established;
run targeted verification;
broaden regression only when justified.

Classify as one of:

code defect
configuration issue
data issue
security/group issue
view/UX issue
expected behavior
business-decision blocker
architecture blocker

Do not fix symptoms without determining the cause.

Required Completion Report

At the end of each milestone or implementation round, report:

Requirements Covered
FR IDs
TR IDs
ADR IDs
DEC IDs where relevant
Root Cause

For defects, state what actually caused the issue.

Files Changed

List changed files.

Odoo 18 Mechanisms Used

List relevant standard Odoo APIs/patterns used.

Tests

List:

tests added
tests executed
database used
results
Standard Odoo Impact

State which Odoo models/methods were extended.

Security Impact

State:

ACL changes
record-rule changes
group changes
sudo changes
authority changes
Database Impact

State:

new models
new fields
migration impact
historical data impact
Regression

Confirm what unrelated behavior was verified.

Do not claim unexecuted regression as passed.

Open Issues

List:

ambiguities
technical debt
deferred work
known limitations

Do not hide unresolved issues.

Git Status

Report:

modified files
untracked files
staged/unstaged status
repository used
Final Classification

Use one clear classification, for example:

PASS_TARGETED_VERIFICATION
PASS_READY_FOR_MANUAL_SIMULATION
PASS_READY_TO_FINALIZE
BLOCKED_CONFIGURATION
BLOCKED_BUSINESS_DECISION
BLOCKED_SECURITY
BLOCKED_ARCHITECTURE
ISOLATED_TEST_DB_REQUIRED
Conflict Handling

If documentation conflicts:

do not choose whichever document is easiest to implement;
stop implementation of the conflicting area;
cite the conflicting IDs and text;
request a decision or ADR/DEC update.

Do not silently reinterpret LOCKED governance.

Completion Rule

A task is complete only when:

implementation matches baseline requirements;
architecture rules are respected;
security validation exists where required;
applicable tests exist;
applicable tests pass;
no unresolved baseline conflict remains;
known limitations are disclosed;
no prohibited scope was silently implemented.
Source Code Location

This repository contains documentation, governance, and implementation
instructions.

Do not create production Odoo addons inside this repository.

The Odoo addon source root is:

../custom-addons/

Approved V1 addon directories:

../custom-addons/light_ir_approval
../custom-addons/light_internal_request
../custom-addons/light_ir_hr
../custom-addons/light_ir_purchase
../custom-addons/light_ir_stock

Before implementing any milestone, verify that the target path resolves to the
expected sibling custom-addons directory.

Do not create a nested light-odoo/addons/ implementation tree.

Git Commit Boundary

This project uses separate Git repositories for documentation and source code.

Documentation/governance Git repository:

light-odoo/

Odoo addon source Git repository:

../custom-addons/

For implementation milestones:

modify source code only under ../custom-addons/;
run source-code Git commands from ../custom-addons/;
do not commit implementation code in light-odoo;
do not modify baseline documentation unless explicitly instructed through
approved change control;
do not create Git commits automatically unless the user explicitly requests
the commit;
do not push automatically;
do not create tags automatically;
milestone source commits belong in ../custom-addons/.

Before reporting milestone completion, include the source-repository Git status.

Repository Boundary Discipline

Respect repository ownership.

If source changes imply governance/documentation updates in another repository,
do not edit that repository unless the user explicitly authorizes it.

Report the required documentation change as pending.

Do not use cross-repository edits merely to make a completion report appear
complete.

Locked Milestone Protection

Completed milestone behavior is a regression baseline.

Do not silently redesign behavior from prior milestones.

If a new requirement requires a semantic change to a LOCKED milestone:

identify the affected milestone;
identify the affected FR/TR/ADR/DEC;
explain the impact;
require explicit approval or superseding governance decision.

Do not hide semantic changes inside a bug fix.

Primary Engineering Principle

Prefer:

standard Odoo

existing LIGHT architecture
configuration
small explicit extensions

over:

custom framework

duplicate business engine
hardcoded business rules
hidden automation

The code should be understandable by an experienced Odoo 18 developer without
needing the original author to explain it.


### Komentar dan Docstring

Jaga komentar dan docstring pada source code tetap ringkas dan mudah dibaca.

Jangan gunakan docstring di source code sebagai:

- dokumen arsitektur;
- implementation report;
- decision register;
- catatan sejarah milestone;
- penjelasan panjang mengenai perubahan versi.

Hindari docstring panjang yang menjelaskan:

- sejarah milestone seperti M20/M21;
- riwayat DEC/ADR/TR;
- riwayat testing;
- kronologi implementasi;
- backward compatibility secara panjang;
- hal yang sebenarnya sudah jelas dari nama method dan kode.

Penjelasan tersebut harus ditempatkan pada dokumen governance atau arsitektur
yang sesuai, bukan di dalam source code.

Untuk method bisnis biasa, gunakan docstring satu baris jika memang diperlukan.

Contoh yang baik:

```python
def action_submit(self):
    """Submit Internal Request dengan routing NEED berbasis Department."""
```

Hindari:

```python
def action_submit(self):
    """Penjelasan panjang mengenai perilaku M20, perubahan M21,
    context injection, sejarah Policy, backward compatibility,
    alasan testing, keputusan governance, dan detail implementasi...
    """
```

Gunakan inline comment hanya untuk menjelaskan alasan (WHY) yang tidak langsung
terlihat dari kode.

Contoh yang baik:

```python
# Proses per request karena setiap request dapat memiliki Department berbeda.
for request in self:
    ...
```

Hindari komentar yang hanya menerjemahkan kode menjadi kalimat.

Utamakan nama method dan variable yang jelas daripada menambahkan banyak komentar.

Jika sebuah komentar membutuhkan beberapa paragraf, pertimbangkan apakah
penjelasan tersebut seharusnya berada di DEC, ADR, TR, atau dokumentasi proyek.

Sebagai pedoman:

- docstring method biasa: maksimal 1 baris;
- docstring method bisnis yang kompleks: maksimal 2-4 baris pendek;
- inline comment: umumnya 1-2 baris;
- dokumentasi yang lebih panjang harus memiliki alasan yang jelas.

Jangan mencantumkan label milestone seperti M20/M21 pada docstring production
biasa kecuali perilaku runtime memang benar-benar bergantung pada perbedaan
tersebut.