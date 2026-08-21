# LIGHT Internal Request V1 — UAT Test Cases

Baseline: `light-ir-m20`. Sebelum eksekusi, pastikan
`UAT_MASTER_DATA_CHECKLIST.md` sudah lengkap dan
`UAT_ROLE_MATRIX.md`/`UAT_GLOSSARY.md` sudah dipahami tester. Setiap
kasus wajib diisi bagian *Execution Record* di bagian bawah kartu
masing-masing — "tombolnya berfungsi" saja **tidak cukup** sebagai bukti;
selalu rujuk field bisnis konkret (nomor IR, state, kuantitas, Approval
Cycle, resolved approver vs actual actor, PO/RFQ, Stock Picking,
Attribution, Acceptance/Cancellation Event, Revision, Fulfillment
Exception) sebagaimana relevan.

Format setiap kasus:

```
### UAT-XX-NN — Judul

Business Process / Business Objective / Business Actor
Preconditions / Required Master Data / Starting State

| Step | User Action | Expected System Behaviour |
|---|---|---|
...

Expected Business Result / Expected State / Expected Quantity Result /
Expected Approval Result / Negative-Security Expectation / Evidence Required

--- Execution Record ---
Actual Result / PASS/FAIL / Tester / Execution Date /
Defect-Finding Reference / Business Comment
```

---

# BAGIAN A — Golden Business Scenarios (14)

### UAT-GS-01 — Simple Purchase

**Business Process:** Purchase Fulfillment
**Business Objective:** Membuktikan alur penuh permintaan barang yang
dipenuhi lewat pembelian ke vendor tunggal berjalan benar dari awal
sampai selesai.
**Business Actor:** Requester, NEED Approver, Reviewer, Procurement,
FINANCIAL Approver (jika tier terlampaui), Receiver
**Preconditions:** Master data §1-6, 9 pada `UAT_MASTER_DATA_CHECKLIST.md`
**Required Master Data:** Company A, Request Type, NEED Policy/Rule/Step,
Governance Policy, FINANCIAL Policy (jika PO melewati threshold), Vendor
**Starting State:** IR belum ada

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Requester membuat IR + satu IR Line (produk, `requested_qty`) | IR tersimpan state Draft |
| 2 | Requester submit | State → Submitted (atau langsung In Process jika NEED self-skip) |
| 3 | NEED Approver menyetujui kuantitas penuh | `approved_qty = requested_qty`; state → In Process |
| 4 | Reviewer set Fulfillment Allocation route `purchase` | Allocation tercatat, `purchase_allocated_qty` terisi |
| 5 | Procurement buat RFQ / Purchase Order ke vendor, lakukan Purchase Attribution | PO Line terkait, Attribution `state=active` |
| 6 | Procurement confirm PO | Jika nilai PO melewati FINANCIAL threshold, PO Commitment Cycle muncul dan wajib disetujui FINANCIAL Approver dulu sebelum PO benar-benar confirmed |
| 7 | Terima barang (validate picking penerimaan) | `purchase_delivered_qty` terisi sesuai qty diterima |
| 8 | Receiver lakukan Acceptance atas qty yang diterima | Acceptance Event tercatat |

**Expected Business Result:** IR selesai sebagai pemenuhan pembelian
tunggal, tanpa intervensi manual pada state.
**Expected State:** In Process → **Done**
**Expected Quantity Result:** `delivered_qty = accepted_qty = approved_qty`, `outstanding_qty = 0`
**Expected Approval Result:** NEED Cycle `approved`; FINANCIAL PO Commitment Cycle `approved` jika threshold terlampaui, atau tidak muncul sama sekali jika di bawah threshold
**Negative/Security Expectation:** N/A
**Evidence Required:** Nomor IR, state akhir, nomor PO, nomor Stock Picking penerimaan, ID Purchase Attribution, ID Acceptance Event, screenshot IR form

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-GS-02 — Simple Internal Transfer

**Business Process:** Internal Fulfillment
**Business Objective:** Membuktikan alur permintaan yang dipenuhi dari
stok internal (bukan pembelian) berjalan benar.
**Business Actor:** Requester, NEED Approver, Reviewer, Inventory, Receiver
**Preconditions/Required Master Data:** Company A, Warehouse+picking type
`internal`, NEED Policy, Governance Policy
**Starting State:** IR belum ada

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1-3 | Sama seperti UAT-GS-01 langkah 1-3 | Sama |
| 4 | Reviewer set Fulfillment Allocation route `internal` | `internal_allocated_qty` terisi |
| 5 | Inventory eksekusi Internal Transfer (stock picking internal) | Stock Attribution tercatat `state=active`, picking divalidasi |
| 6 | Receiver lakukan Acceptance | Acceptance Event tercatat |

**Expected Business Result:** IR selesai sebagai pemenuhan internal murni,
tanpa dokumen pembelian sama sekali.
**Expected State:** In Process → **Done**
**Expected Quantity Result:** `delivered_qty = internal_delivered_qty = accepted_qty = approved_qty`, `outstanding_qty = 0`
**Expected Approval Result:** NEED Cycle `approved`; tidak ada FINANCIAL Cycle sama sekali (tidak ada PO)
**Negative/Security Expectation:** N/A
**Evidence Required:** Nomor IR, state akhir, nomor Stock Picking, ID Stock Attribution, ID Acceptance Event

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-GS-03 — Split Internal + Purchase

**Business Process:** Fulfillment Gabungan
**Business Objective:** Membuktikan satu IR Line bisa dipenuhi sebagian
dari stok internal dan sebagian dari pembelian, keduanya terakumulasi
benar ke satu `delivered_qty` kanonik.
**Business Actor:** Requester, NEED Approver, Reviewer, Procurement, Inventory, Receiver
**Preconditions:** Gabungan UAT-GS-01 + UAT-GS-02
**Starting State:** IR belum ada

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1-3 | Submit + NEED approve qty penuh | Sama seperti sebelumnya |
| 4 | Reviewer split Allocation: sebagian `internal`, sebagian `purchase` | Kedua `internal_allocated_qty` dan `purchase_allocated_qty` terisi, jumlah keduanya = `approved_qty` |
| 5 | Eksekusi rute internal (Inventory) DAN rute purchase (Procurement, termasuk penerimaan barang) | Kedua Attribution tercatat aktif |
| 6 | Receiver Acceptance atas total qty gabungan | Satu Acceptance Event mencakup total dari kedua rute |

