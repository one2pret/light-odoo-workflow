Menurut saya, notes ini sangat penting karena justru di sinilah **aturan bisnis yang sebenarnya** mulai terlihat. Tabel approval-nya hanya menunjukkan “siapa approve siapa”, tetapi notes menjelaskan **kapan chain berubah, kapan step dilewati, dan bagaimana organisasi memperlakukan pengecualian**.

Saya akan bahas satu per satu dari sudut pandang fungsional dan implementasi LIGHT.

### 1. “Setting bisa di tiap divisi”

Ini menurut saya **masuk akal dan wajib didukung secara konfigurasi**, bukan hardcode.

Artinya Growth Marketing, PnC, BisPro, HR boleh memiliki chain berbeda. Secara desain yang sehat, jangan dibuat:

```text
if division == "Marketing":
   approver = Esty
```

tetapi dibuat sebagai konfigurasi:

```text
Division / Request Type
        ↓
Approval Policy
        ↓
Rule
        ↓
Responsibility
```

Jadi kalau Head Marketing ganti orang, tidak perlu ubah code.

**Kesimpulan:** valid, cocok dengan konsep configuration-driven approval.

---

### 2. “Ada jenis pengeluaran yang sudah di-budget dan ada yang belum”

Ini menurut saya **requirement penting, tetapi belum matang**.

Karena agar sistem bisa menentukan:

```text
ADS = budgeted
Lunch Meeting = unbudgeted
```

maka sistem harus punya sumber data yang pasti:

* kategori pengeluaran apa?
* budget-nya milik divisi mana?
* periodenya apa?
* nominal budget berapa?
* apakah hanya flag `budgeted/unbudgeted`, atau benar-benar cek sisa budget?
* siapa yang boleh menentukan kategori budget?

Kalau belum jelas, jangan dulu dibuat rule.

Karena kalau asal tambahkan checkbox:

```text
is_budgeted = True
```

itu belum menjawab siapa yang menentukan dan berdasarkan data apa.

**Kesimpulan:** business requirement valid, tapi masih **needs analysis**.

---

### 3. “Kalau sudah ada di budget, tidak perlu sampai CFO”

Nah, ini konsekuensi langsung dari poin 2.

Artinya approval route punya dimensi tambahan.

Bukan lagi:

```text
Division + Amount
```

tetapi:

```text
Division
+ Amount
+ Budget Status
→ Approval Route
```

Misalnya:

```text
Growth Marketing
Rp 20 juta
Budgeted
→ Head → CMO
```

sedangkan:

```text
Growth Marketing
Rp 20 juta
Unbudgeted
→ Head → CMO → CFO
```

Secara bisnis ini cukup masuk akal.

Tetapi sebelum implementasi, perlu keputusan penting:

> CFO dilewati karena **budget sudah pernah disetujui sebelumnya**, atau karena nilai transaksinya dianggap otomatis authorized?

Ini berkaitan langsung dengan pemisahan **NEED vs FINANCIAL**.

Kalau budget sudah disetujui secara finansial di awal tahun, mungkin CFO tidak perlu approve lagi pada transaksi tertentu. Tapi itu harus jadi keputusan governance, bukan asumsi developer.

---

### 4. “Kalau Head yang membuat request, langsung ke C-Level”

Ini menurut saya rule yang sangat sehat.

Contoh normal:

```text
Aji
→ Esty (Head)
→ CMO
```

Kalau Esty yang request:

```text
Esty
→ Esty
→ CMO
```

tentu salah.

Seharusnya:

```text
Esty
→ CMO
```

Ini adalah **self-approval skip**, dan konsepnya sudah tepat.

Yang penting secara audit sistem tetap menyimpan bahwa step Head di-skip karena requester adalah authority yang sama, bukan diam-diam menghapus step dari sejarah.

**Kesimpulan:** rule ini bagus dan seharusnya tetap.

---

### 5. “Kalau Head vacant, semua amount lewat C-Level”

Ini juga masuk akal, tetapi berbeda dari self-approval.

Kasusnya:

```text
Head position = kosong
```

maka:

```text
Requester
→ C-Level terkait
→ CFO / CEO sesuai threshold
```

