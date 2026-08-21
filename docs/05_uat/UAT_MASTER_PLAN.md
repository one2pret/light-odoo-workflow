# LIGHT Internal Request V1 — UAT Master Plan

**Baseline (Code):** `light-ir-m20` (tag), `custom-addons@62ee406`
**Baseline (Docs):** `light-odoo@2ffcb74`
**Status:** V1 implementation CLOSED. This document validates business
acceptance of the released baseline — it is **not** a specification for new
work, and no code change is expected as an output of UAT unless a genuine
BUG is found (see `UAT_DEFECT_LOG_TEMPLATE.md` for classification rules).

---

## 1. Purpose

Konfirmasi bahwa LIGHT Internal Request V1, sebagaimana sudah dirilis pada
`light-ir-m20`, benar-benar memenuhi kebutuhan bisnis sebelum digunakan di
lingkungan produksi. UAT ini adalah **validasi penerimaan bisnis**
(business acceptance), bukan tahap pengembangan baru. Tidak ada perubahan
kode yang diharapkan sebagai hasil dari UAT ini, kecuali ditemukan BUG
sejati (hasil sistem bertentangan dengan baseline V1 yang sudah dikunci).

## 2. Scope

**In scope:**
- Seluruh 14 Golden Scenario M20 (lihat `UAT_TEST_CASES.md` Bagian A).
- Validasi NEED Approval, FINANCIAL Approval Tier-1, FINANCIAL PO
  Commitment Authority (Tier-2) (Bagian B).
- Validasi setiap kapabilitas/role: Reviewer, Plan Approver, Receiver,
  Cancellation Authority, Revision Authority, Procurement, Inventory,
  Administrator, Auditor (Bagian C).
- Validasi perilaku Completion M20: Done, Cancelled, In Process, blocking
  conditions, dan Controlled Reactivation (Bagian D).
- Validasi keamanan negatif: unauthorized action, cross-company,
  Auditor boundary, Administrator bukan approver otomatis, Responsibility
  Assignment invalid/ambigu, Delegation invalid, self-approval (Bagian E).

**Out of scope (sengaja tidak diuji, karena memang belum diimplementasikan
di V1 — lihat `UAT_MASTER_PLAN.md` §7):**
- Automatic Acceptance (FR-IR-100/101) — tetap deferred. **Tidak boleh
  dicatat sebagai kegagalan UAT** hanya karena fitur ini tidak ada.
- Pengujian beban/performa skala produksi (load testing) — direkomendasikan
  sebagai aktivitas terpisah sebelum go-live, bukan bagian dari UAT
  fungsional ini.
- Perubahan konfigurasi Purchase/Stock standar Odoo di luar yang memang
  dibutuhkan LIGHT.

## 3. Environment

- Dedicated UAT environment, terpisah dari environment development.
- Kode persis pada tag `light-ir-m20`, terinstal fresh (lihat
  `UAT_MASTER_DATA_CHECKLIST.md` untuk urutan instalasi addon).
- Minimal **2 Company** harus dikonfigurasi untuk memungkinkan pengujian
  cross-company isolation (Bagian E).
- Database di-backup penuh sebelum UAT dimulai (lihat §8 Rollback).

## 4. Roles Involved in UAT Execution

| Role UAT | Tanggung jawab |
|---|---|
| Business Process Owner | Memvalidasi hasil bisnis setiap skenario, memberi sign-off fungsional |
| IT / Security Owner | Memvalidasi skenario keamanan negatif, memberi sign-off keamanan |
| UAT Tester(s) | Mengeksekusi setiap kasus di `UAT_TEST_CASES.md`, mencatat bukti dan hasil |
| Release Authority | Keputusan akhir Go/No-Go produksi berdasarkan `UAT_SIGNOFF_TEMPLATE.md` |

Peran-peran BISNIS di atas berbeda dari peran/GROUP APLIKASI (lihat
`UAT_ROLE_MATRIX.md`) — satu Business Process Owner bisa saja login
sebagai beberapa actor aplikasi berbeda selama eksekusi UAT.

## 5. Terminology Rules (WAJIB DIPATUHI)

Istilah kanonik berikut **tidak boleh diterjemahkan atau digabung
maknanya** selama dokumentasi dan eksekusi UAT — gunakan istilah Inggris
asli, jelaskan maksudnya dalam Bahasa Indonesia jika perlu (lihat
`UAT_GLOSSARY.md`):

`NEED`, `FINANCIAL`, `Acceptance`, `Cancellation`, `Revision`,
`Fulfillment Exception`, `Purchase Attribution`, `Stock Attribution`,
`Responsibility Assignment`, `Delegation`.

Kuantitas kanonik berikut juga tidak boleh dicampur maknanya:

`requested_qty`, `approved_qty`, `delivered_qty`, `accepted_qty`,
`cancelled_qty`, `outstanding_qty`.

Formula wajib:

```
outstanding_qty = approved_qty - accepted_qty - cancelled_qty
```

**NEED Approval** = otorisasi kebutuhan bisnis / kuantitas yang disahkan
(mengatur `approved_qty`).

**FINANCIAL Approval** = otorisasi eksposur finansial / pengeluaran.