**Expected Business Result:** Kedua rute pemenuhan tercermin benar dalam
satu dokumen IR tanpa duplikasi maupun kehilangan kuantitas.
**Expected State:** In Process → **Done**
**Expected Quantity Result:** `delivered_qty = internal_delivered_qty + purchase_delivered_qty`, sama dengan `approved_qty`; `accepted_qty = delivered_qty`; `outstanding_qty = 0`
**Expected Approval Result:** NEED Cycle `approved`; FINANCIAL PO Commitment Cycle sesuai nilai PO porsi purchase jika melewati threshold
**Negative/Security Expectation:** N/A
**Evidence Required:** Nomor IR, kedua dokumen eksekusi (Picking + PO), kedua ID Attribution, ID Acceptance Event

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-GS-04 — Unknown Price → Sourcing → FINANCIAL

**Business Process:** Pengadaan dengan Harga Belum Diketahui
**Business Objective:** Membuktikan IR bisa disubmit tanpa harga
tervalidasi (Price Validation) dan harga sesungguhnya baru ditemukan
lewat proses RFQ/Sourcing, lalu FINANCIAL Approval dievaluasi terhadap
nilai PO **sesungguhnya**, bukan harga perkiraan mana pun.
**Business Actor:** Requester, NEED Approver, Procurement, FINANCIAL Approver
**Preconditions:** FINANCIAL Policy dua tier (lihat checklist §5)
**Starting State:** IR Line tanpa Price Validation apa pun

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1-3 | Submit + NEED approve, **tanpa** melakukan Price Validation | Berhasil — Price Validation memang opsional |
| 4 | Procurement buat RFQ dengan harga vendor sesungguhnya (`price_unit`) | Purchase Sourcing `state=candidate`, harga tercatat di PO Line |
| 5 | Procurement Award sourcing tersebut | Purchase Attribution efektif dibuat |
| 6 | Procurement confirm PO | FINANCIAL PO Commitment Cycle dievaluasi terhadap nilai PO **sesungguhnya** (qty × harga RFQ), bukan estimasi apa pun |

**Expected Business Result:** Harga yang sebelumnya tidak diketahui kini
menjadi dasar otorisasi finansial yang benar dan dapat diaudit.
**Expected State:** tetap In Process (belum tentu selesai di kasus ini,
fokus pada FINANCIAL, bukan completion)
**Expected Quantity Result:** N/A untuk kasus ini (fokus nilai finansial)
**Expected Approval Result:** FINANCIAL Cycle amount = qty × harga RFQ
persis; tier yang terpilih sesuai nilai tersebut
**Negative/Security Expectation:** N/A
**Evidence Required:** Purchase Sourcing record, nilai FINANCIAL Cycle
(`amount`), rule/tier yang terpilih

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-GS-05 — Many IR → One PO

**Business Process:** Konsolidasi Pembelian
**Business Objective:** Membuktikan dua Internal Request berbeda bisa
dikonsolidasikan ke satu baris Purchase Order yang sama, masing-masing
tetap terlacak terpisah.
**Business Actor:** 2× Requester, Procurement
**Preconditions:** Dua IR aktif, produk & vendor sama
**Starting State:** Dua IR Line sudah NEED-approved, belum ada Attribution

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Procurement buat RFQ dari IR A | Purchase Sourcing candidate untuk IR A |
| 2 | Procurement tambahkan IR B ke RFQ draft yang sama (bukan RFQ baru) | Satu PO Line teragregasi, `product_qty` bertambah sesuai qty IR B |
| 3 | Award kedua sourcing (IR A dan IR B) | Dua Purchase Attribution aktif, masing-masing terhubung ke IR-nya sendiri |
| 4 | Terima barang untuk PO Line gabungan tersebut | Distribusi `purchase_delivered_qty` proporsional ke masing-masing IR |

**Expected Business Result:** Efisiensi pembelian (satu PO) tanpa
kehilangan keterlacakan per-IR.
**Expected State:** N/A (fokus pada Attribution, bukan completion)
**Expected Quantity Result:** Jumlah `attributed_qty` kedua Attribution =
`product_qty` PO Line; masing-masing `purchase_delivered_qty` sesuai
proporsi
**Expected Approval Result:** N/A untuk kasus ini
**Negative/Security Expectation:** N/A
**Evidence Required:** Satu nomor PO Line, dua ID Purchase Attribution,
dua nomor IR

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-GS-06 — One IR → Multiple Vendors

**Business Process:** Split Award Multi-Vendor
**Business Objective:** Membuktikan satu kebutuhan bisa dipecah dan
dipenuhi dari lebih dari satu vendor sekaligus.
**Business Actor:** Requester, Procurement
**Preconditions:** Minimal dua vendor terdaftar
**Starting State:** Satu IR Line NEED-approved

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Procurement buat RFQ ke Vendor A untuk sebagian qty | Sourcing candidate A |
| 2 | Procurement buat RFQ ke Vendor B untuk sebagian qty lain | Sourcing candidate B |
| 3 | Award sebagian ke A, sebagian ke B (split award) | Dua Purchase Attribution aktif pada satu IR Line yang sama |

**Expected Business Result:** Fleksibilitas sumber pengadaan tanpa
mengorbankan integritas kuantitas.
**Expected State:** N/A
**Expected Quantity Result:** Jumlah kedua `attributed_qty` = total qty
yang di-award, tidak melebihi `purchase_allocated_qty`
**Expected Approval Result:** N/A
**Negative/Security Expectation:** N/A
**Evidence Required:** Dua PO Line (vendor berbeda), dua ID Purchase Attribution

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-GS-07 — Partial Receipt / Acceptance

