# LIGHT Internal Request V1 — PRE-UAT Functional Walkthrough

**Baseline (Code):** `light-ir-m20` tag, `custom-addons@62ee406`
**Baseline (Docs):** `light-odoo` (paket UAT 7 dokumen sudah ada di
`docs/05_uat/`)
**Status dokumen ini:** Panduan click-through pertama untuk
owner/developer sendiri, di lingkungan lokal, **sebelum** Business UAT
resmi dimulai. Ini **bukan** dokumen Business UAT dan **tidak**
menggantikan `UAT_MASTER_PLAN.md` / `UAT_TEST_CASES.md` / template
sign-off. Hasil akhir dokumen ini adalah **PRE-UAT PASS** atau
**PRE-UAT BLOCKED** — istilah "Business UAT PASS" tidak pernah dipakai
di sini.

---

## 1. Tujuan & Posisi Dokumen

Dokumen ini adalah anak tangga baru di antara:

1. Automated test suite M20 (769 test standar + 1 concurrency test, 0
   failed/0 error — bukti sudah tercatat di finalisasi M20), dan
2. Paket Business UAT resmi (`UAT_MASTER_PLAN.md` dkk., 44 kartu kasus,
   40 kasus unik dieksekusi, ditujukan untuk business tester eksternal).

Tujuannya: memberi owner/developer sendiri kesempatan **click-through
manual pertama** di UI Odoo 18 yang sesungguhnya, untuk memastikan
setiap menu/tombol/wizard yang akan dipakai business tester nanti
benar-benar ada, bisa diklik, dan menghasilkan efek bisnis yang benar —
**sebelum** dokumen Business UAT diserahkan ke business tester.

Dokumen ini **bukan** pengganti automated test (tidak membuktikan
concurrency, tidak membuktikan seluruh kasus negatif keamanan — itu
tetap tanggung jawab `UAT_TEST_CASES.md` Bagian E) dan **bukan**
Business UAT resmi (tidak butuh sign-off formal, tidak butuh Business
Process Owner, cukup dijalankan sendiri oleh siapa pun yang menyiapkan
rilis).

Cakupan: 12 alur inti (§6) yang bersama-sama menelusuri seluruh siklus
hidup Internal Request — dari pembuatan sampai Completion dan
Controlled Reactivation — memakai UI sungguhan, bukan shell/API.

## 2. Catatan Baseline (Hasil Verifikasi Sumber)

Seluruh nama menu, tombol, wizard, dan opsi Selection di dokumen ini
diverifikasi langsung dari file XML/Python pada tag `light-ir-m20` —
**tidak ada** yang diasumsikan atau ditebak. Sumber yang diperiksa:

- `light_internal_request/views/menu.xml` (10 menuitem root)
- `light_internal_request/views/config_views.xml` (menu Configuration)
- `light_internal_request/views/auditor_menu.xml` (menu Audit, 11 item)
- `light_internal_request/views/internal_request_views.xml` (form + tombol header IR)
- `light_internal_request/views/internal_request_line_views.xml` (form + tombol header IR Line)
- `light_internal_request/views/approval_worklist_views.xml` (tombol Approve/Reject)
- `light_internal_request/views/history_views.xml` (form + tombol Resolve Fulfillment Exception)
- `light_internal_request/wizards/wizard_views.xml` (9 wizard form + tombol)
- `light_internal_request/models/fulfillment_exception.py` (opsi `exception_type`/`resolution_type`)
- `light_ir_purchase/views/purchase_views.xml` (menu Procurement, 5 item)
- `light_ir_stock/views/stock_attribution_views.xml` (menu Inventory)
- `light_internal_request/models/internal_request_financial.py` (konfirmasi `action_request_financial_authorization` tidak punya tombol/menu — lihat §7)

## 3. Pre-flight Lingkungan Lokal

Jalankan di lingkungan **development/local**, bukan environment UAT
resmi (yang dipakai untuk Business UAT nanti tetap harus fresh install
terpisah — lihat `UAT_MASTER_PLAN.md` §3).

- [ ] Database lokal dengan kelima addon LIGIT terinstal fresh, urutan
      sesuai `UAT_MASTER_DATA_CHECKLIST.md` §0 (`light_ir_approval` →
      `light_internal_request` → `light_ir_hr` → `light_ir_purchase` →
      `light_ir_stock`), 0 error.