Keduanya adalah *purpose* Approval Cycle yang **terpisah**, meskipun
menggunakan mesin (engine) Approval Cycle yang sama persis. Jangan pernah
menganggap NEED Approval yang sudah selesai berarti FINANCIAL Approval
juga otomatis selesai, atau sebaliknya.

## 6. Entry Criteria

- `light-ir-m20` ter-deploy bersih di environment UAT (0 error saat
  instalasi — lihat evidence di `UAT_SIGNOFF_TEMPLATE.md`).
- Seluruh master/configuration data pada `UAT_MASTER_DATA_CHECKLIST.md`
  sudah diisi untuk minimal 2 Company.
- Minimal satu user nyata per actor pada `UAT_ROLE_MATRIX.md`.
- Dokumen ini sudah direview dan disetujui oleh Business Process Owner
  sebelum eksekusi dimulai.

## 7. Exit / Sign-off Criteria

- Setiap kasus di `UAT_TEST_CASES.md` dieksekusi dengan hasil **PASS dan
  bukti yang dapat diamati** terlampir. "Tombolnya berfungsi" tidak
  pernah cukup — rujuk field bisnis konkret (nomor IR, state, kuantitas
  kanonik, Approval Cycle, resolved approver vs actual actor, dokumen
  terkait).
- Setiap kasus negatif/keamanan (Bagian E) menunjukkan **penolakan yang
  memang benar-benar terjadi** — bukan diasumsikan, bukan disimpulkan
  dari ketiadaan tombol.
- Seluruh **14 Golden Scenario** tercakup (12 dieksekusi pada kartunya
  sendiri; UAT-GS-12 dan UAT-GS-13 berbagi bukti eksekusi dengan
  UAT-SEC-02 dan UAT-SEC-08 — lihat `UAT_SIGNOFF_TEMPLATE.md` §1a).
- Tidak ada server error (500) atau hasil bisnis yang salah secara diam-diam.
- **Perlakuan temuan terhadap keputusan GO** (aturan lengkap dan
  otoritatif ada di `UAT_SIGNOFF_TEMPLATE.md` §3a):
  - **BUG Critical/High** yang masih Open → **memblokir GO**.
  - **BUG Medium/Low** yang masih Open → tidak otomatis memblokir, tetapi
    **wajib** ada keputusan risk-acceptance tertulis dari Business
    Process Owner **dan** Release Authority.
  - **CONFIGURATION ISSUE** dan **UAT DATA ISSUE** → wajib diperbaiki dan
    **kasus terdampak dijalankan ulang** sampai PASS.
  - **DOCUMENTATION ISSUE** → wajib diperbaiki sebelum sign-off.
  - **CHANGE REQUEST** → **bukan** kegagalan UAT V1, tidak memblokir GO;
    dicatat sebagai masukan rilis berikutnya.
- Sign-off tercatat lengkap di `UAT_SIGNOFF_TEMPLATE.md`.

## 8. Rollback / Backup Considerations

- **Backup penuh database WAJIB dilakukan sebelum instalasi/upgrade** di
  environment UAT — sekali data transaksi bisnis ada (terutama
  Acceptance/Cancellation Event, yang bersifat immutable tanpa syarat,
  bahkan `sudo()` tidak bisa menulis ulang), tidak ada undo dari dalam
  aplikasi.
- Rollback kode: `git checkout` ke tag `light-ir-mXX` sebelumnya — trivial.
- Rollback data: **hanya** lewat restore backup database — rehearsal
  restore direkomendasikan sekali di environment UAT sebelum bergantung
  padanya untuk go-live produksi.

## 9. Deliverables in This Package

1. `UAT_MASTER_PLAN.md` — dokumen ini.
2. `UAT_TEST_CASES.md` — seluruh kasus uji detail.
3. `UAT_MASTER_DATA_CHECKLIST.md` — data master/konfigurasi wajib.
4. `UAT_ROLE_MATRIX.md` — matriks actor/group/capability/responsibility.
5. `UAT_GLOSSARY.md` — glosarium dwibahasa istilah kanonik.
6. `UAT_DEFECT_LOG_TEMPLATE.md` — template & klasifikasi temuan.
7. `UAT_SIGNOFF_TEMPLATE.md` — template keputusan Go/No-Go akhir.

## 10. Known, Intentionally Deferred Items (Not Defects)

Lihat detail lengkap di `LIGHT IR V1 RELEASE/UAT READINESS ANALYSIS`
(hasil analisis sebelumnya). Ringkasan yang relevan untuk tester:

- **Automatic Acceptance (FR-IR-100/101)** — belum diimplementasikan,
  sengaja deferred. Jangan dicatat sebagai kegagalan.
- Tidak ada tombol "Mark Done" / "Reopen" — completion dan reactivation
  terjadi otomatis sebagai efek samping dari aksi bisnis yang sudah ada
  (lihat Bagian D di `UAT_TEST_CASES.md`). Ini perilaku yang disengaja,
  bukan fitur yang hilang.
- Kombinasi eksekusi beberapa concurrency test class lama (M15-M17) bisa
  konflik satu sama lain di lingkungan test otomatis developer — ini
  murni isu isolasi test, tidak berdampak ke environment UAT/produksi
  sama sekali.