**Business Process:** Penerimaan Bertahap
**Business Objective:** Membuktikan penerimaan/Acceptance bertahap tidak
menyelesaikan IR sebelum seluruh kuantitas benar-benar diterima.
**Business Actor:** Requester, Reviewer, Receiver
**Preconditions:** IR siap dieksekusi (rute apa saja)
**Starting State:** `approved_qty` = 10, belum ada pengiriman

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Kirim/eksekusi 6 dari 10 unit | `delivered_qty = 6` |
| 2 | Receiver Accept 6 unit | `accepted_qty = 6`; **IR TETAP In Process** |
| 3 | Kirim sisa 4 unit | `delivered_qty = 10` |
| 4 | Receiver Accept 4 unit sisanya | `accepted_qty = 10` |

**Expected Business Result:** Sistem tidak pernah menganggap selesai
hanya karena sebagian sudah diterima.
**Expected State:** In Process (setelah step 2) → **Done** (setelah step 4)
**Expected Quantity Result:** `outstanding_qty = 4` setelah step 2, `= 0` setelah step 4
**Expected Approval Result:** N/A (NEED sudah selesai sebelumnya)
**Negative/Security Expectation:** N/A
**Evidence Required:** Dua Acceptance Event terpisah, state IR pada setiap tahap

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-GS-08 — Exception → Replacement

**Business Process:** Penanganan Fulfillment Exception
**Business Objective:** Membuktikan barang bermasalah bisa dilaporkan dan
diselesaikan lewat penggantian (replacement) tanpa menciptakan kebutuhan
ganda.
**Business Actor:** Receiver
**Preconditions:** Ada `delivered_qty` yang belum di-Accept
**Starting State:** `delivered_qty` = qty penuh, `accepted_qty` = 0

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Receiver Report Fulfillment Exception (mis. jenis "damaged") atas qty bermasalah | Fulfillment Exception `status=open` |
| 2 | Receiver Resolve Exception dengan `resolution_type=replacement` | Exception `status=resolved`; **tidak** ada perubahan pada `approved_qty` — DEC-034 (Replacement Must Not Double Count) |
| 3 | Vendor/gudang kirim ulang barang pengganti | `delivered_qty` mencerminkan pengiriman baru (gross/historis, tidak menghapus catatan lama) |
| 4 | Receiver Accept barang pengganti | Acceptance Event tercatat |

**Expected Business Result:** Tidak ada duplikasi otorisasi kebutuhan
akibat proses klaim/replacement.
**Expected State:** In Process → **Done** (setelah Accept final)
**Expected Quantity Result:** `approved_qty` **tidak berubah** sepanjang
proses; `accepted_qty` akhirnya = `approved_qty`
**Expected Approval Result:** N/A
**Negative/Security Expectation:** N/A
**Evidence Required:** Fulfillment Exception (open→resolved,
`resolution_type`), Acceptance Event final

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-GS-09 — Partial Cancellation

**Business Process:** Pembatalan Sebagian
**Business Objective:** Membuktikan sisa kebutuhan yang tidak lagi
diperlukan bisa dibatalkan sebagian tanpa mempengaruhi bagian yang sudah
diterima.
**Business Actor:** Receiver, Cancellation Authority
**Preconditions:** Sebagian qty sudah di-Accept
**Starting State:** `approved_qty` = 10, `accepted_qty` = 6

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Cancellation Authority Cancel sisa 4 unit dengan alasan wajib diisi | Cancellation Event tercatat, `cancelled_qty = 4` |

**Expected Business Result:** Sisa kebutuhan yang memang tidak
dibutuhkan lagi tertutup secara resmi dan auditable.
**Expected State:** **Done** (mixed accepted+cancelled — bukan Cancelled,
karena ada `accepted_qty > 0`)
**Expected Quantity Result:** `outstanding_qty = 10 - 6 - 4 = 0`
**Expected Approval Result:** N/A
**Negative/Security Expectation:** Percobaan Cancel oleh user tanpa
`IR_CANCELLATION_AUTHORITY` yang resolve ke dirinya harus ditolak
**Evidence Required:** Cancellation Event, state IR akhir = Done

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-GS-10 — Material Revision → NEED Reapproval

**Business Process:** Revisi Material Pasca-Submit
**Business Objective:** Membuktikan perubahan kuantitas material setelah
submit hanya bisa lewat Revision resmi, dan jika menambah kuantitas,
memerlukan NEED reapproval sebelum efektif — bahkan bisa mengaktifkan
kembali IR yang sudah Done.
**Business Actor:** Revision Authority, NEED Approver
**Preconditions:** IR sudah Done (qty penuh diterima)
**Starting State:** State = Done, `approved_qty = accepted_qty = 10`

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Revision Authority Propose Revision, qty baru = 15 | Revision `status=active`; jika qty baru > `approved_qty` lama, NEED reapproval cycle otomatis dibuat |
| 2 | NEED Approver menyetujui reapproval (qty 15) | Cycle reapproval `approved` |
| 3 | Revision Authority Apply Revision | `approved_qty` menjadi 15 |

**Expected Business Result:** Penambahan kebutuhan pasca-selesai tercatat
resmi dan otomatis membuka kembali proses pemenuhan — bukan lewat tombol
"buka kembali" apa pun.
**Expected State:** **Done → In Process** (Controlled Reactivation)
**Expected Quantity Result:** `approved_qty = 15`, `outstanding_qty = 15 - 10 - 0 = 5`
**Expected Approval Result:** NEED reapproval Cycle terpisah, `approved`
**Negative/Security Expectation:** Percobaan Apply Revision sebelum
reapproval selesai harus ditolak
**Evidence Required:** Revision record (`status=applied`), Approval
Cycle reapproval, state IR sebelum/sesudah

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-GS-11 — Delegated Approval