- [ ] Minimal data master **untuk 1 Company saja** cukup untuk
      walkthrough ini (tidak perlu Company B/cross-company — itu
      kasus keamanan, sudah dicakup `UAT_TEST_CASES.md` Bagian E,
      bukan tujuan dokumen ini).
- [ ] Ikuti `UAT_MASTER_DATA_CHECKLIST.md` §1-§11 apa adanya untuk 1
      Company, KECUALI baris yang secara eksplisit untuk kasus negatif
      (§3 UAT-SEC-05/06, §4 UAT-SEC-08, §8 UAT-SEC-07) — baris itu
      tidak relevan untuk walkthrough fungsional ini.
- [ ] Aktifkan mode developer (`?debug=1`) — dibutuhkan untuk §7 dan
      untuk memverifikasi security group per user bila perlu.
- [ ] Siapkan user sesuai §4 di bawah.

Dokumen ini **tidak** menuntut backup/rollback rehearsal (itu syarat
Business UAT resmi, lihat `UAT_MASTER_PLAN.md` §8) — data lokal boleh
dibuang/reset kapan saja setelah walkthrough selesai.

## 4. Test User / Actor yang Dibutuhkan

Subset dari `UAT_ROLE_MATRIX.md` yang dipakai 12 alur di bawah:

| Actor | Group Aplikasi | Wewenang Ditentukan Oleh |
|---|---|---|
| Requester | — | Kepemilikan (`requester_user_id`) |
| NEED Approver | tidak ada group | Responsibility Assignment kode NEED Rule Step |
| Reviewer | `group_light_reviewer` | Kapabilitas + Company |
| Plan Approver | `group_light_plan_approver` | Kapabilitas + Company |
| Financial Officer | `group_light_financial_officer` | Gerbang kasar untuk meminta Tier-1 |
| FINANCIAL Approver | tidak ada group | Responsibility Assignment kode FINANCIAL Rule Step |
| Procurement | `group_light_procurement` + `purchase.group_purchase_user` | Kapabilitas + Company |
| Inventory | `group_light_inventory` + `stock.group_stock_user` | Kapabilitas + Company |
| Receiver | `group_light_receiver` | Kapabilitas + identitas efektif |
| Revision Authority | `group_light_revision_authority` | Kapabilitas + Responsibility `IR_REVISION_AUTHORITY` |
| Cancellation Authority | `group_light_canceller` | Kapabilitas + Responsibility `IR_CANCELLATION_AUTHORITY` |

Boleh memakai 1 user Administrator yang login bergantian sebagai
beberapa actor di atas untuk mempercepat walkthrough lokal (berbeda
dari Business UAT resmi yang mewajibkan 1 user = 1 kapabilitas —
lihat `UAT_ROLE_MATRIX.md` "Catatan Penting untuk Tester").

## 5. Lembar Contekan Kuantitas

Formula wajib (jangan dihitung ulang secara manual saat verifikasi —
cukup baca field-nya di form, lalu cocokkan dengan formula ini):

```
outstanding_qty = approved_qty - accepted_qty - cancelled_qty
```

| Field | Berubah oleh aksi |
|---|---|
| `requested_qty` | Diisi Requester saat Draft, tidak berubah lagi |
| `approved_qty` | NEED Approval (bisa lebih kecil dari `requested_qty` jika Quantity Reduction) |
| `delivered_qty` | Fulfillment Allocation + Purchase/Stock Attribution/Service Delivery Event |
| `accepted_qty` | Acceptance (naik), Acceptance Reversal (turun) |
| `cancelled_qty` | Cancellation (naik saja, tidak pernah turun) |

`is_pending_acceptance` = `delivered_qty > accepted_qty` (dipakai
filter menu "Pending Acceptance").

Lihat `UAT_GLOSSARY.md` untuk penjelasan istilah lengkap — jangan
diterjemahkan istilah kanonik (NEED, FINANCIAL, Acceptance,
Cancellation, Revision, Fulfillment Exception, Purchase Attribution,
Stock Attribution, Responsibility Assignment, Delegation) selama
menjalankan walkthrough ini.

---

## 6. 12 Alur Inti Walkthrough

Setiap alur memakai format tetap: **Tujuan** / **Actor** / **Prasyarat**
/ **Langkah UI** (tabel) / **Hasil Bisnis yang Diharapkan** / **Hasil
Kuantitas yang Diharapkan** / **Referensi UAT** / **Catatan**.

### F01 — Buat & Submit Internal Request

