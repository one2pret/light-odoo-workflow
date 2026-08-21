# LIGHT Internal Request V1 — UAT Role Matrix

Baseline: `light-ir-m20`. Peran/GROUP aplikasi di bawah ini **berbeda**
dari peran BISNIS eksekusi UAT (lihat `UAT_MASTER_PLAN.md` §4).

**Prinsip yang wajib dipahami tester (DEC-016):** kapabilitas (group)
**bukan** wewenang transaksi. Memiliki group tertentu hanya membuka
kemungkinan mencoba sebuah aksi — untuk NEED dan FINANCIAL Approval,
wewenang sebenarnya baru ditentukan oleh **Responsibility Assignment**
(atau resolusi HR) terhadap identitas user yang login, bukan oleh group
apa pun. Ini sebabnya tidak ada group "NEED Approver" atau "FINANCIAL
Approver" di daftar bawah — keduanya murni resolusi Responsibility.

## Actor / Group / Capability / Responsibility

| Actor | Group Aplikasi (Addon) | Aksi yang Dibuka | Wewenang Sebenarnya Ditentukan Oleh |
|---|---|---|---|
| Requester | — (`base.group_user` saja) | Buat & Submit Internal Request milik sendiri | Kepemilikan (`requester_user_id` = user yang login), bukan group |
| NEED Approver | **Tidak ada group.** | — | `light.ir.responsibility.assignment._resolve()` (atau resolver HR `light_ir_hr`) terhadap `responsibility_code` yang dikonfigurasi di NEED Rule Step |
| FINANCIAL Approver (Tier-1 & Tier-2) | **Tidak ada group.** | — | Sama seperti di atas, terhadap `responsibility_code` di FINANCIAL Rule Step |
| Delegate | **Tidak ada group.** | Bertindak atas nama resolved approver pada NEED/FINANCIAL step | Baris **Delegation** aktif (`delegator_id` = resolved approver, `delegate_id` = user ini) yang **masih berlaku pada tanggal aksi**. Identitas asli (`resolved_user_id`) dan aktor sebenarnya tetap dicatat terpisah. Diuji di UAT-GS-11 (positif) dan UAT-SEC-07 (Delegation kedaluwarsa) |
| Financial Officer | `group_light_financial_officer` (`light_internal_request`) | Meminta FINANCIAL Authorization Tier-1 (`action_request_financial_authorization`) | Group ini hanya gerbang kasar untuk MEMINTA; approval sebenarnya tetap via resolusi Responsibility di atas |
| Price Validator | `group_light_price_validator` | Validasi harga satuan | Kapabilitas saja, tanpa pengecekan identitas per-transaksi |
| Reviewer | `group_light_reviewer` | `action_review()`, menyusun Fulfillment Allocation | Kapabilitas + Company |
| Plan Approver | `group_light_plan_approver` | Setuju/tolak Fulfillment Plan | Kapabilitas + Company. **Terpisah dari Reviewer** — memegang satu tidak memberi yang lain |
| Receiver | `group_light_receiver` | Acceptance, Reversal, Report/Resolve Fulfillment Exception | Kapabilitas + identitas efektif (`receiver_user_id`, fallback ke `requester_user_id`) |
| Cancellation Authority | `group_light_canceller` | Cancellation | Kapabilitas (gerbang kasar) + resolusi `IR_CANCELLATION_AUTHORITY`, user yang di-resolve harus = user yang login |
| Revision Authority | `group_light_revision_authority` | Propose/Apply Revision | Kapabilitas (gerbang kasar) + resolusi `IR_REVISION_AUTHORITY` |
| Procurement | `group_light_procurement` (`light_ir_purchase`) | Sourcing/RFQ, Purchase Attribution | Kapabilitas + Company; juga butuh `purchase.group_purchase_user` standar Odoo untuk dokumen PO/RFQ |
| Inventory | `group_light_inventory` (`light_ir_stock`) | Stock Attribution, Internal Transfer | Kapabilitas + Company; juga butuh `stock.group_stock_user` standar Odoo untuk picking |
| Administrator | `group_light_administrator` (`light_ir_approval`) | Seluruh menu Configuration (Request Type, kedua model Policy, Rule/Rule Step, Responsibility Assignment, Delegation, HR Responsibility Rule) | **Bukan** approver otomatis di mana pun — lihat UAT-SEC-04 |
| Auditor | `group_light_auditor` (`light_ir_approval`) | Baca-saja pada persis 16 model transaksi/riwayat | Tidak ada `implied_ids`, tidak ada create/write/unlink di mana pun, tidak ada akses konfigurasi/master, tidak ada wewenang aksi bisnis apa pun |

## Catatan Penting untuk Tester

- Satu user bisa memiliki lebih dari satu group sekaligus di dunia nyata
  (misal Reviewer + Plan Approver) — namun untuk UAT, **uji setiap
  kapabilitas dengan user yang HANYA punya group itu saja**, supaya hasil
  positif benar-benar membuktikan group tersebut yang bekerja, bukan
  kombinasi group lain yang kebetulan menutupi celah.
- Semua 11 group LIGHT bersifat *company-scoped* — user harus terdaftar
  di Company yang benar (`company_ids`), bukan hanya punya group-nya saja.
- Auditor tidak boleh melihat menu Configuration sama sekali (bukan cuma
  tombolnya disembunyikan — coba akses langsung via URL/list action untuk
  membuktikan ini benar-benar diblokir server-side, bukan cuma UI).
