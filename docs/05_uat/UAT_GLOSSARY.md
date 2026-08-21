# LIGHT Internal Request V1 — Bilingual UAT Glossary

Istilah kanonik di kolom pertama **tidak boleh diterjemahkan** ke Bahasa
Indonesia dalam dokumentasi atau saat mencatat hasil UAT — gunakan
persis seperti tertulis. Kolom kedua adalah penjelasan bisnis dalam
Bahasa Indonesia untuk membantu pemahaman, bukan pengganti istilah.

## Konsep Inti

| Canonical Term | Business Indonesian Explanation | Notes |
|---|---|---|
| Internal Request (IR) | Dokumen permintaan internal — permintaan barang/jasa dari Requester | Model: `light.internal.request` |
| IR Line | Satu baris kebutuhan di dalam Internal Request (satu produk/jasa, satu kuantitas) | Model: `light.internal.request.line` |
| Requester | Pemohon — pembuat Internal Request | — |
| Receiver | Penerima barang/jasa secara bisnis, bisa berbeda dari Requester | `receiver_user_id`, fallback ke Requester jika kosong |

## NEED vs FINANCIAL — Perbedaan yang WAJIB Dipahami

| Canonical Term | Business Indonesian Explanation | Notes |
|---|---|---|
| **NEED Approval** | Otorisasi **kebutuhan bisnis** — berapa kuantitas yang sah disetujui untuk dipenuhi. Mengatur `approved_qty`. | Purpose Approval Cycle: `need` |
| **FINANCIAL Approval** | Otorisasi **eksposur finansial / pengeluaran** — apakah nilai uang yang terlibat sah untuk dikeluarkan. **Terpisah** dari NEED, meski memakai mesin (engine) Approval Cycle yang sama persis. | Purpose Approval Cycle: `financial`. Ada DUA tingkat: Tier-1 (di level IR, berdasarkan Validated Price) dan Tier-2 (di level Purchase Order, berdasarkan nilai PO sesungguhnya) |
| Approval Cycle | Satu siklus persetujuan (NEED atau FINANCIAL) — riwayat lengkap, tidak pernah diedit setelah selesai | Model: `light.ir.approval.cycle` |
| Approval Cycle Step | Satu tahap dalam Approval Cycle — punya `resolved_user_id` (siapa yang seharusnya menyetujui) | Model: `light.ir.approval.cycle.step` |
| resolved approver | User yang secara sistem ditentukan berwenang untuk suatu Approval Step, hasil resolusi Responsibility Assignment | Field: `resolved_user_id` |
| actual actor | User yang benar-benar menekan tombol approve/reject — bisa berbeda dari resolved approver jika lewat Delegation | Dicatat terpisah di Approval Cycle Step Line |
| self-approval skip | Jika resolved approver = Requester sendiri, tahap tersebut otomatis dilewati (skipped), bukan disetujui-sendiri | Field policy: `self_approval_skip` (default aktif) |

## Alur Bisnis / Business Event