Yang harus ditentukan adalah apakah vacancy dianggap:

**A. Step Head tidak wajib**
→ sistem boleh skip otomatis.

atau

**B. Posisi Head harus punya acting person**
→ misalnya Acting Head / PLT.

Saya lebih suka secara governance pilihan B bila organisasi memungkinkan, karena approval lebih jelas.

Tetapi jika bisnis memang ingin:

> “kalau posisi benar-benar kosong, otomatis skip”

maka itu bisa menjadi rule eksplisit.

Yang tidak boleh terjadi adalah sistem diam-diam skip karena konfigurasi approver hilang. Harus dibedakan antara:

```text
vacant by business rule
```

dan:

```text
configuration missing by mistake
```

Ini sangat penting.

---

### 6. “Head cuti, setelah 7 hari lompat ke level berikutnya”

Ini bagian yang menurut saya paling perlu dibahas ulang dengan bisnis.

Ada dua konsep berbeda:

#### Delegation

```text
Esty cuti
→ authority Head tetap ada
→ dialihkan ke backup user
```

Contoh:

```text
Esty
   delegate to
Dina
```

Dina approve **sebagai delegate dari authority Head**.

#### Escalation

```text
Esty tidak approve 7 hari
→ Head step dianggap dilewati
→ CMO mengambil alih
```

Ini bukan delegation. Ini escalation.

Masalahnya: kalau Head memang mandatory, apakah boleh 7 hari kemudian persetujuannya dianggap tidak diperlukan?

Itu perubahan governance cukup besar.

Saya akan tanya ke bisnis:

> Setelah 7 hari, apakah C-Level **menggantikan Head**, atau **mengambil keputusan karena Head tidak merespons**?

Secara audit keduanya berbeda.

Kalau saya merancang V2, saya mungkin akan modelkan:

```text
Approval Step
Assigned Authority : Head
Due Date           : +7 days
Escalation Target  : C-Level
Escalation Mode    : transfer / add / skip
```

Tetapi itu fitur baru, bukan sekadar setting kecil.

---

### 7. “1 akun untuk 1 divisi, password dipegang 2 orang”

Bagian ini yang paling saya tidak rekomendasikan.

Secara praktik memang sering ada organisasi yang bekerja seperti ini. Tetapi dari sisi audit ERP, ini buruk.

Misalnya:

```text
username: marketing_request
password: ****
```

dipakai Aji dan Budi.

Kalau request dibuat jam 10:00, sistem cuma tahu:

```text
Created by: marketing_request
```

tidak tahu:

```text
Aji atau Budi?
```

Padahal approval system Anda sudah dirancang untuk menyimpan:

* requester
* resolved authority
* actual actor
* delegation
* history

Shared account merusak kejelasan itu.

Saya lebih menyarankan:

```text
Aji   → user Aji
Budi  → user Budi
```

keduanya punya hak membuat request untuk divisi yang sama.

Head tidak perlu tahu password mereka karena tiap orang punya password sendiri.

Jadi kebutuhan sebenarnya:

> “Hanya dua orang tertentu di divisi yang boleh membuat request, dan Head tidak boleh menggunakan capability requester mereka.”

Itu bisa diselesaikan dengan **role/capability**, bukan shared password.

Ini jauh lebih aman.

---

## Kalau saya simpulkan business rule-nya

Saya akan kelompokkan seperti ini:

| Note                       | Penilaian                           |
| -------------------------- | ----------------------------------- |
| Per-divisi configuration   | Sangat masuk akal                   |
| Budgeted vs unbudgeted     | Valid, tapi belum cukup terdefinisi |
| Budgeted skip CFO          | Valid, perlu governance decision    |
| Head requester → skip Head | Sangat masuk akal                   |
| Head vacant → C-Level      | Masuk akal, perlu definisi vacancy  |
| 7-day jump                 | Fitur escalation baru               |
| Shared account             | Tidak disarankan                    |

### Menurut saya, tiga keputusan bisnis terpenting sebelum lanjut adalah:

1. **Apakah Head/C-Level/CFO/CEO berada di NEED, FINANCIAL, atau campuran?**
2. **Apa definisi resmi budgeted vs unbudgeted?**
3. **Apakah “7 hari” berarti delegation, escalation, atau approval bypass?**

Kalau tiga hal itu belum jelas, jangan implementasi tambahan dulu.

Dan saya sarankan ubah requirement shared account menjadi requirement yang lebih sehat:

> “Setiap divisi memiliki maksimal/daftar user yang diberi capability sebagai Requester. Setiap pengguna menggunakan akun individual. Head tidak otomatis memperoleh capability Requester tersebut.”

Itu mempertahankan intent bisnis tanpa merusak audit trail.

Kalau Anda mau, langkah berikutnya saya bisa bantu ubah seluruh notes ini menjadi **Business Rules formal** dengan format `BR-001, BR-002, ...`, lengkap dengan `Rule`, `Rationale`, `Open Question`, dan `Implementation Status`. Itu akan sangat cocok dimasukkan ke BRD dan dibawa ke meeting dengan user.


Ya. Kita sebaiknya formalkan notes dari screenshot itu menjadi **Business Rules**, tetapi jangan langsung dianggap semuanya requirement final. Kita pisahkan mana yang sudah jelas, mana yang masih perlu keputusan bisnis, dan mana yang sebaiknya ditolak/diubah desainnya.

Berikut draft yang menurut saya cocok menjadi bahan BRD sekaligus bahan meeting user.

## Draft Business Rules — Approval Matrix

| ID             | Business Rule                                                                                                                          | Rationale / Tujuan                                                                          | Status terhadap LIGHT V1           | Open Question / Decision                                                                                          |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- | ---------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **BR-APR-001** | Approval configuration harus dapat berbeda untuk setiap divisi.                                                                        | Struktur approver Growth Marketing, PnC, BisPro, HR dapat berbeda.                          | **SUPPORTED / CONFIGURATION**      | Apakah `Request Type` boleh menjadi pembeda divisi, atau Division harus menjadi dimension tersendiri?             |
| **BR-APR-002** | Approval route dapat ditentukan berdasarkan nilai permintaan.                                                                          | Nilai yang lebih besar memerlukan authority yang lebih tinggi.                              | **PARTIAL**                        | FINANCIAL sudah amount-based; NEED belum. Tentukan dahulu NEED/FINANCIAL split.                                   |
| **BR-APR-003** | Head merupakan approval level pertama untuk requester biasa apabila posisi Head tersedia.                                              | Kebutuhan divisi divalidasi oleh Head sebelum naik ke authority berikutnya.                 | **SUPPORTED / CONFIGURATION**      | Tentukan apakah Head merupakan NEED approver.                                                                     |
| **BR-APR-004** | Jika Head sendiri membuat request, approval Head tidak boleh dilakukan oleh dirinya sendiri dan harus dilanjutkan ke level berikutnya. | Mencegah self-approval.                                                                     | **SUPPORTED**                      | Tidak ada blocker; `self_approval_skip` sudah tersedia.                                                           |
| **BR-APR-005** | Jika posisi Head vacant, request harus diteruskan ke C-Level terkait.                                                                  | Tidak boleh terjadi dead-end karena jabatan kosong.                                         | **SUPPORTED WITH CONFIGURATION**   | Perlu definisi resmi `vacant` vs sekadar konfigurasi approver yang belum diisi.                                   |
| **BR-APR-006** | Setiap divisi memiliki C-Level terkait.                                                                                                | Menentukan authority setelah Head sesuai organisasi.                                        | **SUPPORTED / CONFIGURATION**      | Mapping C-Level per divisi perlu dikonfirmasi.                                                                    |
| **BR-APR-007** | CFO/CEO dapat ditambahkan ke approval chain berdasarkan threshold amount.                                                              | Transaksi bernilai tinggi memerlukan financial authority lebih tinggi.                      | **SUPPORTED untuk FINANCIAL**      | Tentukan apakah CFO/CEO termasuk NEED atau FINANCIAL.                                                             |
| **BR-APR-008** | Jenis pengeluaran tertentu dapat diklasifikasikan sebagai budgeted atau unbudgeted.                                                    | Pengeluaran yang sudah memperoleh budget treatment berbeda dari pengeluaran di luar budget. | **NOT DEFINED / GAP**              | Definisi, sumber data, periode, category dan ownership budget belum ditentukan.                                   |
| **BR-APR-009** | Pengeluaran yang sudah di-budget tidak perlu approval sampai CFO dan cukup sampai C-Level terkait.                                     | Financial authority dianggap telah diberikan melalui proses budgeting sebelumnya.           | **GAP / NEEDS DESIGN**             | Harus dipastikan apakah berlaku untuk semua amount dan apa bukti bahwa expense benar-benar budgeted.              |
| **BR-APR-010** | Jika Head sedang cuti, terdapat mekanisme pengganti approval.                                                                          | Approval tidak boleh berhenti karena approver unavailable.                                  | **PARTIALLY SUPPORTED**            | Delegation manual sudah ada. Tentukan apakah itu cukup.                                                           |
| **BR-APR-011** | Jika approval Head belum dilakukan selama 7 hari, approval dapat dieskalasikan ke level berikutnya.                                    | Menghindari request tertahan terlalu lama.                                                  | **FUNCTIONAL GAP**                 | Tentukan apakah level berikutnya menggantikan Head, mendapat authority tambahan, atau Head step dianggap skipped. |
| **BR-APR-012** | Setiap tindakan request/approval harus dapat ditelusuri ke individu yang melakukan tindakan.                                           | Auditability dan accountability.                                                            | **SUPPORTED**                      | Sebaiknya menjadi mandatory governance rule.                                                                      |
| **BR-APR-013** | User yang boleh membuat request untuk suatu divisi harus dapat dibatasi.                                                               | Mempertahankan intent “hanya orang tertentu dari divisi yang boleh request”.                | **SUPPORTED / NEED CONFIG DESIGN** | Tentukan siapa user yang authorized per divisi.                                                                   |
| **BR-APR-014** | Shared credential tidak digunakan untuk aktivitas transactional/approval.                                                              | Shared account menghilangkan identitas actual actor dan merusak audit trail.                | **RECOMMENDED GOVERNANCE RULE**    | Business perlu menyetujui penggantian konsep “1 akun/2 pemegang password” dengan named users.                     |