**Tujuan:** Membuktikan pembuatan IR dan transisi Draft → Submitted
memicu NEED Approval Cycle.

**Actor:** Requester

**Prasyarat:** Request Type, NEED Policy (Tier-1 atau Tier-2) sudah
dikonfigurasi untuk Company (`UAT_MASTER_DATA_CHECKLIST.md` §2, §4).

**Langkah UI:**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | LIGHT Internal Request → **My Requests** | Klik **New** | `request_type_id`, `receiver_user_id` (boleh kosong), `date_required`, minimal 1 baris di tab **Lines** (`product_id`, `requested_qty`) |
| 2 | Form IR (state = Draft) | Klik tombol **Submit** | — |

**Hasil Bisnis yang Diharapkan:** `state` berubah ke `Submitted`.
Smart button "Approval Cycles" pada `button_box` muncul dan menunjuk
ke satu Approval Cycle baru dengan `cycle_purpose = need`.

**Hasil Kuantitas yang Diharapkan:** `approved_qty = 0`,
`outstanding_qty = 0` (belum ada NEED Approval).

**Referensi UAT:** UAT-GS-01 (langkah awal).

**Catatan:** Jika Submit gagal dengan pesan Policy ganda/ambigu, itu
CONFIGURATION ISSUE pada data lokal (`UAT_MASTER_DATA_CHECKLIST.md`
§4), bukan BUG.

---

### F02 — NEED Approval (Penuh)

**Tujuan:** Membuktikan NEED Approver bisa menyetujui IR sesuai
`requested_qty` lewat Approval Worklist.

**Actor:** NEED Approver (resolved via Responsibility Assignment)

**Prasyarat:** F01 selesai, user NEED Approver login sesuai
resolusi Responsibility untuk kode step NEED Rule.

**Langkah UI:**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | LIGHT Internal Request → **Approval Worklist** | Filter **NEED** (filter bawaan `filter_need`) | — |
| 2 | Baris IR yang bersangkutan | Klik tombol **Approve** | Wizard **NEED Approval** terbuka — biarkan `approved_qty` = `requested_qty` (default) per baris |
| 3 | Wizard NEED Approval | Klik **Approve** | — |

**Hasil Bisnis yang Diharapkan:** `need_approval_status` menjadi
disetujui, `state` IR berubah dari `Submitted` ke `In Process`.
Approval Cycle Step tercatat `approved` dengan `resolved_user_id` =
`actual actor` (user yang sama, tanpa Delegation).

**Hasil Kuantitas yang Diharapkan:** `approved_qty = requested_qty`
pada tiap baris, `outstanding_qty = approved_qty`.

**Referensi UAT:** UAT-GS-01, UAT-AP-01.

---

### F03 — NEED Approval (Sebagian / Quantity Reduction)

**Tujuan:** Membuktikan NEED Approver bisa menyetujui kuantitas lebih
kecil dari yang diminta.

**Actor:** NEED Approver

**Prasyarat:** IR baru (ulangi F01) dengan minimal 1 baris.

**Langkah UI:**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | Approval Worklist (filter NEED) | Klik **Approve** pada baris IR | Wizard NEED Approval terbuka |
| 2 | Wizard, tabel `line_ids` | Ubah `approved_qty` pada satu baris menjadi lebih kecil dari `requested_qty` | — |
| 3 | Wizard | Klik **Approve** | — |

**Hasil Bisnis yang Diharapkan:** IR tetap lanjut ke `In Process`
meski `approved_qty < requested_qty` pada satu baris — ini adalah
Quantity Reduction yang sah, bukan Revision.

**Hasil Kuantitas yang Diharapkan:** `approved_qty` pada baris
tersebut < `requested_qty`, `outstanding_qty` mengikuti `approved_qty`
baris itu.

**Referensi UAT:** UAT-GS-02 (bagian NEED Approval).

---

### F04 — Review + Fulfillment Allocation

**Tujuan:** Membuktikan alur Reviewer menyusun Fulfillment Allocation
(pemenuhan internal vs purchase vs service).

**Actor:** Reviewer

**Prasyarat:** IR pada `In Process` (hasil F02), Governance Policy
dengan `review_required = True`.

**Langkah UI:**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | LIGHT Internal Request → **Review Worklist** | Buka IR yang perlu direview | — |
| 2 | Form IR | Klik tombol **Mark Reviewed** | — |

