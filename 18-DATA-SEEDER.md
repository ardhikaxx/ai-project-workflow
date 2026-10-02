# 18 — DATA SEEDER (Data Sintetis yang Realistis, Relasional, Kronologis)

> Seeder untuk development/demo/testing — BUKAN data production dan BUKAN data pribadi nyata. Konsisten dengan schema `04`, aman secara security `05`, dan tidak menjadi slop (`10`).
> Dilarang: `User 1..10`, `Product 1`, `Lorem ipsum`, `test@test.com` sebagai data demo utama — kecuali untuk uji batas/edge yang diberi label jelas.

## 1. Prinsip Data Realistis

- **Relevan domain:** nama, alamat, SKU, harga, dan alur harus cocok `[EDIT: DOMAIN BISNIS]` di `01`. Contoh rental: `Excavator PC78US-11 — 0.28m³`, tarif harian/mingguan, deposit, overtime. Contoh klinik: poli, dokter, slot, ICD — bukan barang elektronik.
- **Relasional:** setiap anak menunjuk parent yang ada (FK valid), ownership konsisten (`outlet_id` sama di order+items+payment), tidak ada orphan.
- **Kronologis:** `created_at` berurutan logis (daftar → pesan → bayar → kirim → selesai). Jangan ada pembayaran sebelum order, atau ulasan sebelum transaksi selesai.
- **Distribusi status wajar:** contoh: 60% selesai, 20% aktif, 10% pending, 7% batal, 3% overdue — bukan 100% sukses atau acak seragam. Cantumkan distribusi di `[EDIT: ...]`.
- **Harga/angka wajar:** ikuti `[EDIT: ATURAN BISNIS INTI]` (tarif, pajak, denda). Jangan harga `Rp1.000` untuk excavator atau stok `999999`.
- **PII sintetis:** nama Indonesia wajar + variasi, telepon format `[EDIT: FORMAT NOMOR — contoh: 08xx-xxxx-xxxx]`, email domain `[EDIT: DOMAIN EMAIL DEV — contoh: @example.test]`, alamat campuran kota sesuai domain. Dilarang NIK/alamat asli.

## 2. Konfigurasi per Proyek (Wajib Diisi di `01` / Header Seeder)

```text
[EDIT: JUMLAH DATA — contoh: 5 outlet, 40 produk, 200 customer, 800 transaksi 90 hari]
[EDIT: RENTANG TANGGAL — contoh: 2026-07-01 s.d. 2026-09-30]
[EDIT: TIMEZONE — contoh: Asia/Jakarta]
[EDIT: FORMAT NOMOR / EMAIL / MATA UANG]
[EDIT: DISTRIBUSI ROLE — contoh: 1 superadmin, 5 admin, 10 kasir, 1 owner read-only]
[EDIT: DISTRIBUSI STATUS — contoh: lihat Bagian 1]
[EDIT: DATA SENSITIF YANG TIDAK BOLEH DI-SEED — contoh: NIK asli, rekam medis asli]
```

Jumlah default hemat: cukup untuk menguji pagination (2–3 halaman), filter, dan 1 laporan. Jangan seed 1 juta row "untuk jaga-jaga" tanpa alasan perf test.

## 3. Urutan Seeding (Dependency Order) & Idempotensi

```text
1. Role/permission → 2. Outlet/cabang → 3. User (per outlet + role)
→ 4. Kategori/master → 5. Produk/layanan → 6. Customer
→ 7. Transaksi header → 8. Items (harga snapshot saat itu) → 9. Payment/status log
→ 10. Audit log → 11. File/lampiran (dummy valid) → 12. Laporan/cache refresh
```

- Idempoten: bisa dijalankan ulang tanpa duplikat (cek `firstOrCreate` / upsert by unique key). Sediakan `db:seed --fresh-dev` yang sadar bahaya (kunci: dilarang di production tanpa guard + konfirmasi ganda).
- Harga snapshot: salin harga saat transaksi (bukan referensi live) agar laporan historis stabil bila harga master berubah.
- Akun default: buat dari env (`SEED_ADMIN_PASSWORD`), paksa ganti saat login pertama, jangan hardcode password lemah di repo.

## 4. Contoh yang Benar vs Salah

Benar (rental):

```text
Customer: Siti Rahmawati, 0812-3456-7890, siti.r@example.test, Cileungsi-Bogor
Order #INV-2026-08-014: Excavator PC78 × 3 hari (2026-08-04→07), deposit 2jt,
overtime 4 jam × 150rb, denda 0 (tepat waktu), status: closed, audit: created by Kasir-A outlet Cileungsi
```

Salah (slop):

```text
User 1, test1@test.com, Product 1 Rp1000, Order 1 status "status1", Lorem ipsum dolor
```

## 5. Keamanan & Etika Seeder

- Tidak ada password/token/API key asli. Tidak ada data customer nyata.
- Tandai jelas data dummy di UI dev (banner "Data demo — bukan data asli") agar tidak dikira production.
- Pisahkan seeder dev/demo dari seeder wajib production (role dasar, master pajak) — yang kedua minimal + direview + tanpa akun lemah.
- Lampiran dummy harus file valid kecil (PDF/JPG 10–50KB), bukan payload berbahaya. Upload path ikut aturan `05`/`08`.

---

**Gate:** setelah seed, jalankan: FK valid (tanpa orphan), list + filter + pagination + 1 laporan jalan, distribusi status/harga wajar, dan audit `16` tanpa Critical sebelum demo ke pemilik.