---

## Ada satu rule yang saya sengaja ubah

Catatan asli mengatakan:

> “1 akun untuk 1 divisi, yang boleh pegang pass nya hanya 2 orang dari div tersebut (dan Head nya tidak boleh mengetahui pass tersebut).”

Saya **tidak akan menuliskannya ke BRD sebagai requirement final seperti itu**.

Karena business intent yang saya tangkap sebenarnya bukan “harus berbagi password”, melainkan:

> Hanya personel tertentu dalam suatu divisi yang berwenang membuat User Request, dan Head tidak boleh otomatis memperoleh kewenangan tersebut.

Itu jauh lebih baik diterjemahkan menjadi:

**BR-APR-013 — Authorized Requester per Division**

```text
Growth Marketing
├── Aji        ✓ boleh membuat request
├── User B     ✓ boleh membuat request
└── Esty       tidak otomatis mendapat capability tersebut
```

Masing-masing tetap login menggunakan akun sendiri.

Dengan begitu audit:

```text
Requested by : Aji
Approved by  : Esty
Approved by  : CMO
```

tetap valid.

Bukan:

```text
Requested by : GM_SHARED_ACCOUNT
```

yang membuat kita tidak tahu Aji atau orang kedua yang sebenarnya melakukan transaksi.

---

# Hal yang perlu dibawa ke meeting bisnis

Saya sarankan jangan membawa 14 rule di atas sebagai 14 pertanyaan terpisah. Business user bisa kewalahan. Kelompokkan menjadi **5 keputusan**.

### Decision 01 — NEED vs FINANCIAL

Ini yang paling penting.

Tanyakan:

> Dalam matrix Head → C-Level → CFO → CEO, approval masing-masing sebenarnya menyetujui apa?

Berikan pilihan:

**Option A**

```text
NEED
Head → C-Level

FINANCIAL
CFO → CEO
```

**Option B**

```text
NEED
Head → C-Level → CFO → CEO
```