**Hasil Bisnis yang Diharapkan:** `review_status` berubah dari
`not_reviewed`, field `reviewer_user_id` terisi otomatis dengan user
yang login.

**Hasil Kuantitas yang Diharapkan:** Tidak ada perubahan kuantitas
kanonik pada langkah ini (Fulfillment Allocation dibuat lewat
proses Reviewer di belakang layar / model terkait, bukan tombol
tersendiri di form IR).

**Referensi UAT:** UAT-GS-01 (langkah Review), UAT-RL-03 (kapabilitas
Reviewer).

**Catatan:** Bila `review_required = False` di Governance Policy
lokal, langkah ini boleh dilewati (bukan blocker) — sesuaikan
`UAT_MASTER_DATA_CHECKLIST.md` §6 bila ingin menguji tombol ini.

---

### F05 — Plan Approval

**Tujuan:** Membuktikan Plan Approver bisa menyetujui atau menolak
Fulfillment Plan, terpisah dari Reviewer.

**Actor:** Plan Approver

**Prasyarat:** IR sudah melalui F04 (jika `plan_approval_required =
True` pada Governance Policy).

**Langkah UI (jalur Approve):**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | LIGHT Internal Request → **Plan Approval Worklist** | Buka IR | — |
| 2 | Form IR | Klik tombol **Approve Plan** | — |

**Hasil Bisnis yang Diharapkan:** `plan_approval_status` berubah dari
`draft`, `plan_approver_user_id` terisi.

**Langkah UI (jalur Reject, opsional untuk dicoba terpisah):**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | Form IR (`plan_approval_status = draft`) | Klik tombol **Reject Plan** | Wizard **Reject Fulfillment Plan** terbuka — isi `reason` |
| 2 | Wizard | Klik **Reject Plan** | — |

**Hasil Bisnis yang Diharapkan (Reject):** `plan_rejection_reason`
terisi dan tampil di form IR.

**Referensi UAT:** UAT-RL-04 (kapabilitas Plan Approver, terpisah
dari Reviewer).

---

### F06 — FINANCIAL Authorization Tier-1 + FINANCIAL Approval

**Tujuan:** Membuktikan FINANCIAL Approval Tier-1 (di level IR)
berjalan benar dan **terpisah** dari NEED Approval.

**Actor:** Financial Officer (meminta), FINANCIAL Approver (menyetujui)

**Prasyarat:** IR dengan minimal 1 baris ber-`purchase_allocated_qty >
0` (baris yang dialokasikan ke Purchase, bukan internal fulfillment
murni — lihat §7 poin gap UI), Validated Price sudah ada pada baris
tersebut, FINANCIAL Policy Tier-1 dikonfigurasi
(`UAT_MASTER_DATA_CHECKLIST.md` §5).

**Langkah UI:**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | *(Lihat §7 — tidak ada tombol/menu UI untuk memicu langkah ini di `light-ir-m20`.)* | — | — |
| 2 | LIGHT Internal Request → **Approval Worklist**, filter **FINANCIAL** | Klik **Approve** pada baris cycle FINANCIAL | Wizard generik Approve (form default, bukan wizard `need.approve`) |

**Hasil Bisnis yang Diharapkan:** FINANCIAL Approval Cycle
(`cycle_purpose = financial`) terpisah dari NEED Approval Cycle pada
IR yang sama — verifikasi lewat smart button "Approval Cycles":
harus ada 2 cycle (satu `need`, satu `financial`), bukan satu cycle
gabungan.

**Referensi UAT:** UAT-AP-01, UAT-AP-02.

**Catatan:** Langkah 1 **tidak bisa dijalankan murni lewat UI** pada
baseline ini — lihat temuan di §7 dan §9. Untuk keperluan walkthrough
lokal, trigger lewat mode developer: buka IR terkait, `?debug=1` →
menu teknis (ikon kutu/bug) → **Execute Action** → cari
`action_request_financial_authorization` pada model
`light.internal.request`. Ini **workaround developer**, bukan langkah
yang boleh diberikan ke business tester.

---

### F07 — Procurement: Sourcing → Purchase Attribution

**Tujuan:** Membuktikan alur Procurement dari kandidat sourcing sampai
Purchase Attribution formal.

**Actor:** Procurement

**Prasyarat:** IR dengan baris yang dialokasikan ke Purchase (hasil
F04/Fulfillment Allocation), vendor tersedia
(`UAT_MASTER_DATA_CHECKLIST.md` §9).