**Business Process:** Pelimpahan Wewenang Persetujuan
**Business Objective:** Membuktikan approver yang berhalangan bisa
melimpahkan wewenang sementara, dengan identitas asli dan aktor
sebenarnya tetap tercatat terpisah.
**Business Actor:** Delegate, (NEED Approver yang berhalangan)
**Preconditions:** Delegation aktif mencakup tanggal eksekusi (lihat
checklist §8)
**Starting State:** IR Submitted, menunggu NEED Approval

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Delegate (bukan resolved approver asli) menyetujui NEED step | Berhasil — sistem mengizinkan berdasarkan Delegation aktif |

**Expected Business Result:** Proses bisnis tidak terhenti karena
approver berhalangan, namun akuntabilitas tetap terjaga.
**Expected State:** Submitted → In Process
**Expected Quantity Result:** N/A
**Expected Approval Result:** Approval Cycle Step: `resolved_user_id` =
approver asli, actor pada Step Line = Delegate — **keduanya tercatat
terpisah, tidak digabung**
**Negative/Security Expectation:** Delegate mencoba approve **di luar**
rentang tanggal Delegation harus ditolak (lihat UAT-SEC-07)
**Evidence Required:** Approval Cycle Step Line menunjukkan
`resolved_user_id` ≠ `actor_user_id`

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-GS-12 — Cross-company Violation Blocked

Lihat **UAT-SEC-02** (dipindah ke Bagian E agar tidak duplikat — isi
kasus identik, ID keamanan menjadi rujukan utama).

---

### UAT-GS-13 — Self-approval Skip

Lihat **UAT-SEC-08** (dipindah ke Bagian E — isi kasus identik).

---

### UAT-GS-14 — Merged PO Higher Commitment Authority

**Business Process:** Konsolidasi Melewati Ambang Finansial
**Business Objective:** Membuktikan penggabungan dua IR bernilai kecil ke
satu PO bisa melewati ambang (threshold) FINANCIAL yang lebih tinggi
daripada yang dibutuhkan masing-masing IR sendirian — dan sistem
mewajibkan approver tier tinggi, bukan tier rendah.
**Business Actor:** 2× Requester, Procurement, FINANCIAL Approver Low
tier, FINANCIAL Approver High tier
**Preconditions:** FINANCIAL Policy dua tier (checklist §5), contoh split
di 10.000
**Starting State:** Dua IR terpisah, masing-masing nilai purchase di
bawah 10.000, gabungan di atas 10.000

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Konsolidasikan kedua IR ke satu PO Line (seperti UAT-GS-05) | Nilai PO Line gabungan melewati 10.000 |
| 2 | Procurement confirm PO | PO Commitment Cycle dibuat dengan `amount` = nilai gabungan, rule yang terpilih = **tier tinggi** |
| 3 | FINANCIAL Approver Low tier mencoba approve | **Harus ditolak** — bukan resolved approver untuk tier ini |
| 4 | FINANCIAL Approver High tier approve | Berhasil, PO confirm |

**Expected Business Result:** Tidak ada celah untuk menghindari otorisasi
tier tinggi lewat konsolidasi administratif.
**Expected State:** N/A (fokus FINANCIAL, bukan completion IR)
**Expected Quantity Result:** N/A
**Expected Approval Result:** FINANCIAL Cycle `amount` = jumlah kedua
kontribusi persis; `rule_id` = rule tier tinggi
**Negative/Security Expectation:** Approver tier rendah ditolak pada step 3
**Evidence Required:** FINANCIAL Cycle detail (amount, rule/tier),
percobaan gagal approver tier rendah

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

# BAGIAN B — Approval Validation

### UAT-AP-01 — NEED Approval

**Business Process:** NEED Approval
**Business Objective:** Membuktikan NEED Approval murni mengotorisasi
KUANTITAS, terpisah total dari pertimbangan finansial apa pun.
**Business Actor:** NEED Approver
**Preconditions:** IR Submitted
**Starting State:** `approved_qty = 0`

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | NEED Approver setujui dengan kuantitas **dikurangi** dari yang diminta | `approved_qty` = kuantitas yang disetujui approver, BUKAN `requested_qty` |
| 2 | (Ulangi di IR terpisah) NEED Approver tolak | Cycle `rejected`; IR tetap Submitted (tidak otomatis kembali ke Draft) |

**Expected Business Result:** Kuantitas yang sah adalah keputusan
eksplisit approver, bukan default dari permintaan.
**Expected State:** Submitted → In Process (approve) / tetap Submitted (reject)
**Expected Quantity Result:** `approved_qty` ≤ `requested_qty`, sesuai keputusan
**Expected Approval Result:** NEED Cycle `approved`/`rejected`
**Negative/Security Expectation:** N/A
**Evidence Required:** NEED Approval Cycle, `approved_qty` pada IR Line

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-AP-02 — FINANCIAL Approval Tier-1

**Business Process:** FINANCIAL Approval (level IR)
**Business Objective:** Membuktikan otorisasi finansial di level IR
(berdasarkan Validated Price) berjalan independen dari proses PO apa pun.
**Business Actor:** Financial Officer (meminta), FINANCIAL Approver
**Preconditions:** Price Validation sudah dilakukan pada IR Line
**Starting State:** Belum ada FINANCIAL Cycle Tier-1

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Financial Officer minta FINANCIAL Authorization (Tier-1) | FINANCIAL Cycle dibuat, `amount` dihitung dari Validated Price × `approved_qty` |
| 2 | FINANCIAL Approver setujui | Cycle `approved` |

**Expected Business Result:** Eksposur finansial diotorisasi sebelum ada
komitmen pembelian apa pun.
**Expected State:** N/A (tidak mempengaruhi state IR)
**Expected Quantity Result:** N/A
**Expected Approval Result:** FINANCIAL Cycle Tier-1 `approved`, `amount`
sesuai basis Validated Price
**Negative/Security Expectation:** Tanpa `group_light_financial_officer`,
permintaan authorization ditolak
**Evidence Required:** FINANCIAL Cycle Tier-1 detail

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-AP-03 — FINANCIAL PO Commitment Authority (Tier-2)

