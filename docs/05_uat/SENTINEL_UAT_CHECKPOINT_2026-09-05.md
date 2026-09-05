# LIGHT Internal Request — Sentinel Development & UAT Checkpoint

**Tanggal checkpoint:** 2026-09-05  
**Status:** Living document / working baseline  
**Dokumen bisnis acuan terbaru:** BRD Permintaan Internal Sentinel v2.2 (revisi bisnis terakhir yang diterima pada awal September 2026)  
**Code branch aktif:** `sentinel-s1-request-type-entry`  
**Frozen reference baseline:** M22 (`ace6608f61b30bea9e5aad2e744f1e5f3e92e592`)

---

## 1. Tujuan Dokumen

Dokumen ini adalah checkpoint development dan UAT, bukan pengganti BRD. Tujuannya:

- mengunci pemahaman flow yang sudah diuji;
- membedakan requirement lama vs requirement terbaru;
- mencatat fungsi yang sudah PASS;
- mencatat bug/regression yang sudah ditemukan dan diperbaiki;
- mencatat gap terhadap BRD yang masih membutuhkan keputusan bisnis;
- menjadi titik mulai untuk UAT berikutnya tanpa mengulang analisis dari awal.

Dokumen ini harus diperbarui setiap kali satu blok flow utama selesai atau ada perubahan requirement material dari client.

---

## 2. Baseline Flow Bisnis Terbaru

### 2.1 Requester dan NEED Approval

```text
Requester
  → Create Internal Request
  → Pilih Request Type
  → Isi kebutuhan, qty, harga estimasi, required date, business purpose
  → Submit
  → Approver RQ / NEED Approval
      → Head of Division
      → C-Level terkait
      → CFO
      → CEO
    sesuai matrix Department + Amount
```

Jika NEED Approval ditolak, transaksi berhenti sesuai business rule terbaru.

Jika NEED Approval disetujui, sistem melakukan post-approval routing berdasarkan Request Governance Policy.

---

## 3. Post-NEED Routing Terbaru

### 3.1 Direct Purchase

Berdasarkan diagram bisnis terbaru:

```text
Permintaan Stock
  → NEED Approved
  → Direct Purchase
  → tidak melalui Reviewer
  → tidak melalui Approver Review
  → Approved Qty otomatis dialokasikan ke Purchase
  → Procurement Worklist
  → Draft RFQ

Permintaan Lainnya
  → NEED Approved
  → Direct Purchase
  → tidak melalui Reviewer
  → tidak melalui Approver Review
  → Approved Qty otomatis dialokasikan ke Purchase
  → Procurement Worklist
  → Draft RFQ
```

**Status UAT:** PASS untuk routing sampai Procurement Worklist / Ready for RFQ.

Catatan penting: diagram bisnis menyebut Draft RFQ otomatis. Implementasi saat ini masih meminta user Procurement memilih Vendor sebelum RFQ standard Odoo dibuat. Sumber Vendor otomatis belum ditentukan di BRD, sehingga sistem tidak membuat vendor dummy atau memilih vendor secara arbitrer.

### 3.2 To Review

Flow terbaru yang masih melewati Reviewer:

```text
Asset
  → Reviewer: Asset Management

Supplies
  → Reviewer: General Services

Inventory Request
  → Reviewer: Accounting
```

Reviewer menentukan fulfillment:

```text
Purchase (PR)
atau
Internal Transfer (TR)
```

Kemudian:

```text
Reviewer
  → Submit Hasil Review
  → Approver Review
      → Approved PR → Purchase / RFQ
      → Approved TR → Draft Internal Transfer
      → Rejected → kembali ke Reviewer / Review Hasil
```

**Status:** lifecycle Review + Approver Review sudah diuji. Internal Transfer execution belum end-to-end.

---

## 4. Flow Yang Sudah PASS

### 4.1 Request & NEED Approval

- Create berdasarkan Request Type — PASS
- Submit Internal Request — PASS
- NEED Approval berdasarkan Department + Amount — PASS
- Approved Qty final hanya terbentuk setelah NEED final approved — PASS
- Self-skip approval yang sudah ada di engine — tetap dipertahankan

### 4.2 Post-Approval Routing

- Request Governance Policy resolve berdasarkan Company + Request Type — PASS
- Routing snapshot disimpan saat NEED selesai — PASS
- Responsibility Code → Responsibility Assignment → Assigned Reviewer — PASS
- Reviewer capability guard — PASS
- Historical request tidak berubah ketika master policy diubah — PASS by design

### 4.3 Reviewer & Approver Review

- Assigned Reviewer melihat request di Review Worklist — PASS
- Reviewer lain tidak menjadi owner task — PASS
- Fulfillment Allocation Purchase/Internal — PASS
- Remaining Qty validation — PASS
- Save & Mark Reviewed — PASS
- Approver Review Worklist — PASS
- Approve Review — PASS
- Reject Review — PASS
- Rejected kembali ke Reviewer — PASS
- Review Hasil / rework — PASS
- Resubmit ke Approver Review — PASS

### 4.4 Procurement & RFQ

