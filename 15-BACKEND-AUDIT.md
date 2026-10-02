# 15 — BACKEND AUDIT (Checklist + Prompt Audit Backend)

> Audit ini memeriksa arsitektur, kebenaran, keamanan, dan maintainability backend setelah `08`+`09`+`05` diimplementasikan.
> Output: temuan ber-severity + cara reproduksi. Dilarang auto-fix Critical (data/uang/auth) tanpa persetujuan + backup.

## 1. Cara Menjalankan

1. Baca `00` + `01` + `02` AC + `08` + `09` + `05`.
2. Pilih scope: `[EDIT: MODUL/ENDPOINT]` — jangan audit "semuanya" sekaligus tanpa batas.
3. Periksa kode + jalankan endpoint (happy + invalid + unauthorized) + baca log.
4. Klasifikasikan: **Critical** (kebocoran data/uang/hapus massal/auth bypass/kerusakan data), **High** (bug sering, tanpa transaksi untuk uang, N+1 berat, error bocor), **Medium** (inkonsistensi kontrak, log berlebih/kurang, duplikasi), **Low** (gaya, penamaan, komentar).
5. Untuk tiap temuan: bukti file:baris + request/response repro + dampak + saran fix minimal + area regression bila di-fix.

## 2. Checklist

### A. Arsitektur & Kompleksitas
- [ ] Controller tipis? Business logic di service, bukan tersebar di route/controller/model/frontend?
- [ ] Satu method = satu use-case? Tidak ada endpoint "super" multi-tugas?
- [ ] Tidak ada repository/DTO/interface berlapis untuk CRUD sederhana tanpa alasan di `03`?
- [ ] Event/queue hanya untuk efek samping + idempoten? Tidak ada queue yang sebenarnya tidak dibutuhkan (`01`/`03`)?

### B. Validasi & Kontrak
- [ ] Semua input divalidasi di backend (tipe, panjang, enum, cross-field, unknown field)?
- [ ] Mass assignment aman (whitelist/DTO, tanpa `$request->all()` ke field sensitif)?
- [ ] Response/error/pagination konsisten dengan `09` + `00` (termasuk `code` stabil)?
- [ ] Tidak ada breaking change diam-diam (rename/hapus field, ubah status code) tanpa versi + doc + laporan `BERUBAH:`?

### C. Authorization & Security (Rujuk `05`)
- [ ] Setiap endpoint menulis `auth + role + ownership` dan menegakkannya di backend?
- [ ] IDOR diuji (tukar ID antar user/outlet → ditolak aman)?
- [ ] Rate limiting untuk login/reset/upload/endpoint mahal? Audit log untuk uang/status/permission/hapus?
- [ ] Upload aman (tipe/ukuran/rename/path traversal)? Secret tidak di kode/log? Pesan error tidak membocorkan SQL/path/env?

### D. Transaksi, Konkurensi, Integritas
- [ ] Alur multi-tulis dalam transaksi sesingkat mungkin + rollback terbukti (uji gagal di tengah)?
- [ ] Race dilindungi (row lock / constraint `CHECK` + handling konflik)? Double submit/idempotency untuk endpoint kritis?
- [ ] Soft delete vs hard delete sesuai `04`? Tidak ada hapus permanen data bersejarah tanpa backup + persetujuan?
- [ ] File yatim dibersihkan via job? Notifikasi gagal punya retry + status terpantau?

### E. Query & Performance Backend
- [ ] Tidak ada query di loop (N+1)? Eager loading / join / batching dipakai?
- [ ] Pagination + batas `per_page` + select minimal? Tidak ada `SELECT *` raksasa tanpa limit?
- [ ] Laporan agregat di DB, bukan tarik semua lalu hitung di memori? `EXPLAIN` untuk query berat?
- [ ] Cache hanya untuk data idempoten + invalidasi jelas + key scoping benar per user/tenant?

### F. Error Handling, Logging, Maintainability
- [ ] Error terduga → response standar; tak terduga → log + pesan generik (tanpa stack ke user)?
- [ ] Log berformat (level, trace id, actor, action, duration, outcome) tanpa password/token/PII penuh? Trace id sampai ke frontend?
- [ ] Tidak ada `console.log`/debug tertinggal, TODO tanpa issue, kode mati, atau duplikasi rumus di ≥2 tempat?
- [ ] Test relevan ada (`13`) + lolos dengan bukti? Bug yang pernah ada punya regression test?

## 3. Contoh Masalah Khas Buatan AI

- Controller 300+ baris berisi rumus diskon + update stok + kirim WA tanpa transaksi → pecah ke `Service` + `DB::transaction()` + event.
- `GET /orders?user_id=123` tanpa ownership check → siapa pun bisa intip order orang lain → ganti ke scope dari session + test IDOR.
- `update($request->all())` memungkinkan `role=admin` → whitelist + test mass assignment.
- List 10.000 row tanpa pagination → tambah pagination + max limit + uji `EXPLAIN`.
- Pesan error `SQLSTATE[23000]: ...` bocor ke user → map ke `CONFLICT` generik + log detail.

## 4. Format Laporan

```text
[AUDIT-BE-001] [Critical] [POST /api/v1/orders]
Masalah: tanpa transaksi; bila update stok gagal, order tetap tercipta (data setengah jadi).
Bukti: backend/OrderService.php:45 + repro: matikan DB di tengah → order yatim.
Dampak: stok/uang tidak konsisten.
Saran fix minimal: bungkus dalam transaction + test TEST-POS-004.
Regression bila di-fix: list order, laporan stok, webhook.
Status: OPEN / FIXED / WAIVED (oleh siapa + alasan + risiko diterima)
```

---

**Gate:** nol Critical terbuka; High dijadwalkan sebelum rilis; setiap fix diikuti regression test (`13`) + catatan changelog bila mengubah kontrak (`21`).
