# 06 — DATABASE RULES

## 1. Prinsip
- Schema adalah kontrak. Perubahan via migration, tidak edit manual di prod.
- Naming: tabel `snake_case plural` (users), kolom `snake_case`, PK `id uuid/text`, FK `xxx_id`.
- Wajib kolom: `id, created_at, updated_at`. Soft delete `deleted_at` jika data sensitif.

## 2. Template Tabel
```sql
-- users
id uuid PK, email text UNIQUE NOT NULL, name text,
password_hash text, role text DEFAULT 'user',
created_at timestamptz DEFAULT now(), updated_at timestamptz
```

## 3. Relasi & Integritas
- FK dengan `ON DELETE` eksplisit (CASCADE hanya jika aman, default RESTRICT).
- UNIQUE untuk data bisnis (email, slug, order_no).
- CHECK untuk enum/status agar tidak ada status liar.
- Index: semua FK + kolom filter/sort/search utama.

## 4. Query Rules
- Select hanya kolom perlu (hindari `SELECT *` di app).
- Pagination keyset untuk data besar, offset untuk admin kecil.
- Transaksi untuk multi-write (order + stock + payment).

## 5. Migration Checklist
- [ ] Up + down teruji lokal
- [ ] Backfill data aman (batch, bukan sekali jutaan row)
- [ ] Index CONCURRENTLY di prod besar
- [ ] Backup sebelum migrate destruktif

## 6. Seed (detail di 15)
- Seed dev terpisah dari prod. Tidak ada password asli di seed.