**Langkah UI:**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | LIGHT Internal Request → **Procurement** (root, aksi = Procurement Worklist) | Buka baris kandidat (`state = candidate`) | — |
| 2 | LIGHT Internal Request → Procurement → **Sourcing** | Verifikasi baris `light.ir.purchase.sourcing` terkait, `sourcing_qty`/`awarded_qty` | — |
| 3 | LIGHT Internal Request → Procurement → **Attributions** | Verifikasi baris `light.ir.purchase.attribution` terbentuk setelah PO/RFQ diproses (proses PO standar Odoo, di luar LIGHT) | — |

**Hasil Bisnis yang Diharapkan:** `light.ir.purchase.attribution`
menghubungkan `line_id` IR dengan `purchase_order_line_id` yang
sungguhan, `state` badge terisi, `attributed_by_user_id` tercatat.

**Referensi UAT:** UAT-GS-04, UAT-GS-05, UAT-GS-06, UAT-RL-07.

**Catatan:** Langkah pembuatan PO/RFQ itu sendiri memakai UI Odoo
Purchase standar (`purchase.order`) — bukan bagian dari menu LIGHT,
dan sengaja tidak diklaim di sini karena di luar cakupan verifikasi
addon LIGHT (lihat CLAUDE.md — "Do not replace `purchase.order`").

---

### F08 — Stock: Internal Fulfillment Worklist → Stock Attribution

**Tujuan:** Membuktikan alur Inventory untuk baris yang dialokasikan
ke pemenuhan internal (bukan Purchase).

**Actor:** Inventory

**Prasyarat:** IR dengan baris yang dialokasikan internal, warehouse +
picking type `internal` aktif (`UAT_MASTER_DATA_CHECKLIST.md` §10).

**Langkah UI:**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | LIGHT Internal Request → **Internal Fulfillment Worklist** | Buka baris `light.ir.stock.attribution` berstatus `active` | — |
| 2 | Proses stock move terkait lewat UI Inventory standar Odoo (`stock.picking`) | Validasi picking | — |

**Hasil Bisnis yang Diharapkan:** `light.ir.stock.attribution`
menghubungkan `line_id` IR dengan `stock_move_id` sungguhan,
`attributed_qty` terisi, `state` badge berubah.

**Referensi UAT:** UAT-GS-02, UAT-GS-03, UAT-RL-08.

---

### F09 — Acceptance → Completion (Done)

**Tujuan:** Membuktikan Acceptance mengurangi `outstanding_qty` dan
memicu Completion otomatis (`state = done`) ketika seluruh baris
sudah memenuhi syarat DAN ada `accepted_qty > 0`.

**Actor:** Receiver

**Prasyarat:** `delivered_qty > accepted_qty` pada baris (hasil F07
atau F08), tidak ada Fulfillment Exception terbuka pada baris
tersebut.

**Langkah UI:**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | LIGHT Internal Request → **Pending Acceptance** | Buka baris (filter `is_pending_acceptance = True` otomatis aktif) | — |
| 2 | Form IR Line, tombol header **Accept** | Klik (tombol hanya tampak jika `delivered_qty - accepted_qty > 0`) | Wizard **Accept Delivered Quantity** terbuka |
| 3 | Wizard | Isi `quantity` (default penuh sisa), `reason` opsional | Klik **Accept** |

**Hasil Bisnis yang Diharapkan:** `light.ir.acceptance.event` baru
(`event_type = acceptance`) tercatat di tab **Acceptances**.
Jika ini baris terakhir yang memenuhi syarat completion dan
`accepted_qty > 0` di minimal satu baris pada IR, `state` IR otomatis
berubah dari `In Process` ke **Done** — **tanpa tombol apa pun**
(bukan bug, lihat `UAT_GLOSSARY.md` "Completion").

**Hasil Kuantitas yang Diharapkan:** `accepted_qty` naik,
`outstanding_qty` turun sesuai formula §5.

**Referensi UAT:** UAT-CP-01, UAT-CP-02, UAT-GS-07/08/09/10 (masing-
masing sesuai alur allocation-nya).

---

### F10 — Reverse Acceptance → Controlled Reactivation

**Tujuan:** Membuktikan Acceptance Reversal pada IR yang sudah `Done`
memicu Controlled Reactivation kembali ke `In Process`.

**Actor:** Receiver

**Prasyarat:** IR pada state `Done` (hasil F09), baris dengan
`accepted_qty > 0`.

