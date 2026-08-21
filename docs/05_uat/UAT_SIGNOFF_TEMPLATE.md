# LIGHT Internal Request V1 — UAT Sign-off

**Baseline (Code):** `light-ir-m20` — commit: ______________________
**Baseline (Docs):** `light-odoo` — commit: ______________________
**UAT Environment:** ______________________
**UAT Period:** dari ______________ sampai ______________

---

## 1. Execution Summary

| Kategori | Kasus Unik Dieksekusi | PASS | FAIL | Blocked / Not Executed |
|---|---|---|---|---|
| A. Golden Business Scenarios | 12 | | | |
| B. Approval Validation (NEED / FINANCIAL Tier-1 / Tier-2) | 3 | | | |
| C. Role / Capability Validation | 8 | | | |
| D. Completion Behavior | 9 | | | |
| E. Security Negative Tests | 8 | | | |
| **Total kasus unik dieksekusi** | **40** | | | |

**Catatan penghitungan:** `UAT_TEST_CASES.md` memuat **44 kartu kasus**,
namun **4 di antaranya adalah kartu rujukan-silang** yang tidak memiliki
Execution Record sendiri dan tidak dieksekusi dua kali. Maka jumlah
kasus unik yang benar-benar dieksekusi = **40**.

### 1a. Golden Scenario Coverage — 14/14

Cakupan Golden Scenario tetap **14 dari 14** sesuai baseline M20.
Dua di antaranya **berbagi bukti eksekusi** dengan kasus keamanan:

| Golden Scenario | Dieksekusi sebagai | Bukti diambil dari |
|---|---|---|
| UAT-GS-12 — Cross-company violation blocked | **UAT-SEC-02** | Execution Record UAT-SEC-02 |
| UAT-GS-13 — Self-approval skip | **UAT-SEC-08** | Execution Record UAT-SEC-08 |

Dua belas Golden Scenario lain (GS-01..GS-11, GS-14) dieksekusi dan
dicatat pada kartunya masing-masing.

- [ ] Konfirmasi: seluruh **14 Golden Scenario** tercakup (12 kartu GS
      dieksekusi sendiri + 2 lewat berbagi bukti di atas).

### 1b. Seluruh Kartu Rujukan-Silang

| Kartu | Bukti diambil dari |
|---|---|
| UAT-GS-12 | UAT-SEC-02 |
| UAT-GS-13 | UAT-SEC-08 |
| UAT-CP-09 (Revision reactivates terminal IR) | UAT-GS-10 |
| UAT-RL-09 (Auditor) | UAT-SEC-03 |

- [ ] Konfirmasi: setiap kartu rujukan-silang di atas sudah dipastikan
      bukti sumbernya PASS.

## 2. Automated Regression Evidence (Referensi, Bukan Pengganti UAT)

- Fresh install `light-ir-m20`: ______ modul, 0 error (Ya/Tidak)
- Regresi LIGHT standar: 769 test, 0 failed, 0 error (Ya/Tidak, lampirkan log)
- Concurrency completion test: 1/1 PASS (Ya/Tidak)
- `git diff --check`: clean (Ya/Tidak)

## 3. Open Defects at Sign-off Time

| Defect ID | Klasifikasi | Severity | Status | Perlakuan (lihat aturan di bawah) |
|---|---|---|---|---|
| | | | | |

*(rujuk `UAT_DEFECT_LOG_TEMPLATE.md` untuk detail lengkap)*

### 3a. Aturan Perlakuan Temuan terhadap Keputusan GO

| Klasifikasi | Severity | Perlakuan terhadap sign-off |
|---|---|---|
| **BUG** | Critical / High | **Memblokir GO.** Tidak boleh ada yang berstatus Open saat sign-off. |
| **BUG** | Medium / Low | Tidak otomatis memblokir, namun **wajib ada keputusan risk-acceptance eksplisit** yang ditandatangani Business Process Owner **dan** Release Authority (isi §3b). Tanpa keputusan tertulis, dianggap memblokir. |
| **CONFIGURATION ISSUE** | apa pun | **Wajib diperbaiki**, lalu **kasus UAT yang terdampak dijalankan ulang** sampai PASS. Tidak boleh ditutup hanya dengan penjelasan. |
| **UAT DATA ISSUE** | apa pun | **Wajib diperbaiki**, lalu **kasus UAT yang terdampak dijalankan ulang** sampai PASS. |
| **DOCUMENTATION ISSUE** | apa pun | **Wajib diperbaiki** pada dokumen UAT terkait sebelum sign-off. Tidak memerlukan eksekusi ulang kasus kecuali instruksi kasusnya sendiri yang salah. |
| **CHANGE REQUEST** | apa pun | **Bukan kegagalan UAT V1** dan **tidak memblokir GO** — sistem berjalan sesuai desain V1 yang sudah dikunci. Dicatat sebagai masukan untuk pertimbangan rilis berikutnya. |

**Tidak boleh melemahkan syarat bukti:** perlakuan di atas mengatur
*keputusan GO*, bukan standar bukti. Persyaratan `UAT_MASTER_PLAN.md`
§7 tetap berlaku penuh — setiap kasus harus tetap menghasilkan **PASS
dengan bukti yang dapat diamati**, dan setiap kasus negatif/keamanan
harus menunjukkan **penolakan yang benar-benar terjadi**, bukan
diasumsikan. Sebuah kasus yang belum PASS tidak menjadi "selesai" hanya
karena temuannya diklasifikasikan sebagai Medium/Low atau CHANGE
REQUEST.

### 3b. Risk Acceptance untuk BUG Medium/Low yang Masih Open

| Defect ID | Ringkasan Risiko Bisnis | Mitigasi/Workaround | Diterima Oleh (BPO) | Diterima Oleh (Release Authority) | Tanggal |
|---|---|---|---|---|---|
| | | | | | |

## 4. Deferred / Out-of-Scope Items Acknowledged

- [ ] Automatic Acceptance (FR-IR-100/101) — dipahami tetap deferred,
      bukan bagian dari V1.
- [ ] Tidak ada tombol Mark Done/Reopen — dipahami sebagai perilaku
      otomatis yang disengaja.
- [ ] Pengujian beban/performa skala produksi belum dilakukan di UAT
      ini — direkomendasikan sebagai aktivitas terpisah sebelum go-live.

## 5. Rollback Readiness Confirmation

- [ ] Backup database penuh sebelum instalasi/upgrade UAT sudah dibuat.
- [ ] Rehearsal restore sudah dilakukan minimal satu kali di environment
      UAT.

## 6. Sign-off

| Peran | Nama | Jabatan | Tanda Tangan | Tanggal | Keputusan |
|---|---|---|---|---|---|
| Business Process Owner | | | | | Approve / Reject / Conditional |
| IT / Security Owner | | | | | Approve / Reject / Conditional |
| Release Authority | | | | | **GO / NO-GO** |

### Kondisi (jika Conditional/GO bersyarat)

______________________________________________________________________
______________________________________________________________________

### Catatan Akhir

______________________________________________________________________
______________________________________________________________________

---

Dokumen ini, beserta seluruh evidence terlampir (log eksekusi per
kasus di `UAT_TEST_CASES.md`, defect log, dan output regresi otomatis),
menjadi arsip resmi keputusan Go/No-Go produksi untuk LIGHT Internal
Request V1 (`light-ir-m20`).
