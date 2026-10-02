# 04 — DATABASE DESIGN (Perencanaan Database Sebelum Migration)

> File ini mengatur perencanaan database. Audit schema ada di `16`. Seeder ada di `18`. Prinsip anti-overengineering dan impact analysis ada di `00`.
> AI wajib menghasilkan desain di atas kertas (entity → relasi → constraint → index → migration plan) sebelum membuat migration.

## 1. Langkah Wajib Berurutan

1. **Identifikasi entity domain** dari `02`: kata benda inti (contoh: `customer`, `product`, `transaction`, `payment`). Bedakan entity vs atribut vs log.
2. **Atribut + lifecycle:** field apa, siapa pemiliknya, bagaimana status berubah, berapa lama disimpan (lihat `[EDIT: BACKUP]` / retensi di `01`).
3. **Relationship & ownership:** one-to-many / many-to-many, siapa parent, apa yang terjadi saat parent dihapus.
4. **Constraint & uniqueness:** apa yang tidak boleh duplikat, apa yang tidak boleh null, batas nilai.
5. **Auditability:** butuh `created_by/updated_by`? butuh audit trail terpisah? butuh soft delete vs hard delete?
6. **Transaksi & konkurensi:** alur mana butuh DB transaction, risiko race condition (stok, saldo, booking slot).
7. **Index & query plan:** query utama apa (list+filter+pagination, laporan agregat), index apa yang mendukungnya.
8. **Migration plan:** urutan pembuatan tabel, rollback strategy, backfill bila tabel existing.

Dilarang langsung membuat migration tanpa langkah 1–7 untuk tabel inti.

## 2. Aturan Key, Constraint, dan Tipe Data

- **Primary key:** ikuti konvensi framework/stack di `01` secara konsisten (`id` auto-increment vs UUIDv7). Jangan campur tanpa alasan. UUID bila butuh ID publik tidak berurutan / multi-writer; selain itu integer/bigint cukup.
- **Foreign key:** selalu dengan constraint + aksi `onDelete` eksplisit. Pilih sadar: `restrict` (aman default untuk data uang), `cascade` (hanya untuk anak yang tak bermakna tanpa parent, contoh: `order_items`), `set null` (bila anak boleh yatim + nullable + alasan bisnis), `no action` + soft delete (bila histori harus utuh).
- **Unique:** terapkan di DB, bukan hanya validasi aplikasi (contoh: `sku`, `email per tenant`, `slot booking`). Gunakan composite unique bila perlu (`outlet_id + sku`).
- **Nullable vs default:** nullable hanya bila "belum tahu" adalah keadaan valid. Selain itu wajib + default yang bermakna. Dilarang nullable untuk flag status inti.
- **Enum vs lookup table:** enum/check constraint untuk daftar stabil kecil (<10, jarang berubah, contoh: `status`). Lookup table bila butuh label dinamis, i18n, atau tambahan atribut per nilai.
- **Uang:** gunakan integer minor unit (sen) atau `decimal(19,2)` konsisten satu proyek. Dilarang float untuk uang. Cantumkan mata uang bila multi-currency.
- **Waktu:** `timestamptz` + simpan UTC, tampilkan sesuai `[EDIT: TIMEZONE]`. Catat timezone laporan.
- **Naming:** konsisten satu gaya (`snake_case` untuk SQL). Nama tabel plural vs singular ikut konvensi existing. Jangan rename tabel existing tanpa persetujuan + migration aman.

Contoh definisi yang benar (sesuaikan stack):

```text
TABLE products (
  id BIGINT PK,
  outlet_id BIGINT NOT NULL FK → outlets(id) ON DELETE RESTRICT,
  sku VARCHAR(32) NOT NULL,
  name VARCHAR(120) NOT NULL,
  price_cents BIGINT NOT NULL CHECK (price_cents >= 0),
  stock INT NOT NULL DEFAULT 0 CHECK (stock >= 0),
  is_active BOOLEAN NOT NULL DEFAULT TRUE,
  deleted_at TIMESTAMPTZ NULL, -- soft delete karena histori transaksi
  created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
  UNIQUE (outlet_id, sku)
);
INDEX idx_products_outlet_active (outlet_id, is_active);
```

## 3. Relasi Lanjutan: Pivot, Polimorfik, Hierarki