**Business Process:** FINANCIAL Approval (level Purchase Order)
**Business Objective:** Membuktikan otorisasi finansial di level PO
dievaluasi terhadap nilai PO **sesungguhnya**, independen dan terpisah
dari Tier-1, dan dievaluasi ulang setiap kali PO coba di-confirm.
**Business Actor:** Procurement, FINANCIAL Approver
**Preconditions:** PO dengan Purchase Attribution aktif
**Starting State:** PO draft, belum confirmed

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Procurement confirm PO tanpa Tier-1 pernah dijalankan sama sekali | PO Commitment Cycle (Tier-2) tetap dibuat dan dievaluasi — **tidak bergantung** pada Tier-1 |
| 2 | Ulangi percobaan confirm sebelum Cycle disetujui | Ditolak lagi, **tidak** membuat Cycle duplikat (idempotent) |
| 3 | FINANCIAL Approver setujui Cycle | PO berhasil confirm |

**Expected Business Result:** Tidak ada PO LIGHT-linked yang bisa
confirm tanpa otorisasi finansial berbasis nilai sesungguhnya.
**Expected State:** N/A
**Expected Quantity Result:** N/A
**Expected Approval Result:** Tepat satu FINANCIAL Cycle Tier-2 per PO,
`amount` = nilai PO sesungguhnya (bukan Validated Price)
**Negative/Security Expectation:** PO non-LIGHT (tanpa Attribution)
confirm normal tanpa Cycle sama sekali (lihat juga regresi non-LIGHT)
**Evidence Required:** FINANCIAL Cycle Tier-2, jumlah Cycle tetap satu
setelah percobaan berulang

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

# BAGIAN C — Role / Capability Validation

### UAT-RL-01 — Reviewer

**Business Process:** Penyusunan Fulfillment Plan
**Business Objective:** Membuktikan hanya Reviewer yang bisa menyusun
Fulfillment Allocation.
**Business Actor:** Reviewer
**Preconditions:** IR In Process, `need_approval_status=approved`
**Starting State:** Belum ada Allocation

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Reviewer set Allocation (`internal`/`purchase`) | Berhasil |
| 2 | Reviewer `action_review()` | `review_status=reviewed`, tercatat siapa & kapan |

**Expected Business Result:** Perencanaan pemenuhan hanya bisa dilakukan
aktor yang berwenang.
**Expected State:** N/A
**Expected Quantity Result:** Allocation sesuai input, ≤ `approved_qty`
**Expected Approval Result:** N/A
**Negative/Security Expectation:** User tanpa `group_light_reviewer`
ditolak pada step 1 dan 2
**Evidence Required:** Fulfillment Allocation record, `review_status`, `reviewer_user_id`

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-RL-02 — Plan Approver

**Business Process:** Persetujuan Fulfillment Plan
**Business Objective:** Membuktikan Plan Approver adalah kapabilitas
TERPISAH dari Reviewer — memegang satu tidak memberi yang lain.
**Business Actor:** Plan Approver
**Preconditions:** Fulfillment Allocation sudah disusun
**Starting State:** `plan_approval_status=draft`

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | User yang HANYA punya `group_light_reviewer` (bukan Plan Approver) coba `action_approve_plan()` | **Ditolak** |
| 2 | Plan Approver approve | `plan_approval_status=approved` |
| 3 | Plan Approver reject (di IR terpisah) | `plan_approval_status=rejected`, Allocation tetap bisa disesuaikan Reviewer |

**Expected Business Result:** Pemisahan wewenang penyusunan vs
persetujuan rencana benar-benar berjalan.
**Expected State:** N/A
**Expected Quantity Result:** N/A
**Expected Approval Result:** N/A
**Negative/Security Expectation:** Step 1 wajib gagal
**Evidence Required:** `plan_approval_status`, `plan_approver_user_id`

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-RL-03 — Receiver

**Business Process:** Acceptance & Fulfillment Exception
**Business Objective:** Membuktikan hanya identitas Receiver efektif
(`receiver_user_id`, fallback Requester) dengan capability yang bisa
Accept/Reverse/Report/Resolve.
**Business Actor:** Receiver
**Preconditions:** `delivered_qty > 0`
**Starting State:** `accepted_qty = 0`

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | User dengan `group_light_receiver` TAPI bukan `receiver_user_id`/Requester IR ini, coba Accept | **Ditolak** |
| 2 | Receiver yang benar Accept | Berhasil |

**Expected Business Result:** Kapabilitas saja tidak cukup — identitas
juga diperiksa.
**Expected State:** N/A
**Expected Quantity Result:** `accepted_qty` sesuai qty yang di-Accept
**Expected Approval Result:** N/A
**Negative/Security Expectation:** Step 1 wajib gagal
**Evidence Required:** Acceptance Event, `actor_user_id`

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-RL-04 — Cancellation Authority

**Business Process:** Cancellation
**Business Objective:** Membuktikan Cancellation memerlukan capability
DAN resolusi Responsibility (`IR_CANCELLATION_AUTHORITY`) — bukan cukup
salah satu saja.
**Business Actor:** Cancellation Authority
**Preconditions:** `outstanding_qty > 0`

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | User dengan `group_light_canceller` tapi BUKAN user yang di-resolve `IR_CANCELLATION_AUTHORITY` untuk Company ini, coba Cancel | **Ditolak** |
| 2 | User yang benar-benar di-resolve, Cancel | Berhasil |

**Expected Business Result:** Wewenang pembatalan benar-benar personal,
tidak cukup punya group saja.
**Expected State:** N/A
**Expected Quantity Result:** `cancelled_qty` bertambah sesuai
**Expected Approval Result:** N/A
**Negative/Security Expectation:** Step 1 wajib gagal
**Evidence Required:** Cancellation Event, `actor_user_id`

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-RL-05 — Revision Authority