**Option C**

```text
FINANCIAL
Head → C-Level → CFO → CEO
```

Saya merekomendasikan **A sebagai kandidat untuk didiskusikan**, bukan langsung ditetapkan.

---

### Decision 02 — Budget

Contohkan dengan kasus nyata:

```text
Growth Marketing
ADS
Rp30 juta
Budgeted
```

Apakah:

```text
Requester
→ Head
→ CMO
→ STOP
```

sedangkan:

```text
Growth Marketing
Lunch Meeting
Rp30 juta
Unbudgeted

Requester
→ Head
→ CMO
→ CFO
```

Kalau jawabannya ya, baru kita tahu bentuk requirement budget routing yang sebenarnya.

Kemudian masih harus ditanyakan: **siapa/sistem apa yang menyatakan ADS tersebut budgeted?**

---

### Decision 03 — Vacant vs Leave

Jangan campurkan keduanya.

**Vacant:**

```text
Head position kosong
→ C-Level
```

**Leave:**

```text
Head masih memegang jabatan
tetapi sedang cuti
→ Delegate?
```

**Timeout:**

```text
Head ada
tidak merespons 7 hari
→ Escalation?
```

Itu tiga kondisi bisnis berbeda.

---

### Decision 04 — Amount boundary

Harus dibuat eksplisit.

Saya sarankan kandidat:

```text
0 <= amount < 5.000.000
5.000.000 <= amount < 10.000.000
10.000.000 <= amount < 50.000.000
amount >= 50.000.000
```

Tapi ada masalah dengan label screenshot `> 50 juta`. Jika bisnis benar-benar bermaksud **lebih besar dari** Rp50 juta, kita harus menentukan apa yang terjadi tepat pada Rp50.000.000.

Jadi jangan ubah label menjadi rule tanpa konfirmasi.

---

### Decision 05 — Account model

Tanyakan business intent, bukan sekadar menolak.

> “Apakah tujuan 1 akun divisi adalah agar hanya dua orang tertentu yang dapat membuat request?”

Kalau jawabannya **ya**, tawarkan:

```text
Named User A ─┐
              ├─ Authorized Requester: Growth Marketing
Named User B ─┘

Head → tidak memiliki requester capability tersebut
```

Tidak perlu password sharing.

---

# Setelah keputusan itu didapat

Baru kita bisa menghasilkan matrix final yang jauh lebih berguna:

```text
Division
  ↓
Expense / Budget Status
  ↓
Amount
  ↓
NEED Approval
  ↓
FINANCIAL Approval
  ↓
Special Routing
```

Contoh hipotetis setelah disetujui:

```text
Growth Marketing
Expense      : Lunch Meeting
Budget       : Unbudgeted
Amount       : Rp65 juta

Requester    : Aji

NEED
1. Esty / Division Head
2. CMO

FINANCIAL
1. CFO
2. CEO
```

Sedangkan:

```text
Growth Marketing
Expense      : ADS
Budget       : Budgeted
Amount       : Rp65 juta

Requester    : Aji

NEED
1. Esty
2. CMO

FINANCIAL
Skipped / not required
karena budget authorization sudah ada
```

**Itu contoh desain untuk diskusi, belum keputusan final.**

---

## Status yang saya sarankan sekarang

Jangan buat M21 dan jangan ubah `light-ir-m20`.

Posisi kita saat ini lebih tepat:

```text
V1 baseline
light-ir-m20
       │
       ├── PRE-UAT / Business Validation
       │
       ├── Approval Matrix Clarification
       │
       └── Business Decision Record
                    │
                    ▼
          Post-V1 Gap Assessment
                    │
           ┌────────┴────────┐
           │                 │
     Configuration      Enhancement
                         (jika disetujui)
```

Jadi analisis Claude tadi justru berguna: sekarang kita tahu **mana yang cukup dikonfigurasi dan mana yang benar-benar membutuhkan pengembangan berikutnya**.

Setelah business menjawab lima keputusan di atas, baru kita dapat menentukan apakah perlu M21/V1.1, atau sebagian besar matrix ternyata cukup dengan konfigurasi LIGHT yang sudah ada.