- **Pivot many-to-many:** beri PK sendiri bila butuh atribut tambahan (qty, harga saat itu, `created_by`). Beri unique composite untuk mencegah duplikat.
- **Polimorfik (`*_id` + `*_type`):** dilarang kecuali ada minimal 3 consumer + alasan kuat + strategi index + constraint pengganti (FK tidak bisa enforced). Pertimbangkan tabel terpisah atau JSONB tervalidasi sebagai alternatif yang lebih aman.
- **Hierarki (kategori/tree):** pilih `parent_id` sederhana untuk depth kecil, atau `ltree`/`closure table` bila query subtree berat. Jangan overdesign untuk 2 level.
- **Denormalisasi:** hanya untuk laporan berat yang terbukti lambat (contoh: `total` di header transaksi) + wajib dijaga via transaction / trigger aplikasi yang teruji, bukan manual. Catat sebagai `DENORMALIZED: <field> | dijaga oleh <mekanisme> | risiko stale: <x>`.

Alasan aturan ini: AI sering membuat database terlalu normal (join 7 tabel untuk list sederhana) atau terlalu denormal (duplikasi nama/harga di mana-mana tanpa penjagaan). Keduanya salah tanpa alasan terukur.

## 4. Indexing yang Proporsional

- Index untuk: FK yang sering di-join, kolom filter/sort utama, composite untuk query list utama (`(outlet_id, created_at DESC)`), unique sesuai Bagian 2.
- Jangan index semua kolom "untuk jaga-jaga". Setiap index memperlambat write + memakan ruang.
- Untuk teks pencarian: mulai dari `ILIKE` + trigram / full-text bawaan DB. Engine eksternal (Meilisearch/ES) hanya bila ada di `01` + volume membenarkan.
- Verifikasi dengan `EXPLAIN` untuk query laporan. Target: list paginated tanpa sequential scan pada tabel besar; agregat laporan memakai index atau materialized view bila terbukti perlu.

## 5. Query Strategy: N+1, Pagination, Agregat

AI wajib merancang pola query bersamaan dengan schema:

- **N+1:** identifikasi relasi yang akan di-loop di UI/API. Wajib eager loading / join / dataloader. Dilarang lazy load di loop. Audit di `16`.
- **Pagination:** server-side cursor/offset konsisten. Default dari `[EDIT: DEFAULT PAGINATION — contoh: 20]`. Jangan `SELECT *` tanpa limit pada tabel bertumbuh. Tentukan max `per_page` (contoh: 100).
- **Select minimal:** pilih kolom yang dibutuhkan. Dilarang `SELECT *` pada tabel lebar untuk list.
- **Agregat/laporan:** pisahkan query operasional (OLTP) dari laporan berat. Untuk laporan: agregat di DB (`GROUP BY`, window function), bukan tarik semua lalu hitung di aplikasi.
- **Transaksi:** bungkus alur uang/stok/status dalam satu transaksi DB dengan level yang tepat + row lock (`SELECT ... FOR UPDATE`) bila ada race (stok, saldo, slot). Uji skenario konkuren di `13`.

## 6. Migration Strategy

- Satu migration = satu tujuan, reversible. Tulis `down` / rollback yang benar-benar jalan.
- Urutan: parent → child, tambah kolom nullable dulu → backfill → jadikan NOT NULL bila tabel existing besar (hindari lock lama).
- Dilarang menghapus kolom/tabel destruktif tanpa backup + persetujuan + masa tenggang (rename deprecated dulu bila API publik).
- Setiap migration yang mengubah data kritis wajib: backup, dry-run di staging, rencana rollback, dan catatan di `21-CHANGELOG-WORKFLOW.md`.
- Perubahan schema adalah **silent change** bila tidak dilaporkan — wajib lapor sesuai `00` Bagian 8.

```text
Migration plan modul [EDIT: MODUL]:
1. create_outlets (parent)
2. create_products (FK restrict)
3. create_transactions + items (transaksional)
4. add_index_products_outlet_active
5. backfill is_active=true WHERE NULL (bila existing)
Rollback: langkah 4 → 3 → 2 → 1. Backup: [EDIT: BACKUP]
```

## 7. Soft Delete, Histori, dan Retensi

- Gunakan soft delete (`deleted_at`) bila data punya histori hukum/bisnis (transaksi, invoice, rekam medis). Gunakan hard delete hanya untuk data ephemeral (draft kosong, token expired, cache).
- Tentukan: apakah data terhapus masih muncul di laporan? Siapa boleh restore? Kapan purge permanen?
- Audit trail terpisah (tabel `audit_logs`) untuk: perubahan uang, status, permission, hapus/restore. Isi: `actor, action, entity, before, after, reason, at`.
- Retensi sesuai `[EDIT: REGULASI]` / `[EDIT: BACKUP]` di `01`. Jangan menyimpan data pribadi lebih lama dari perlu (lihat `05`).

---

**Gate:** desain + migration plan disetujui → baru buat migration. Setelah implementasi, jalankan `16-DATABASE-AUDIT.md` sebelum dianggap selesai.