**Business Process:** Revision
**Business Objective:** Sama seperti UAT-RL-04 namun untuk
`IR_REVISION_AUTHORITY`, sekaligus membuktikan Revision bisa diajukan
pada IR Draft-tidak-boleh (harus sudah Submitted minimal), termasuk pada
IR Done/Cancelled (lihat UAT-CP-09).
**Business Actor:** Revision Authority
**Preconditions:** IR minimal Submitted

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | User tanpa resolusi `IR_REVISION_AUTHORITY` yang benar coba Propose Revision | **Ditolak** |
| 2 | Revision Authority yang benar, Propose Revision pada IR **Draft** | **Ditolak** — Draft bukan target Revision yang sah (perubahan Draft = edit biasa) |
| 3 | Revision Authority Propose Revision pada IR Submitted/In Process | Berhasil |

**Expected Business Result:** Wewenang dan target state Revision
konsisten dengan desain V1.
**Expected State:** N/A
**Expected Quantity Result:** N/A
**Expected Approval Result:** N/A
**Negative/Security Expectation:** Step 1 dan 2 wajib gagal
**Evidence Required:** Revision record atau pesan penolakan

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-RL-06 — Procurement

**Business Process:** Sourcing & Purchase Attribution
**Business Objective:** Membuktikan Procurement butuh KEDUA capability:
LIGHT Procurement DAN hak Purchase standar Odoo.
**Business Actor:** Procurement
**Preconditions:** Allocation route `purchase` sudah ada

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | User dengan `group_light_procurement` saja (tanpa `purchase.group_purchase_user`) coba buat RFQ | **Ditolak oleh ACL standar Odoo Purchase**, bukan LIGHT |
| 2 | Procurement dengan kedua hak, buat RFQ dan Attribution | Berhasil |

**Expected Business Result:** LIGHT tidak memberi jalan pintas melewati
hak akses standar Odoo Purchase.
**Expected State:** N/A
**Expected Quantity Result:** N/A
**Expected Approval Result:** N/A
**Negative/Security Expectation:** Step 1 wajib gagal
**Evidence Required:** Pesan penolakan step 1, RFQ berhasil step 2

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-RL-07 — Inventory

**Business Process:** Stock Attribution & Internal Transfer
**Business Objective:** Sama seperti UAT-RL-06, untuk `group_light_inventory` + `stock.group_stock_user`.
**Business Actor:** Inventory
**Preconditions:** Allocation route `internal` sudah ada

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | User dengan `group_light_inventory` saja coba eksekusi Internal Transfer | **Ditolak oleh ACL standar Odoo Stock** |
| 2 | Inventory dengan kedua hak, eksekusi transfer | Berhasil |

**Expected Business Result:** Sama seperti UAT-RL-06, untuk domain Stock.
**Expected State:** N/A **Expected Quantity Result:** N/A **Expected Approval Result:** N/A
**Negative/Security Expectation:** Step 1 wajib gagal
**Evidence Required:** Pesan penolakan step 1, picking berhasil step 2

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-RL-08 — Administrator

Lihat **UAT-SEC-04** (Administrator is not automatic approver) di
Bagian E untuk validasi negatif utamanya. Tambahan validasi positif:

**Business Objective tambahan:** Membuktikan Administrator BISA mengelola
seluruh menu Configuration.

| Step | User Action | Expected System Behaviour |
|---|---|---|
| 1 | Administrator buka & ubah Request Type, kedua model Policy, Rule/Rule Step, Responsibility Assignment, Delegation, HR Responsibility Rule | Seluruhnya berhasil dibaca/diubah |

**Evidence Required:** Perubahan tersimpan di masing-masing model konfigurasi

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-RL-09 — Auditor

Lihat **UAT-SEC-03** (Auditor read-only boundary) di Bagian E untuk
kasus lengkap.

---

# BAGIAN D — Completion Behavior (M20)

### UAT-CP-01 — Accepted-only completion → Done

**Business Actor:** Receiver
**Starting State:** `approved_qty=10, accepted_qty=0, cancelled_qty=0`
**Step:** Accept 10 unit penuh
**Expected State:** **Done**
**Expected Quantity Result:** `outstanding_qty=0`, `accepted_qty=10 > 0`
**Evidence Required:** State IR, Acceptance Event

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-CP-02 — Accepted + cancelled completion → Done

**Business Actor:** Receiver, Cancellation Authority
**Starting State:** `approved_qty=10`
**Steps:** Accept 6, Cancel 4
**Expected State:** **Done** (bukan Cancelled — TR-03: kombinasi
accepted+cancelled tetap "the quantity condition for completion", jalur
Done biasa)
**Expected Quantity Result:** `outstanding_qty = 10-6-4 = 0`
**Evidence Required:** Acceptance Event + Cancellation Event, state=Done

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-CP-03 — Cancellation-only completion → Cancelled

**Business Actor:** Cancellation Authority
**Starting State:** `approved_qty=10, accepted_qty=0`
**Step:** Cancel 10 (seluruhnya)
**Expected State:** **Cancelled** (bukan Done — tidak ada `accepted_qty>0` sama sekali)
**Expected Quantity Result:** `outstanding_qty=0`
**Evidence Required:** Cancellation Event, state=Cancelled

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-CP-04 — Outstanding quantity > 0 → In Process

**Business Actor:** Receiver
**Starting State:** `approved_qty=10`
**Step:** Accept hanya 6
**Expected State:** **In Process** (tetap, tidak selesai)
**Expected Quantity Result:** `outstanding_qty=4`
**Evidence Required:** State tetap In Process meski sudah ada Acceptance

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-CP-05 — Open Fulfillment Exception blocks completion

**Business Actor:** Receiver, Cancellation Authority
**Starting State:** `outstanding_qty` sudah 0 lewat Accept+Cancel yang seimbang
**Step:** Report Fulfillment Exception (status tetap `open`, jangan diselesaikan)
**Expected State:** **In Process** — meskipun `outstanding_qty=0`, Exception terbuka tetap memblokir
**Evidence Required:** Fulfillment Exception `status=open`, state IR bukan Done

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-CP-06 — Pending Acceptance blocks completion