| Canonical Term | Business Indonesian Explanation | Notes |
|---|---|---|
| Fulfillment Allocation | Rencana pemenuhan — internal, purchase, atau gabungan keduanya | Model: `light.ir.fulfillment.allocation` |
| Purchase Attribution | Keterkaitan formal antara Fulfillment Allocation dan satu baris Purchase Order sungguhan | Model: `light.ir.purchase.attribution`. Jangan disamakan dengan Purchase Sourcing (RFQ) |
| Purchase Sourcing | Kandidat sumber/vendor (RFQ) — belum tentu menjadi Purchase Attribution efektif | Model: `light.ir.purchase.sourcing` |
| Stock Attribution | Keterkaitan formal antara Fulfillment Allocation internal dan satu stock move sungguhan | Model: `light.ir.stock.attribution` |
| **Acceptance** | Pengakuan bisnis bahwa barang/jasa yang terkirim (`delivered_qty`) sudah diterima secara sah — bukan otomatis dari pengiriman | Model: `light.ir.acceptance.event`, `event_type='acceptance'`. **Bukan** Delivery |
| Acceptance Reversal | Koreksi atas Acceptance yang sudah tercatat — tetap event baru, riwayat asli tidak pernah dihapus/diedit | `event_type='reversal'` pada model yang sama |
| **Fulfillment Exception** | Catatan masalah pada pemenuhan (rusak, salah barang, kurang, dll.) — **bukan** Cancellation, tidak otomatis mengurangi kebutuhan yang sah | Model: `light.ir.fulfillment.exception` |
| **Cancellation** | Pembatalan resmi atas sisa kebutuhan yang sah (`outstanding_qty`) — peristiwa tunggal (atomic), bukan proses bertahap | Model: `light.ir.cancellation`. **Bukan** Fulfillment Exception |
| **Revision** | Perubahan material pasca-submit (kuantitas/produk/deskripsi) lewat mekanisme terkontrol — riwayat lama tetap tersimpan, tidak pernah ditimpa | Model: `light.ir.revision`, status `active` → `applied`/`rejected` |
| **Responsibility Assignment** | Penunjukan resmi: siapa yang memegang suatu kode tanggung jawab (mis. "Kepala Departemen") di Company tertentu, pada periode tertentu | Model: `light.ir.responsibility.assignment`. Dasar resolusi wewenang NEED/FINANCIAL/Cancellation/Revision |
| **Delegation** | Pelimpahan wewenang sementara dari satu resolved approver ke user lain, dalam rentang tanggal tertentu | Model: `light.ir.approval.delegation` |

## Kuantitas Kanonik — JANGAN DICAMPUR MAKNANYA

| Field | Business Indonesian Explanation |
|---|---|
| `requested_qty` | Kuantitas yang **diminta** Requester |
| `approved_qty` | Kuantitas yang **disahkan** lewat NEED Approval — bisa lebih kecil dari `requested_qty` |
| `delivered_qty` | Kuantitas yang **secara operasional sudah terkirim/tereksekusi** (internal, purchase, atau service) — **belum tentu** berarti diterima secara bisnis |
| `accepted_qty` | Kuantitas yang **diterima secara sah** lewat Acceptance, dikurangi Reversal |
| `cancelled_qty` | Kuantitas yang **dibatalkan** secara resmi lewat Cancellation |
| `outstanding_qty` | Sisa kebutuhan yang masih harus dipenuhi/diselesaikan — formula wajib di bawah |

```
outstanding_qty = approved_qty - accepted_qty - cancelled_qty
```

## Status / Lifecycle

| Canonical Term | Business Indonesian Explanation |
|---|---|
| Draft | Belum diajukan, masih bisa diubah bebas oleh Requester |
| Submitted | Sudah diajukan, menunggu NEED Approval selesai |
| In Process | NEED Approval sudah selesai, proses pemenuhan berjalan |
| **Done** | Selesai — seluruh baris sudah memenuhi syarat penyelesaian DAN ada kuantitas yang diterima (`accepted_qty > 0`) di suatu baris |
| **Cancelled** | Selesai sebagai pembatalan — seluruh baris memenuhi syarat penyelesaian TANPA ada `accepted_qty > 0` di baris mana pun |
| Completion | Proses otomatis (bukan tombol) yang menentukan Done/Cancelled — lihat `UAT_TEST_CASES.md` Bagian D |
| Controlled Reactivation | IR yang sudah Done/Cancelled bisa kembali ke In Process **hanya** sebagai efek samping dari Acceptance Reversal, Report Exception, atau Revision yang disetujui — tidak pernah lewat tombol "buka kembali" |

## Peran / Group

| Canonical Term | Business Indonesian Explanation |
|---|---|
| Administrator | Mengelola konfigurasi (Policy, Rule, Request Type, dll.) — **bukan** approver otomatis |
| Auditor | Hanya bisa membaca (read-only) data transaksi/riwayat — tidak bisa mengubah apa pun, tidak bisa mengakses konfigurasi |
