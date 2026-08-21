# LIGHT Internal Request V1 — UAT Defect Log Template

Baseline: `light-ir-m20`. Setiap temuan selama UAT dicatat di sini dengan
klasifikasi yang tepat — klasifikasi yang salah bisa menyebabkan pekerjaan
implementasi yang tidak perlu atau, sebaliknya, mengabaikan bug sungguhan.

## Klasifikasi Temuan (WAJIB pilih salah satu)

### BUG
Hasil sistem yang **bertentangan langsung** dengan baseline V1 yang sudah
dikunci (dokumentasi governance, FR/TR/ADR/DEC, atau perilaku yang sudah
dibuktikan lewat automated test di `light-ir-m20`). Contoh: kuantitas
salah hitung, state berubah tanpa lewat aksi yang sah, security check
tidak berjalan sebagaimana didokumentasikan.

**Jangan** klasifikasikan sebagai BUG:
- Permintaan perilaku baru/berbeda yang tidak pernah menjadi bagian
  baseline V1 (lihat CHANGE REQUEST).
- Ketiadaan Automatic Acceptance (FR-IR-100/101) — ini memang belum
  diimplementasikan, bukan cacat.
- Ketiadaan tombol "Mark Done"/"Reopen" — completion dan reactivation
  memang dirancang otomatis, bukan lewat tombol (lihat
  `UAT_GLOSSARY.md`).

### CONFIGURATION ISSUE
Hasil sistem yang tidak sesuai harapan **karena** data
master/konfigurasi UAT belum benar (Policy ganda/ambigu, Responsibility
Assignment belum diisi, Rule Step salah kode, dll.) — bukan karena kode
aplikasi salah. Perbaikan: ralat data di
`UAT_MASTER_DATA_CHECKLIST.md`, ulangi kasus.

### DOCUMENTATION ISSUE
Dokumen UAT (Master Plan, Test Cases, Glossary, dll.) mengandung
kesalahan, istilah yang tidak konsisten, atau instruksi yang tidak jelas
— sistem sendiri berjalan benar. Perbaikan: revisi dokumen UAT, bukan
kode aplikasi.

### UAT DATA ISSUE
Data uji yang dipakai tester sendiri salah/tidak representatif (produk
salah, kuantitas tidak masuk akal, user salah login) — bukan masalah
konfigurasi sistem maupun kode. Perbaikan: ulangi kasus dengan data yang
benar.

### CHANGE REQUEST
Permintaan perilaku BARU atau BERBEDA dari yang sudah dikunci di
baseline V1 — sistem berjalan **sesuai** desain V1, namun bisnis
menginginkan sesuatu yang lain. Ini **bukan** kegagalan UAT terhadap V1
dan tidak menghalangi sign-off V1 — dicatat sebagai input untuk
pertimbangan rilis berikutnya, di luar cakupan dokumen ini.

---

## Defect Log

| Defect ID | UAT ID Terkait | Tanggal Ditemukan | Ditemukan Oleh | Klasifikasi | Deskripsi | Bukti (evidence) | Dampak Bisnis | Status | Ditutup Oleh | Tanggal Ditutup | Catatan |
|---|---|---|---|---|---|---|---|---|---|---|---|
| DEF-001 | | | | | | | | Open / In Review / Closed / Rejected | | | |
| DEF-002 | | | | | | | | | | | |
| DEF-003 | | | | | | | | | | | |

*(tambahkan baris sesuai kebutuhan)*

## Status Definitions

- **Open** — baru dicatat, belum ditinjau.
- **In Review** — sedang dianalisis untuk menentukan klasifikasi/akar
  masalah.
- **Closed** — sudah diselesaikan (data diperbaiki, dokumen direvisi,
  atau — khusus BUG sejati — kode sudah diperbaiki lewat proses
  implementasi terpisah dan diverifikasi ulang).
- **Rejected** — setelah ditinjau, ternyata bukan penyimpangan dari
  baseline V1 (misal, salah paham perilaku yang memang disengaja).

## Severity Guidance

Severity **wajib diisi untuk setiap temuan berklasifikasi BUG**, karena
menentukan perlakuannya terhadap keputusan GO.

| Severity | Kriteria |
|---|---|
| Critical | Data bisnis salah/hilang, security control gagal, blocking untuk seluruh alur bisnis |
| High | Satu alur bisnis utama gagal, tidak ada workaround |
| Medium | Alur bisnis terganggu tapi ada workaround |
| Low | Kosmetik, tidak mempengaruhi hasil bisnis |

## Perlakuan Temuan terhadap Keputusan GO

Aturan otoritatif ada di **`UAT_SIGNOFF_TEMPLATE.md` §3a** (dan
diringkas di `UAT_MASTER_PLAN.md` §7). Ringkasan:

| Klasifikasi | Perlakuan |
|---|---|
| BUG — Critical/High | **Memblokir GO** selama masih Open |
| BUG — Medium/Low | Tidak otomatis memblokir, **wajib** risk-acceptance tertulis dari Business Process Owner **dan** Release Authority (`UAT_SIGNOFF_TEMPLATE.md` §3b) |
| CONFIGURATION ISSUE | Perbaiki data/konfigurasi, lalu **jalankan ulang kasus terdampak** sampai PASS |
| UAT DATA ISSUE | Perbaiki data uji, lalu **jalankan ulang kasus terdampak** sampai PASS |
| DOCUMENTATION ISSUE | Perbaiki dokumen UAT terkait sebelum sign-off |
| CHANGE REQUEST | **Bukan** kegagalan UAT V1, **tidak** memblokir GO — masukan untuk rilis berikutnya |

**Penting:** klasifikasi tidak pernah menurunkan standar bukti. Sebuah
kasus UAT hanya boleh ditandai PASS jika bukti yang dapat diamati benar-
benar diperoleh (`UAT_MASTER_PLAN.md` §7) — bukan karena temuannya
dianggap ringan atau dialihkan menjadi CHANGE REQUEST.