- Procurement Worklist — PASS
- Product-required guard — PASS
- Create RFQ wizard — PASS
- Vendor selection — PASS
- Standard Odoo Draft RFQ terbentuk — PASS
- Initial Unit Price boleh berbeda dari Requester Estimated Price — PASS
- Multi Vendor RFQ Candidate — PASS
- Procurement status `RFQ Created / In Sourcing` — PASS
- `RFQ Candidates` count — PASS

### 4.5 Sourcing, Award, Attribution

- Purchase Sourcing list/form — PASS
- Vendor context dan RFQ price terlihat — PASS
- Award Vendor — PASS
- Mark Lost action tersedia — PASS
- Purchase Attribution terbentuk setelah Award — PASS
- Award Qty mengurangi remaining authority secara benar — PASS
- Traceability IR → Sourcing → RFQ → Attribution — PASS

---

## 5. Re-baseline Requirement Penting

### 5.1 Permintaan Stock

**Requirement/assumption lama:**

```text
Permintaan Stock
  → PPIC Review
  → Approver Review
  → Purchase / Internal Transfer
```

**Flow client terbaru:**

```text
Permintaan Stock
  → NEED Approval
  → Direct Purchase
  → Draft RFQ
```

Status: **REBASELINED / IMPLEMENTED / UAT PASS** sampai Procurement Worklist.

Policy baru yang dipakai:

```text
Post Approval Route       = Direct to Purchase
Review Wajib              = No
Persetujuan Rencana Wajib = No
Reviewer Responsibility   = kosong
```

Existing historical request yang sudah tersnapshot `Stock - PPIC Review` tetap mempertahankan snapshot lama dan tidak dimigrasikan diam-diam.

### 5.2 Permintaan Lainnya

Flow terbaru:

```text
Permintaan Lainnya
  → NEED Approval
  → Direct Purchase
  → Procurement
  → RFQ
```

Status: **MATCH / IMPLEMENTED / UAT PASS** sampai Procurement Worklist.

---

## 6. Bug & Regression Yang Sudah Ditemukan

Daftar ini wajib menjadi regression checklist pada upgrade/refactor berikutnya.

| Area | Masalah | Status |
|---|---|---|
| Approval Cycles | user melihat banyak cycle yang bukan milik record yang dibuka | Fixed |
| Action Window | ordinary user terkena `ir.actions.act_window` access error | Fixed |
| Product Search More | access leak ke `mrp.bom` | Fixed |
| Fulfillment Wizard | ACL wizard tidak tersedia | Fixed |
| Fulfillment Wizard | `line_id` mandatory tidak terisi | Fixed |
| Fulfillment Wizard | Planned/Remaining tidak recompute saat qty berubah | Fixed |
| XML View | inheritance menggunakan `string` selector | Fixed |
| Create RFQ Wizard | Vendor mandatory terlalu dini sehingga wizard gagal dibuka | Fixed |
| Procurement Status | tetap `Ready for RFQ` setelah RFQ candidate dibuat | Fixed |
| Sourcing UX | tidak ada Award/Mark Lost action di UI | Fixed |
| Sourcing UX | line teknis dan harga vendor tidak informatif | Improved |
| Related Field | `rfq_unit_price` type mismatch terhadap `purchase.order.line.price_unit` | Fixed |
| Award Wizard | `rfq_currency_id` dipanggil view tetapi belum ada di model | Fixed |

---

## 7. UI/UX Improvement Yang Sudah Dilakukan

### Procurement Worklist

Lifecycle status dibuat lebih informatif:

```text
Ready for RFQ
→ RFQ Created / In Sourcing
→ Partially Awarded
→ Fully Awarded
```

Jika RFQ candidate sudah ada, action dapat berubah menjadi `Create Another RFQ` karena satu kebutuhan dapat memiliki beberapa vendor candidate.

### Purchase Sourcing

Informasi bisnis yang sekarang ditampilkan:

- Internal Request
- Product
- Description / Specification
- UoM
- Estimated Price Requester
- RFQ
- Vendor
- RFQ Unit Price
- RFQ Subtotal
- Sourcing Qty
- Award Qty
- Status
- Audit actor/timestamp

### Award / Purchase Attribution

UI dibuat lebih business-readable dan mengurangi identifier teknis seperti `light.internal.request.line,387` dari fokus utama user.

---

## 8. BRD Alignment & Gap Saat Ini

| Area | Status terhadap BRD terbaru | Catatan |
|---|---|---|
| Requester create/submit | MATCH | sudah ada |
| Approver RQ / NEED berjenjang | MATCH | sudah ada |
| Stock Direct Purchase | MATCH | sudah re-baseline |
| Permintaan Lainnya Direct Purchase | MATCH | sudah re-baseline |
| Asset → Asset Management Review | MATCH / perlu UAT terbaru | policy perlu divalidasi |
| Supplies → General Services Review | MATCH / perlu UAT | belum end-to-end terbaru |
| Inventory Request → Accounting Review | PARTIAL | perlu rekonsiliasi penuh dengan INV-007 |
| Reviewer decision Purchase/Internal | MATCH | sudah ada |
| Approver Review approve/reject/rework | MATCH | sudah PASS |
| Approved Purchase → Draft RFQ | PARTIAL | routing siap; vendor source untuk full-auto RFQ belum didefinisikan |
| Approved Internal → Draft Internal Transfer | GAP / NEXT | belum end-to-end |
| Additional Financial/PO Commitment Approval | NOT EXPLICIT IN LATEST BRD | existing LIGHT M11; business decision required |
| PUR-034 spreadsheet import | GAP / future scope | belum dikerjakan |
| INV-007 Accounting/Warehouse/Expense Journal flow | GAP / future workstream | belum dikerjakan |

