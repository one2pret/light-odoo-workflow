# LIGHT Internal Request V1 — UAT Master Data Checklist

Baseline: `light-ir-m20`. Isi checklist ini **sebelum** eksekusi
`UAT_TEST_CASES.md` dimulai. Semua nama contoh boleh diganti asal
konsisten dipakai di seluruh kasus uji.

## 0. Urutan Instalasi Addon

- [ ] `light_ir_approval`
- [ ] `light_internal_request`
- [ ] `light_ir_hr`
- [ ] `light_ir_purchase`
- [ ] `light_ir_stock`

Instalasi harus selesai 0 error sebelum lanjut ke bawah.

## 1. Company

- [ ] **Company A** (contoh: "UAT Company A") — digunakan untuk seluruh
      Golden Scenario dan skenario positif.
- [ ] **Company B** (contoh: "UAT Company B") — **wajib**, khusus untuk
      UAT-SEC-02 (cross-company violation blocked).

## 2. Request Type (`light.ir.request.type`)

- [ ] Minimal satu Request Type per Company (contoh: "Barang Umum" /
      "General Goods"). Boleh global (`company_id` kosong) atau spesifik
      per Company — pastikan konsisten dengan resolusi Policy di bawah
      (Tier-1 vs Tier-2).
- [ ] Ingat: Request Type **tidak** menyimpan perilaku bisnis apa pun
      (tidak ada logic yang bercabang berdasarkan nama/kode Request Type).

## 3. Responsibility Assignment (`light.ir.responsibility.assignment`)

Untuk setiap Company yang dipakai:

- [ ] Satu baris aktif untuk setiap `responsibility_code` yang dipakai di
      NEED Rule Step / FINANCIAL Rule Step (lihat §4-5) — user yang
      ditunjuk harus login sebagai actor NEED/FINANCIAL Approver.
- [ ] `IR_CANCELLATION_AUTHORITY` — **wajib**, satu baris aktif per
      Company, user = Cancellation Authority actor.
- [ ] `IR_REVISION_AUTHORITY` — **wajib**, satu baris aktif per Company,
      user = Revision Authority actor.
- [ ] Untuk **UAT-SEC-05** (Invalid/missing Responsibility Assignment):
      siapkan satu Company/kode TANPA baris aktif sama sekali, jangan
      diisi sebelum kasus itu dieksekusi.
- [ ] Untuk **UAT-SEC-06** (Ambiguous Responsibility Assignment):
      siapkan **dua** baris aktif untuk Company/kode yang SAMA, hanya
      untuk saat menjalankan kasus itu (lalu nonaktifkan salah satu
      sebelum melanjutkan kasus lain).

## 4. NEED Approval Policy (`light.ir.approval.policy`, `purpose='need'`)

- [ ] Satu Policy aktif per Company (Tier-1: `request_type_id` diisi
      persis, atau Tier-2: `request_type_id` kosong/global) — **hanya
      satu** yang boleh cocok untuk kombinasi Company+Request
      Type+tanggal, atau Submit akan diblokir.
- [ ] Satu `light.ir.approval.rule` di bawah Policy tersebut.
- [ ] Satu atau lebih `light.ir.approval.rule.step`:
      - `responsibility_code` (contoh: `UAT_DEPT_HEAD`)
      - `is_mandatory = True`
      - `sequence`
- [ ] Untuk **UAT-SEC-08** (Self-approval behavior; dirujuk juga sebagai
      UAT-GS-13): siapkan Responsibility Assignment yang resolusinya =
      Requester sendiri untuk kode step ini. Sediakan juga kemungkinan
      mengubah `self_approval_skip` pada Rule menjadi `False` untuk
      bagian kedua kasus tersebut.

## 5. FINANCIAL Approval Policy (`light.ir.approval.policy`, `purpose='financial'`)

- [ ] Satu Policy aktif per Company, `threshold_basis` **wajib diisi**
      (`untaxed` atau `tax_included`).