**Langkah UI:**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | Buka IR Line yang sudah `Done` (lewat **All Requests** atau **Quantity Monitoring**) | Klik tombol header **Reverse Acceptance** (tampak hanya jika `accepted_qty > 0`) | Wizard **Reverse Acceptance** terbuka |
| 2 | Wizard | Isi `quantity` (≤ `accepted_qty`), `reason` | Klik **Reverse** |

**Hasil Bisnis yang Diharapkan:** `light.ir.acceptance.event` baru
(`event_type = reversal`) tercatat — event Acceptance asli **tidak
dihapus/diedit**. `state` IR berubah dari `Done` kembali ke
`In Process` (Controlled Reactivation) **karena** baris tersebut
sekarang punya `delivered_qty > accepted_qty` lagi (pending
acceptance kembali).

**Hasil Kuantitas yang Diharapkan:** `accepted_qty` turun,
`outstanding_qty` naik kembali sesuai formula §5.

**Referensi UAT:** UAT-CP-05, UAT-CP-06 (Controlled Reactivation via
Acceptance Reversal).

**Catatan:** Tidak ada tombol "Reopen" — reaktivasi murni efek
samping. Jika `state` tidak berubah kembali ke `In Process`, ini
kandidat BUG (bertentangan dengan DEC-041) — catat di §9 dengan
detail lengkap sebelum menyimpulkan.

---

### F11 — Report & Resolve Fulfillment Exception

**Tujuan:** Membuktikan Fulfillment Exception tidak otomatis mengubah
kuantitas kanonik sampai diresolusi secara eksplisit.

**Actor:** Receiver (report), Receiver (resolve — actor sama di V1)

**Prasyarat:** Baris IR dengan `delivered_qty > 0`.

**Langkah UI (Report):**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | Form IR Line | Klik tombol header **Report Exception** | Wizard **Report Fulfillment Exception** terbuka |
| 2 | Wizard | Pilih `exception_type` (label field: **Jenis Exception** — opsi: Damaged, Wrong Product, Wrong Specification, Shortage, Quality Issue, Service Incomplete, Other), isi `quantity`, `note` | Klik **Report Exception** |

**Hasil Bisnis yang Diharapkan:** `light.ir.fulfillment.exception`
baru, `status = open`. **Tidak ada** perubahan `accepted_qty`/
`cancelled_qty` pada langkah ini.

**Langkah UI (Resolve):**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | LIGHT Internal Request → **Exceptions** (hanya menampilkan `status = open`) | Buka Exception yang baru dibuat | — |
| 2 | Form Fulfillment Exception, tombol header **Resolve** (tampak hanya jika `status = open`) | Klik | Wizard **Resolve Fulfillment Exception** terbuka |
| 3 | Wizard | Pilih `resolution_type` (label field: **Jenis Resolusi** — opsi: Replacement, Return and Replace, Redelivery, Accept As Is, Cancel Remaining, Other), isi `resolution_note` | Klik **Resolve** |

**Hasil Bisnis yang Diharapkan:** `status` Exception berubah dari
`open`, `resolution_type`/`resolved_by_user_id`/`resolved_date`
terisi. Jika IR sebelumnya sempat `Done`/`Cancelled` dan exception
ini terbuka pada baris tersebut, pelaporan Exception (bukan resolusi-
nya) yang memicu Controlled Reactivation (lihat `UAT_GLOSSARY.md`).

**Referensi UAT:** UAT-GS-08, UAT-CP-07, UAT-CP-08.

---

### F12 — Revision (Reaktivasi) & Cancel Outstanding (Completion sebagai Cancelled)

**Tujuan:** Membuktikan dua jalur completion/reaktivasi terakhir:
Revision yang disetujui menaikkan kuantitas pada IR terminal
(reaktivasi), dan Cancellation penuh menghasilkan `state = Cancelled`
tanpa `accepted_qty` sama sekali.

**Actor:** Revision Authority, Cancellation Authority

**Prasyarat (Revision):** IR pada state `Done`/`Cancelled`.
**Prasyarat (Cancellation):** IR line dengan `outstanding_qty > 0` dan
tanpa `accepted_qty > 0` sama sekali di seluruh baris (agar hasil akhir
`Cancelled`, bukan `Done`).

