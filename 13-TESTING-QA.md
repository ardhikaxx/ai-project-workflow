# 13 — TESTING & QA (Framework Pengujian Lengkap)

> File ini mengatur STRATEGI dan BUKTI testing. Template prompt ada di `12` (P18). Coverage final diverifikasi di `20`.
> Prinsip `00`: dilarang mengklaim "testing berhasil" tanpa menjalankan test. Laporkan perintah + hasil nyata + yang di-skip.

## 1. Test Pyramid (Proporsional, Bukan Dogmatis)

```text
        / E2E (sedikit, alur kritis) \
       / Integration/API (sedang)    \
      / Feature/DB (banyak, bisnis)  \
     / Unit (banyak, rumus/helper)   \
```

- **Unit:** rumus, helper, policy murni (diskon, pajak, denda, total, permission check). Cepat, tanpa DB bila bisa.
- **Feature/DB:** alur modul dengan DB nyata/transaksi (buat order → stok berkurang → audit tercatat). Inti untuk aplikasi CRUD/workflow.
- **Integration/API:** kontrak (`09`): status code, format sukses/error, pagination, auth per role, idempotency, rate limit.
- **Browser/E2E:** sedikit tetapi kritis (login → checkout → bayar; booking → approve). Hanya untuk MUST yang bila rusak bisnis berhenti.
- **Non-fungsional:** security (`05`), responsive/a11y (`06`/`14`), performance (`17`), integritas DB (`16`). Dijadwalkan sebagai audit, bukan tiap commit.

Kapan tiap jenis dipakai: unit untuk logika hitung; feature untuk business rule; API untuk kontrak; E2E untuk uang/status kritis; audit untuk rilis. Jangan menulis E2E untuk semua tombol demi "coverage tinggi".

## 2. Prioritas: Apa yang Wajib Diuji vs Boleh Ringan

Wajib (blokir rilis bila gagal):

- Happy path MUST di `02` + 1–2 edge paling mungkin.
- Validasi backend (tipe, batas, enum, cross-field, unique).
- Authorization per role + ownership + IDOR negatif.
- Transaksi/konkurensi untuk uang/stok/slot (saldo tidak negatif, stok konsisten).
- Regression untuk bug yang pernah terjadi (tulis test yang mereproduksi bug sebelum fix).

Boleh ringan (smoke/manual tercatat):

- Halaman statis, CRUD master sederhana tanpa rule.
- Styling pixel-perfect (cukup screenshot + audit `14`).
- Laporan non-kritis (cukup 1 dataset representatif + EXPLAIN).

Setiap test memiliki ID yang terhubung ke requirement (`02` traceability): `TEST-<MODUL>-<NNN> ↔ REQ-...`.

## 3. Template Skenario per Fitur (Salin per `[EDIT: FITUR]`)

```text
TEST-[EDIT: MODUL]-001 Happy path: [EDIT: ...]
  Setup: role [EDIT: ROLE], data [EDIT: ...]
  Steps: [EDIT: ...]
  Expected: [EDIT: status + DB berubah + audit tercatat]
TEST-[EDIT: MODUL]-002 Validasi: [kirim input invalid: kosong/tipe salah/batas/unique/duplikat]
  Expected: 422 VALIDATION_ERROR per field, DB tidak berubah
TEST-[EDIT: MODUL]-003 Authorization positif/negatif:
  - role berhak → 200/201; role tak berhak → 403; tanpa login → 401
  - tukar ID milik orang lain → 403/404 aman, data tidak bocor
TEST-[EDIT: MODUL]-004 Edge: [duplikat submit / concurrent / timezone / hapus parent / file oversize]
  Expected: [EDIT: ...] + tidak ada data setengah jadi (transaksi rollback)
TEST-[EDIT: MODUL]-005 Regression (bila bug pernah ada): repro langkah bug → harus lolos sekarang
```

Contoh konkret (POS):

```text
TEST-POS-001: kasir outlet A buat order 2 item stok cukup → 201, stok berkurang atomik, audit tercatat
TEST-POS-002: kirim qty 0 / -1 / >stok → 422, stok tidak berubah
TEST-POS-003: kasir outlet B akses order outlet A → 403; tanpa token → 401
TEST-POS-004: double-click submit / retry dengan Idempotency-Key sama → 1 order saja
TEST-POS-005: diskon >10% tanpa approval owner → 403 + pesan jelas
```

## 4. Data Test, Isolasi, dan Determinisme

- Gunakan database test terpisah + factory/seeder khusus test (bukan data production). Reset per test (transaction rollback / refresh) agar independen.
- Tanggal/jam dibuat deterministik (freeze time) untuk uji lifecycle; random dengan seed tetap bila perlu.
- Mock integrasi eksternal (`[EDIT: EXTERNAL SERVICE]`): payment gateway, WA, email, storage. Test harus jalan offline + cepat. Satu smoke staging boleh memukul sandbox asli.
- Dilarang test yang bergantung urutan eksekusi, sleep acak, atau data manual yang tidak dibuat oleh test itu sendiri.

## 5. Menjalankan & Melaporkan (Bukti, Bukan Klaim)

Perintah contoh (sesuaikan stack `[EDIT: ...]` di `01`):

```text
[EDIT: PERINTAH TEST — contoh: npm run test / php artisan test / pytest]
[EDIT: PERINTAH TEST FILE TERTENTU]
[EDIT: PERINTAH TYPECHECK + LINT]
```

Laporan wajib:

```text
Perintah: [EDIT: ...] | Hasil: [X passed, Y failed, Z skipped] | Durasi: [...]
Gagal: [TEST-ID + log ringkas + hipotesis + fix]
Skip: [TEST-ID + alasan + risiko diterima siapa]
Belum diuji: [area + mengapa + rencana]
```

Dilarang menyembunyikan test gagal/skip. Jika test suite tidak ada (proyek baru), AI wajib membuatnya untuk area wajib di atas, bukan melewatkannya dengan alasan "belum ada test".

## 6. Regression & Failure Scenario

- Setiap bugfix wajib didahului test yang mereproduksi bug (red) lalu fix (green). Tanpa ini bug akan kembali.
- Failure scenario yang harus diuji minimal sekali sebelum rilis: DB down, storage penuh, provider eksternal timeout, token expired di tengah alur, validasi concurrent, file rusak/oversize, rollback migration.
- Simpan daftar `KNOWN ISSUES:` (bug yang diketahui + workaround + pemilik + target fix) agar tidak dianggap selesai diam-diam.

---

**Gate ke audit/rilis:** area wajib hijau dengan bukti; skip/known issues tercatat + disetujui pemilik; traceability `REQ ↔ TEST` diperbarui.