- [ ] Minimal **dua tier** `light.ir.approval.rule` (contoh: Low
      `threshold_min=0, threshold_max=10000`; High
      `threshold_min=10000, threshold_max` kosong) — dibutuhkan untuk
      UAT-GS-04 dan UAT-GS-14.
- [ ] Satu `light.ir.approval.rule.step` per tier, `responsibility_code`
      berbeda per tier (contoh: `UAT_DEPT_HEAD` untuk Low,
      `UAT_CFO` untuk High).

## 6. Governance Policy (`light.ir.policy`)

- [ ] Satu Policy aktif per Company (Tier-1 atau Tier-2, sama seperti NEED
      Policy) — **wajib ada**, atau seluruh aksi downstream
      (Accept/Cancel/Exception/Attribution/Revision) akan diblokir oleh
      `_assert_fulfillment_plan_ready_for_execution()`.
- [ ] `review_required` — set sesuai kebutuhan skenario (boleh `False`
      untuk menyederhanakan sebagian besar Golden Scenario).
- [ ] `plan_approval_required` — sama.

## 7. HR Responsibility Rule (`light.ir.hr.responsibility.rule`) — OPSIONAL

Hanya isi jika UAT ingin menguji resolusi berbasis struktur organisasi
(Department Manager) sebagai alternatif Responsibility Assignment
langsung:

- [ ] `hr_source = department_manager`
- [ ] `hr.employee`/`hr.department` dengan manager sudah diatur di modul
      HR standar Odoo.

## 8. Delegation (`light.ir.approval.delegation`) — untuk UAT-GS-11 dan UAT-SEC-07

- [ ] Satu baris: `delegator_id` = NEED/FINANCIAL Approver yang sedang
      berhalangan, `delegate_id` = Delegate, `date_from`/`date_to`
      mencakup tanggal eksekusi UAT-GS-11.
- [ ] Untuk **UAT-SEC-07** (Invalid Delegation date): siapkan baris
      kedua dengan `date_to` di masa lalu (sudah kedaluwarsa) — jangan
      diaktifkan sebelum kasus itu dieksekusi.

## 9. Standard Odoo Purchase Data

- [ ] Minimal satu `res.partner` bertipe vendor per Company (untuk
      UAT-GS-01, 04, 05, 06, 14).
- [ ] Untuk UAT-GS-06 (multiple vendors): minimal **dua** vendor.

## 10. Standard Odoo Stock Data

- [ ] Warehouse per Company dengan picking type kode `internal` aktif
      (untuk UAT-GS-02, 03).
- [ ] Product yang dipakai di skenario Purchase/Stock harus storable
      (`is_storable=True` / `type='consu'` sesuai build Odoo 18 yang
      dipakai) agar stock move nyata bisa dieksekusi.

## 11. Product / UoM

- [ ] Minimal satu Product per skenario dengan UoM standar (`Units`) —
      boleh reuse satu Product untuk semua Golden Scenario asal tidak
      saling mengganggu quantity antar skenario paralel.

## 12. Users (lihat detail role di `UAT_ROLE_MATRIX.md`)

Minimal satu user nyata per actor berikut, masing-masing dengan Company
yang benar di `company_ids`:

- [ ] Requester (Company A)
- [ ] Requester lain (Company B) — untuk cross-company negative test
- [ ] NEED Approver (resolusi via Responsibility Assignment)
- [ ] FINANCIAL Approver Low tier
- [ ] FINANCIAL Approver High tier (CFO-equivalent)
- [ ] Delegate
- [ ] Reviewer
- [ ] Plan Approver
- [ ] Receiver
- [ ] Cancellation Authority
- [ ] Revision Authority
- [ ] Procurement
- [ ] Inventory
- [ ] Administrator
- [ ] Auditor
- [ ] Plain user (tanpa capability apa pun) — untuk UAT-SEC-01