**Business Actor:** Receiver
**Starting State:** `delivered_qty > accepted_qty` (masih ada yang terkirim tapi belum di-Accept)
**Step:** Pastikan tidak ada Accept lebih lanjut meski `outstanding_qty` sudah 0 lewat kombinasi lain
**Expected State:** **In Process** — pending Acceptance memblokir walau outstanding sudah 0
**Evidence Required:** `delivered_qty` vs `accepted_qty`, state bukan Done

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-CP-07 — Acceptance Reversal can reactivate terminal IR

**Business Actor:** Receiver
**Starting State:** IR **Done** (accepted penuh)
**Step:** Reverse sebagian Acceptance
**Expected State:** **Done → In Process** (Controlled Reactivation)
**Evidence Required:** Acceptance Event `event_type=reversal`, state berubah

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-CP-08 — Report Exception can reactivate terminal IR

**Business Actor:** Receiver
**Starting State:** IR **Done**
**Step:** Report Fulfillment Exception baru pada baris yang sudah selesai
**Expected State:** **Done → In Process**
**Evidence Required:** Fulfillment Exception baru, state berubah

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-CP-09 — Approved Revision increase can reactivate terminal IR

Identik dengan **UAT-GS-10** — rujuk kasus tersebut sebagai bukti utama.

---

### UAT-CP-10 — No generic reopen action exists

**Business Actor:** Administrator, Plain user
**Starting State:** IR **Done** atau **Cancelled**
**Steps:**
1. Cari tombol/aksi "Reopen"/"Mark In Process" di mana pun pada IR — **tidak boleh ditemukan**.
2. Coba ubah field status langsung lewat cara apa pun yang tersedia bagi user (bukan developer/database) — **wajib ditolak sistem**.

**Expected Business Result:** Satu-satunya jalan kembali ke In Process
adalah lewat UAT-CP-07/08/09 — tidak ada jalan pintas administratif.
**Evidence Required:** Konfirmasi tidak ada tombol reopen; pesan
penolakan pada percobaan ubah status langsung

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

# BAGIAN E — Security Negative Tests

### UAT-SEC-01 — Unauthorized action

**Business Process:** Server-side Authorization
**Business Objective:** Membuktikan setiap aksi bisnis utama ditolak
server-side untuk user tanpa capability yang sesuai — bukan sekadar
tombolnya disembunyikan di UI.
**Business Actor:** Plain user (tanpa capability LIGHT apa pun, hanya
`base.group_user`)
**Preconditions:** Tersedia IR pada state yang membuat masing-masing aksi
di bawah "seharusnya bisa" bagi actor yang benar (mis. `delivered_qty>0`
untuk Acceptance, `outstanding_qty>0` untuk Cancellation, dll.)
**Required Master Data:** Checklist §12 (Plain user)
**Starting State:** Bervariasi per sub-check, lihat kolom Preconditions

**Instruksi penting:** jangan berhenti pada "tombol tidak terlihat".
Untuk setiap sub-check, upayakan memicu aksi lewat jalur yang tetap
dapat diakses user (menu/list/form/URL yang tersedia baginya). Bukti
yang sah adalah **pesan penolakan dari server**, bukan ketiadaan tombol.

**Sub-check matrix** — isi setiap baris:

| # | Aksi yang dicoba | Expected | Actual | Evidence | PASS/FAIL |
|---|---|---|---|---|---|
| 1 | **Acceptance** (Accept qty) | Ditolak — capability `group_light_receiver` tidak dimiliki | | Pesan penolakan; `accepted_qty` tidak berubah | |
| 2 | **Cancellation** (Cancel sisa qty) | Ditolak — capability `group_light_canceller` tidak dimiliki | | Pesan penolakan; `cancelled_qty` tidak berubah | |
| 3 | **Revision** (Propose Revision) | Ditolak — capability `group_light_revision_authority` tidak dimiliki | | Pesan penolakan; tidak ada Revision record terbentuk | |
| 4 | **Purchase Attribution** (attribute ke PO Line) | Ditolak — capability `group_light_procurement` tidak dimiliki | | Pesan penolakan; tidak ada Purchase Attribution terbentuk | |
| 5 | **Stock Attribution** (attribute ke stock move) | Ditolak — capability `group_light_inventory` tidak dimiliki | | Pesan penolakan; tidak ada Stock Attribution terbentuk | |
| 6 | **NEED Approval** (approve/reject NEED step) | Ditolak — bukan resolved approver hasil Responsibility Assignment, dan tidak ada Delegation yang berlaku | | Pesan penolakan; Approval Cycle Step tetap `waiting` | |
| 7 | **FINANCIAL Approval** (approve step Tier-1 atau Tier-2) | Ditolak — alasan sama seperti #6 | | Pesan penolakan; FINANCIAL Cycle Step tetap `waiting`; PO tetap belum confirmed (untuk Tier-2) | |
| 8 | **Plan Approval** (approve/reject Fulfillment Plan) | Ditolak — capability `group_light_plan_approver` tidak dimiliki | | Pesan penolakan; `plan_approval_status` tetap `draft` | |

**Expected Business Result:** Tidak ada satu pun aksi bisnis yang bisa
dieksekusi tanpa wewenang yang sah, terlepas dari apa yang terlihat atau
tidak terlihat di antarmuka.
**Expected State:** Tidak ada perubahan state IR akibat percobaan apa pun
di atas.
**Expected Quantity Result:** Seluruh kuantitas kanonik (`approved_qty`,
`delivered_qty`, `accepted_qty`, `cancelled_qty`, `outstanding_qty`)
**tidak berubah sama sekali** setelah seluruh sub-check dijalankan.
**Expected Approval Result:** Tidak ada Approval Cycle/Step yang berubah
status akibat percobaan di atas.
**Negative/Security Expectation:** Seluruh 8 sub-check **wajib**
menghasilkan penolakan. Satu saja yang berhasil = temuan keamanan
severity Critical.
**Evidence Required:** Delapan baris matriks di atas terisi lengkap
(Actual + Evidence + PASS/FAIL), disertai nomor IR yang dipakai dan
bukti bahwa kuantitas serta state tidak berubah setelah pengujian.