**Langkah UI (Revision):**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | Form IR Line | Klik tombol header **Propose Revision** | Wizard **Propose Revision** terbuka |
| 2 | Wizard | Isi `reason`, `new_requested_qty` (naikkan dari `requested_qty` semula), opsional `new_product_id`/`new_description` | Klik **Propose Revision** |
| 3 | LIGHT Internal Request → **Active Revisions** | Terapkan/setujui Revision sesuai wewenang Revision Authority | — |

**Hasil Bisnis yang Diharapkan:** Revision baru `status = active` →
`applied`. Jika Revision menaikkan `approved_qty` efektif pada IR yang
sebelumnya terminal, `state` IR reaktivasi kembali ke `In Process`.
Riwayat Revision lama (jika ada) tetap tersimpan, tidak ditimpa.

**Langkah UI (Cancellation):**

| No | Menu / Layar | Aksi | Field Diisi |
|---|---|---|---|
| 1 | Form IR Line, `outstanding_qty > 0` | Klik tombol header **Cancel Outstanding** (tampak hanya jika `outstanding_qty > 0`) | Wizard **Cancel Outstanding Quantity** terbuka |
| 2 | Wizard | Isi `quantity` (≤ `outstanding_qty`), `reason` | Klik **Cancel Quantity** |
| 3 | LIGHT Internal Request → **Cancellation History** | Verifikasi baris `light.ir.cancellation` baru | — |

**Hasil Bisnis yang Diharapkan:** `cancelled_qty` naik,
`outstanding_qty` turun ke 0 pada baris tersebut. Jika ini baris
terakhir yang membuat seluruh IR memenuhi syarat completion, dan
**tidak ada** baris mana pun dengan `accepted_qty > 0`, `state` IR
otomatis menjadi **Cancelled** (bukan Done).

**Referensi UAT:** UAT-CP-03, UAT-CP-04, UAT-CP-09/UAT-GS-10 (Revision
reactivates terminal IR — kartu rujukan-silang).

---

## 7. UI Discovery Check — Temuan Verifikasi Menu/Tombol

Hasil pemeriksaan langsung terhadap seluruh XML menu/view/wizard pada
`light-ir-m20`:

