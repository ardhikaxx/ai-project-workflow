# 16 — DATABASE AUDIT (Checklist + Prompt Audit Database & Query)

> Audit khusus schema, migration, constraint, index, query, dan integritas data. Desain di `04`, implementasi query di `08`, seeder di `18`, test di `13`.
> Jalankan sebelum rilis dan setiap kali ada migration baru.

## 1. Cara Menjalankan

1. Baca `00` + `01` (`[EDIT: DATABASE + VERSI]`) + `04` + migration aktual + schema dump.
2. Scope: `[EDIT: MODUL/TABEL]` — jangan audit seluruh DB tanpa batas pada DB besar.
3. Periksa: dump schema, daftar migration, FK/index/unique, sample data (dev/staging, bukan production PII), slow query log + `EXPLAIN` untuk query utama.
4. Klasifikasikan Critical/High/Medium/Low seperti `15`. Dilarang fix destruktif (drop/alter besar) tanpa backup + dry-run staging + persetujuan.

## 2. Checklist

### A. Schema, Relasi, Constraint
- [ ] PK konsisten satu gaya? FK lengkap + `onDelete` eksplisit dan tepat (restrict vs cascade vs set null)?
- [ ] Unique di DB untuk yang harus unik (bukan hanya validasi aplikasi)? Composite unique bila perlu?
- [ ] Nullable/default tepat (status inti tidak nullable tanpa alasan; uang punya default + check ≥0)?
- [ ] Enum vs lookup tepat (enum untuk stabil kecil; lookup bila dinamis)? Tipe uang/waktu benar (tanpa float untuk uang; timestamptz UTC)?
- [ ] Naming konsisten + tidak ada rename tabel/kolom existing tanpa migration aman + laporan `BERUBAH:`?
- [ ] Tidak ada field redundant (`total` duplikat tanpa penjagaan, `user_name` disalin ke 5 tabel tanpa alasan)? Denormalisasi tercatat + dijaga transaksi?

### B. Index & Query Performance
- [ ] Index untuk FK join, filter/sort utama, composite list utama? Tanpa index berlebih "jaga-jaga"?
- [ ] Query list memakai pagination + limit + select minimal (tanpa `SELECT *` lebar)?
- [ ] Bebas N+1 (eager/join/batching)? Agregat laporan di DB (bukan tarik semua ke aplikasi)?
- [ ] `EXPLAIN` untuk query utama/laporan: tanpa sequential scan pada tabel besar; sort memakai index atau batas wajar?
- [ ] Search teks proporsional (trigram/full-text bawaan dulu; engine eksternal hanya bila di `01`)?

### C. Integritas, Orphan, Transaksi
- [ ] Tidak ada orphan (anak tanpa parent) — cek via anti-join? Cascade tidak menghapus histori berharga diam-diam?
- [ ] Tidak ada duplikat yang seharusnya unik? Tidak ada null di kolom yang seharusnya wajib (cek distribusi null)?
- [ ] Transaksi menutupi alur uang/stok/status? Uji gagal di tengah → rollback penuh (tanpa setengah jadi)?
- [ ] Race diuji (dua request konkuren untuk stok/slot terakhir → hanya satu menang + satunya ditolak anggun)?
- [ ] Soft delete konsisten (query default mengecualikan terhapus; laporan memutuskan eksplisit include/exclude; restore + purge terdefinisi)?

### D. Migration & Skalabilitas
- [ ] Satu migration satu tujuan + reversible + teruji rollback? Urutan parent→child benar?
- [ ] Backfill aman untuk tabel besar (batch, tanpa lock lama)? Destruktif (drop/alter tipe) punya backup + tenggang + persetujuan?
- [ ] Retensi/purge sesuai `[EDIT: REGULASI]`/`[EDIT: BACKUP]`? PII tidak disimpan berlebihan?
- [ ] Estimasi tumbuh (row/bulan, ukuran index) masuk akal untuk 12 bulan? Laporan berat punya strategi (index/materialized/pemisahan) bila terbukti perlu — bukan prematur?

## 3. Contoh Masalah Khas Buatan AI

- `orders.user_id` tanpa FK → orphan saat user dihapus → tambah FK + tentukan `RESTRICT` + cleanup orphan.
- `products.sku` unik hanya di validasi → race menciptakan duplikat → tambah `UNIQUE(outlet_id, sku)` + handling `409`.
- `price FLOAT` → error pembulatan uang → migrasi ke `BIGINT cents` / `DECIMAL` + backfill + test rumus.
- Polimorfik `attachable_id/type` untuk 2 consumer tanpa index → ganti tabel terpisah + FK nyata.
- List order join 6 tabel tanpa index `(outlet_id, created_at)` → tambah composite index + `EXPLAIN` sebelum/sesudah.
- `SELECT * FROM logs` tanpa limit di dashboard → tambah pagination + arsip/purge log lama.

## 4. Query Pemeriksaan Contoh (Sesuaikan Dialek `[EDIT: DATABASE]`)

```sql
-- Orphan check
SELECT COUNT(*) FROM order_items oi LEFT JOIN orders o ON o.id = oi.order_id WHERE o.id IS NULL;
-- Duplikat yang seharusnya unik
SELECT outlet_id, sku, COUNT(*) FROM products GROUP BY 1,2 HAVING COUNT(*) > 1;
-- Distribusi null mencurigakan
SELECT COUNT(*) FILTER (WHERE email IS NULL) AS null_email, COUNT(*) FROM users;
-- Index yang ada
SELECT tablename, indexname, indexdef FROM pg_indexes WHERE schemaname='public' ORDER BY 1,2;
```

## 5. Format Laporan

```text
[AUDIT-DB-001] [Critical] [tabel orders]
Masalah: tanpa UNIQUE (outlet_id, sku) sehingga duplikat tercipta saat retry.
Bukti: query duplikat → 17 baris; migration 2024_... tidak punya unique.
Dampak: order ganda / stok salah.
Saran fix: tambah unique + backfill dedup + test idempotency.
Rollback: drop index bila gagal; backup: [EDIT: BACKUP].
Status: OPEN / FIXED / WAIVED
```

---

**Gate:** nol orphan/duplikat kritis; N+1 + query tanpa limit diperbaiki; migration destruktif hanya dengan backup + persetujuan; temuan dicatat ke `20` dan `21` bila mengubah schema.