--- Execution Record ---
**Actual Result (ringkasan seluruh 8 sub-check):** ______________________
**PASS/FAIL (keseluruhan — FAIL jika ada satu saja sub-check FAIL):** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-SEC-02 — Cross-company access (= UAT-GS-12)

**Business Actor:** User Company B
**Starting State:** IR milik Company A sudah ada
**Steps:**
1. User Company B cari IR Company A di list — **tidak boleh muncul**.
2. Coba buka langsung (URL/ID) — **AccessError**.
3. Coba jalankan aksi bisnis apa pun atas IR tersebut — **UserError**, tidak ada kebocoran parsial.
**Evidence Required:** Bukti negatif ketiga langkah

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-SEC-03 — Auditor read-only boundary

**Business Actor:** Auditor
**Steps:**
1. Login sebagai Auditor, buka menu Audit (16 model) — semua bisa dibaca.
2. Pastikan menu Configuration **tidak muncul sama sekali**.
3. Coba create/write/unlink langsung (via list/form yang bisa diakses) pada salah satu dari 16 model — **ditolak**.
4. Coba jalankan aksi bisnis apa pun (Accept/Cancel/dll.) — **ditolak**.
**Expected Result:** Baca-saja murni, tanpa pengecualian.
**Evidence Required:** Screenshot menu (tanpa Configuration), pesan
penolakan step 3 dan 4

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-SEC-04 — Administrator is not automatic approver

**Business Actor:** Administrator (hanya `group_light_administrator`, TIDAK di-resolve sebagai approver mana pun)
**Starting State:** IR Submitted, PO menunggu commitment
**Steps:**
1. Administrator coba approve NEED step — **ditolak**.
2. Administrator coba approve FINANCIAL step (Tier-1 atau Tier-2) — **ditolak**.
3. Administrator coba Accept/Cancel — **ditolak** (tidak punya capability terkait).
**Expected Result:** Kapabilitas konfigurasi tidak pernah menjadi
wewenang transaksi (DEC-016).
**Evidence Required:** Pesan penolakan ketiga percobaan

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-SEC-05 — Invalid/missing Responsibility Assignment

**Business Actor:** NEED Approver yang seharusnya
**Starting State:** Rule Step mengacu ke `responsibility_code` yang
**belum** punya Responsibility Assignment aktif sama sekali
**Step:** Submit IR
**Expected Result:** Ditolak dengan pesan deterministik ("tidak
ditemukan"/"missing") — **tidak ada fallback diam-diam** ke approver
mana pun
**Evidence Required:** Pesan error persis

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-SEC-06 — Ambiguous Responsibility Assignment

**Business Actor:** NEED Approver
**Starting State:** DUA Responsibility Assignment aktif untuk
Company+`responsibility_code` yang SAMA, user berbeda
**Step:** Submit IR
**Expected Result:** Ditolak dengan pesan "ambiguous"/"tidak bisa
ditentukan" — sistem **tidak** memilih salah satu secara diam-diam
**Evidence Required:** Pesan error persis

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-SEC-07 — Invalid Delegation date

**Business Actor:** Delegate
**Starting State:** Delegation dengan `date_to` di masa lalu (sudah kedaluwarsa)
**Step:** Delegate coba approve NEED/FINANCIAL step di luar rentang tanggal
**Expected Result:** Ditolak — Delegation kedaluwarsa tidak memberi
wewenang apa pun
**Evidence Required:** Pesan penolakan, tanggal Delegation vs tanggal
percobaan

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

### UAT-SEC-08 — Self-approval behavior (= UAT-GS-13)

**Business Actor:** Requester yang juga resolved approver untuk dirinya sendiri
**Starting State:** `self_approval_skip=True` (default) pada Rule
**Steps:**
1. Submit — step otomatis **skipped**, bukan disetujui-sendiri secara eksplisit.
2. (Kasus kedua) Set `self_approval_skip=False` pada Rule, ulangi — step **tidak** di-skip, tetap `waiting`, memblokir sampai ada tindakan (bahkan dari diri sendiri, tapi bukan otomatis).
**Expected Result:** Perilaku self-approval sepenuhnya dikendalikan
konfigurasi, bukan hard-coded.
**Evidence Required:** Approval Cycle Step `status` pada kedua konfigurasi

--- Execution Record ---
**Actual Result:** ______________________ **PASS/FAIL:** ______
**Tester:** ______________ **Execution Date:** ______________
**Defect/Finding Reference:** ______________ **Business Comment:** ______________

---

# Ringkasan Jumlah Kasus

| Bagian | Kartu Kasus | Kasus Unik Dieksekusi |
|---|---|---|
| A. Golden Business Scenarios | 14 | **12** (UAT-GS-12, UAT-GS-13 = rujukan-silang) |
| B. Approval Validation | 3 | 3 |
| C. Role/Capability Validation | 9 | **8** (UAT-RL-09 = rujukan-silang) |
| D. Completion Behavior | 10 | **9** (UAT-CP-09 = rujukan-silang) |
| E. Security Negative Tests | 8 | 8 |
| **Total** | **44 kartu** | **40 kasus unik dieksekusi** |

**Cakupan Golden Scenario tetap 14/14** — lihat
`UAT_SIGNOFF_TEMPLATE.md` §1a untuk pemetaan bukti eksekusi bersama.

**Kartu rujukan-silang (tidak punya Execution Record sendiri, tidak
dieksekusi dua kali, tidak dihitung ganda):**

| Kartu | Bukti diambil dari |
|---|---|
| UAT-GS-12 — Cross-company violation blocked | UAT-SEC-02 |
| UAT-GS-13 — Self-approval skip | UAT-SEC-08 |
| UAT-CP-09 — Revision reactivates terminal IR | UAT-GS-10 |
| UAT-RL-09 — Auditor | UAT-SEC-03 |

Catatan: **UAT-RL-08 (Administrator) bukan** rujukan-silang penuh — ia
merujuk UAT-SEC-04 untuk sisi negatifnya, namun tetap memiliki langkah
validasi positif dan Execution Record sendiri, sehingga tetap dihitung
sebagai kasus unik yang dieksekusi.