---

## 9. Financial Approval / PO Commitment — Current Decision

LIGHT M11 existing memiliki governance tambahan:

```text
LIGHT-linked PO
  → PO Commitment Authority / FINANCIAL cycle
  → baru boleh Confirm PO
```

Namun BRD terbaru tidak secara eksplisit menyebut approval finansial tambahan setelah RFQ/vendor selection. BRD terbaru lebih langsung menunjukkan Purchasing memverifikasi RFQ untuk menjadi PO.

Karena itu:

**Status:** BUSINESS DECISION REQUIRED.

Jangan menganggap Global FINANCIAL Policy sebagai mandatory Sentinel requirement sebelum client/PM mengonfirmasi bahwa setelah vendor/RFQ dipilih masih diperlukan approval finansial tambahan sebelum PO.

Existing M11 tetap dipertahankan sebagai capability LIGHT dan security baseline sampai ada keputusan desain final.

---

## 10. Current Development Status

```text
M22 Frozen Baseline
✅ preserved as reference

Sentinel Request Entry
✅ Request Type entry UX

Sentinel Routing
✅ To Review
✅ Responsibility Assignment
✅ Direct to Purchase
✅ Stock re-baseline
✅ Permintaan Lainnya direct purchase

Reviewer Lifecycle
✅ Review
✅ Fulfillment Allocation
✅ Approver Review
✅ Reject → Review Hasil → Resubmit

Purchase Lifecycle
✅ Procurement Worklist
✅ Draft RFQ creation
✅ Multi Vendor RFQ
✅ Sourcing
✅ Award Vendor
✅ Purchase Attribution

Next Main Execution Branch
⏭ Internal Transfer
```

---

## 11. Next Recommended Work

Prioritas berikutnya adalah menyelesaikan execution branch yang belum end-to-end:

```text
Asset / Supplies / Inventory Request
  → Reviewer
  → Approver Review
  → Approved Internal Transfer
  → Draft Internal Transfer Odoo
```

Target utama:

1. validasi policy dan responsibility untuk Asset, Supplies, Inventory Request;
2. UAT Review → Approver Review dengan pilihan Internal Transfer;
3. buat Draft standard Odoo `stock.picking` / `stock.move` tanpa mengkonfirmasi transfer otomatis;
4. pertahankan traceability IR Line ↔ Stock execution;
5. pastikan user Warehouse, bukan Requester/Reviewer, yang memiliki execution authority;
6. setelah Internal Transfer PASS, lakukan checkpoint dokumentasi berikutnya.

Setelah dua execution branches lengkap (`Purchase` dan `Internal Transfer`), baru lanjut ke:

- keputusan final PO Commitment / Financial Approval;
- INV-007;
- PUR-034;
- Not Process;
- formal End-to-End UAT Guide v1.0.

---

## 12. Regression UAT Minimum Sebelum Merge

Sebelum branch Sentinel dianggap siap untuk review/merge, ulangi minimum skenario berikut:

1. **Stock Direct Purchase**
   - Submit → NEED Approved → Direct Purchase → Procurement Worklist.
   - Tidak muncul di Review Worklist.

2. **Permintaan Lainnya Direct Purchase**
   - Submit → NEED Approved → Procurement Worklist.

3. **Review Route**
   - Assigned Reviewer only.
   - Fulfillment qty recompute benar.

4. **Approver Review Reject/Rework**
   - Reject → kembali Reviewer → Review Hasil → submit ulang.

5. **Purchase RFQ**
   - Create RFQ → RFQ candidate count bertambah.

6. **Multi Vendor Sourcing**
   - dua candidate → satu Award → Purchase Attribution terbentuk.

7. **Security**
   - Requester tidak memiliki Reviewer/Procurement authority hanya karena bisa melihat record.
   - Technical Administrator tidak otomatis dianggap business approver.

---

## 13. Dokumentasi Berikutnya

Checkpoint berikutnya dibuat setelah Internal Transfer execution selesai.

Setelah Purchase + Internal Transfer lengkap, checkpoint ini menjadi sumber untuk dokumen formal:

**LIGHT Internal Request — End-to-End UAT & Functional Flow v1.0**

Dokumen formal tersebut nantinya memuat:

- glossary;
- actor/role;
- master/config preparation;
- business flow;
- step-by-step UAT;
- expected result;
- BRD mapping;
- screenshots/evidence;
- known gaps;
- regression test list;
- sign-off checklist.
