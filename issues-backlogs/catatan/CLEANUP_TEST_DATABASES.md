# Cleanup Test Database PostgreSQL (Odoo Dev)

## Masalah

Banyak database testing menumpuk hasil regression test M13-M20
(`m13b_*`, `m14_*`, ..., `light_m19_*`, `light_m20_*`). Percobaan
`dropdb`/`DROP DATABASE` manual gagal dengan error:

```
ERROR:  syntax error at or near "0"
ERROR:  database "m16_final_regress" is being accessed by other users
DETAIL:  There is 1 other session using the database.
```

**Penyebab:**
1. Syntax error — nama database mengandung karakter yang bikin shell
   quoting berantakan saat drop manual satu-satu tanpa quote yang benar.
2. `being accessed by other users` — koneksi lama (dari test runner
   Odoo yang crash/tidak clean disconnect) masih menempel ke database,
   PostgreSQL menolak `DROP DATABASE` selama masih ada session aktif.

## Solusi

Container: `odoo18-dev-db` (postgres:16), user `odoo`.

### 1. List semua database, kecualikan yang wajib dipertahankan

```bash
docker exec -i odoo18-dev-db psql -U odoo -d postgres -Atc \
  "SELECT datname FROM pg_database WHERE datistemplate = false AND datname NOT IN ('main','postgres') ORDER BY datname;" \
  > dbs_to_drop.txt
```

- `datistemplate = false` — buang `template0`/`template1`.
- `datname NOT IN ('main','postgres')` — proteksi database produksi/
  sistem, tidak ikut kena drop.
- `-Atc` — Align off + Tuples only, output bersih satu nama per baris.

### 2. Generate script SQL: terminate connection lalu drop, per database

```bash
SCRIPT=drop_all.sql
: > "$SCRIPT"
while read -r db; do
  [ -z "$db" ] && continue
  echo "SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE datname = '$db' AND pid <> pg_backend_pid();" >> "$SCRIPT"
  echo "DROP DATABASE IF EXISTS \"$db\";" >> "$SCRIPT"
done < dbs_to_drop.txt
```

Poin penting:
- `pg_terminate_backend(pid)` dulu **sebelum** `DROP DATABASE` — ini
  yang menyelesaikan error "being accessed by other users".
- `pid <> pg_backend_pid()` — jangan bunuh koneksi psql yang sedang
  dipakai script itu sendiri.
- Nama database di-quote dengan `"..."` (double quote identifier) di
  `DROP DATABASE`, bukan single quote — ini yang menghindari syntax
  error kalau nama database ada karakter aneh.
- `IF EXISTS` — aman dijalankan ulang, tidak error kalau db sudah
  hilang duluan.

### 3. Eksekusi satu kali jalan (bukan loop per-database via docker exec)

```bash
docker exec -i odoo18-dev-db psql -U odoo -d postgres \
  -v ON_ERROR_STOP=0 -f /dev/stdin < drop_all.sql
```

- Kenapa **satu file sekaligus**, bukan loop `docker exec` per
  database: overhead spawn proses `docker exec` + koneksi psql baru
  per database bikin loop lambat/macet di tengah jalan (di kasus ini
  loop per-DB berhenti setelah 1 database karena timeout). Satu
  psql session, banyak statement `;` berurutan = jauh lebih cepat
  dan tidak macet.
- `-v ON_ERROR_STOP=0` — satu database gagal drop tidak menghentikan
  seluruh proses, lanjut ke database berikutnya.
- `-f /dev/stdin` + redirect `<` — psql baca script dari stdin
  container, tidak perlu copy file ke dalam container dulu.

### 4. Verifikasi hasil akhir

```bash
docker exec -i odoo18-dev-db psql -U odoo -d postgres -Atc \
  "SELECT datname FROM pg_database WHERE datistemplate = false ORDER BY datname;"
```

Hasil harus tinggal persis:
```
main
postgres
```

## Ringkasan Alur

```
list database (exclude main/postgres)
        v
generate script: terminate_backend + DROP DATABASE IF EXISTS per db
        v
jalankan sekali via psql -f /dev/stdin (bukan loop per-db)
        v
verifikasi: hanya main + postgres tersisa
```

## Catatan Keamanan

- **Selalu** exclude `main` (database aplikasi aktif) dan `postgres`
  (database sistem wajib PostgreSQL) di query awal — jangan andalkan
  ingat manual saat generate script.
- `pg_terminate_backend` memutus paksa koneksi apa pun ke database
  target — pastikan tidak ada proses penting (server Odoo yang
  sedang dipakai user lain) yang masih connect ke database yang mau
  didrop sebelum menjalankan ini di luar environment testing lokal.