| Area | Status | Detail |
|---|---|---|
| Menu root + 10 anak (`menu.xml`) | ✅ Sesuai | Semua nama/aksi/groups/sequence cocok §6 |
| Menu Configuration (5 anak) | ✅ Sesuai | Request Types, Request Governance Policies, Approval Policies, Responsibility Assignments, Approval Delegations |
| Menu Audit (11 anak) | ✅ Sesuai | Termasuk 2 item lintas-addon (Purchase/Stock Attributions dari `light_ir_purchase`/`light_ir_stock`) |
| Menu Procurement (root + 4 anak) | ✅ Sesuai | Procurement (root=Procurement Worklist), Sourcing, Attributions, Receipt Attributions, Service Delivery Events |
| Menu Inventory (1 item) | ✅ Sesuai | Internal Fulfillment Worklist — tidak ada sub-menu Inventory lain |
| 4 tombol header form IR | ✅ Sesuai | Submit, Mark Reviewed, Approve Plan, Reject Plan |
| 5 tombol header form IR Line | ✅ Sesuai | Accept, Reverse Acceptance, Cancel Outstanding, Propose Revision, Report Exception |
| Tombol Approve/Reject di Approval Worklist | ✅ Sesuai | Generik untuk NEED maupun FINANCIAL, dibedakan lewat filter `NEED`/`FINANCIAL` |
| Tombol Resolve pada form Fulfillment Exception | ✅ Sesuai | `history_views.xml` — hanya tampak saat `status = open` |
| 9 wizard form + tombol konfirmasi | ✅ Sesuai | Semua label dikonfirmasi persis (lihat §6 per alur) |
| Opsi `exception_type`/`resolution_type` | ✅ Sesuai | Label field sesungguhnya adalah **"Jenis Exception"**/**"Jenis Resolusi"** (Bahasa Indonesia, bukan invented) |
| **FINANCIAL Authorization Tier-1** (`action_request_financial_authorization`) | ❌ **Tidak ada UI** | Method Python ada dan teruji lengkap (`test_financial_authorization.py`), tapi **tidak ada tombol atau menu apa pun** yang memanggilnya di seluruh `light_internal_request/views/*.xml`. Dikonfirmasi lewat grep menyeluruh — hanya muncul di test dan di docstring `approval_cycle_financial.py`. Tidak disebut sebagai gap yang disengaja di dokumen governance mana pun yang diperiksa (PRD/FRS/NFR/TR-04/ADR/DECISION_REGISTER). |

## 8. Rekomendasi First-Run

- Jalankan F01 → F02 → F04 → F05 → F09 dulu (jalur paling pendek
  menuju Completion) sebelum mencoba F06 (butuh workaround developer)
  dan F10-F12 (butuh IR yang sudah terminal).
- Jalankan F07 **atau** F08 sesuai jenis alokasi baris yang dipakai —
  keduanya tidak perlu dijalankan berurutan pada IR yang sama kecuali
  ingin menguji baris campuran (mixed allocation, lihat UAT-GS-06).
- Simpan nomor IR (`name`) setiap alur yang berhasil — akan
  memudahkan pengisian evidence saat Business UAT resmi nanti
  memakai kasus yang setara.
- Jangan jalankan F06 di environment yang akan dipakai untuk Business
  UAT resmi tanpa lebih dulu memutuskan bagaimana Financial Officer
  akan memicu Tier-1 di produksi (lihat §9 DEF-PU-01).

## 9. Issue Log (Temuan Selama PRE-UAT)

Format sama seperti `UAT_DEFECT_LOG_TEMPLATE.md`, memakai prefix
`DEF-PU-` (khusus PRE-UAT) supaya tidak bentrok penomoran dengan
Defect Log Business UAT resmi.

| Defect ID | Alur Terkait | Klasifikasi | Deskripsi | Dampak terhadap PRE-UAT | Rekomendasi |
|---|---|---|---|---|---|
| DEF-PU-01 | F06 | **DOCUMENTATION ISSUE** *(cakupan spesifikasi)* — bukan BUG karena tidak ada baseline requirement yang secara eksplisit mewajibkan tombol UI untuk aksi ini | `action_request_financial_authorization()` di `light_internal_request/models/internal_request_financial.py` tidak punya entry point UI (tombol/menu) sama sekali — hanya bisa dipanggil lewat test/shell/Execute Action mode developer | **Tidak memblokir PRE-UAT PASS** — 11 dari 12 alur lain sepenuhnya bisa di-click-through. Alur F06 sendiri harus memakai workaround developer, bukan jalur bisnis normal | Sebelum Business UAT resmi dimulai, business perlu memutuskan: (a) tambahkan tombol UI untuk memicu Tier-1 sebagai perubahan kecil di luar cakupan dokumentasi ini, atau (b) konfirmasi bahwa trigger ini memang dimaksudkan lewat automation/server action lain di luar UI form IR — **keputusan ini di luar wewenang dokumen read-only ini** |

*(tambahkan baris sesuai temuan yang muncul saat walkthrough
sesungguhnya dijalankan — tabel di atas hanya memuat temuan dari
tahap verifikasi baca-saja sebelum eksekusi klik langsung)*

## 10. PRE-UAT Exit Gate

- [ ] Seluruh 12 alur di §6 sudah dicoba klik-langsung minimal 1 kali
      di lingkungan lokal.
- [ ] Setiap hasil bisnis dan kuantitas yang diharapkan di setiap alur
      benar-benar teramati (bukan diasumsikan).
- [ ] DEF-PU-01 sudah dibawa ke pembahasan (keputusan (a)/(b) di §9)
      sebelum paket Business UAT (`UAT_MASTER_PLAN.md` dkk.)
      diserahkan ke business tester — **bukan syarat blocking PASS**,
      tapi wajib dikomunikasikan.
- [ ] Tidak ditemukan menu/tombol yang hilang di luar yang sudah
      dicatat di §7/§9.
- [ ] Tidak ada server error (500) pada satu pun dari 12 alur.

**Hasil akhir:** Jika seluruh butir di atas terpenuhi →
**PRE-UAT PASS**. Jika ada butir yang gagal (selain DEF-PU-01 yang
memang tidak blocking) → **PRE-UAT BLOCKED**, dengan detail temuan
dicatat di §9 sebelum paket Business UAT diserahkan.

---

Dokumen ini melengkapi, bukan menggantikan, ketujuh dokumen di
`docs/05_uat/`: `UAT_MASTER_PLAN.md`, `UAT_TEST_CASES.md`,
`UAT_MASTER_DATA_CHECKLIST.md`, `UAT_ROLE_MATRIX.md`,
`UAT_GLOSSARY.md`, `UAT_DEFECT_LOG_TEMPLATE.md`,
`UAT_SIGNOFF_TEMPLATE.md`.
