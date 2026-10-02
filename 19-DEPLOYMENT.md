# 19 — DEPLOYMENT (Dari Kode Lokal ke Production yang Aman)

> File ini mengatur persiapan, eksekusi, dan verifikasi pasca-deploy. Stack konkret dari `[EDIT: DEPLOYMENT]` di `01`. Prinsip secret, backup, dan rollback di sini mengikat `20-FINAL-CHECKLIST.md`.
> Dilarang deploy tanpa backup + migration plan + smoke test, meskipun "hanya perubahan kecil".

## 1. Persiapan Environment & Secret

- Pisahkan config per environment: `local / staging / production`. Daftar env wajib (tanpa nilai):

```text
APP_ENV, APP_URL, APP_KEY/SECRET
DATABASE_URL ([EDIT: DATABASE])
SESSION/TOKEN driver + expiry ([EDIT: METODE AUTHENTICATION])
STORAGE driver + bucket/path ([EDIT: STORAGE])
MAIL/WA provider + sender ([EDIT: EXTERNAL SERVICE])
SENTRY/APM DSN (bila ada), LOG_LEVEL
Fitur flag: [EDIT: ...]
```

- `.env.example` wajib ada + terdokumentasi. `.env` nyata gitignored. Secret di secret manager / panel hosting, bukan di repo/chat/log.
- Validasi saat boot: gagal cepat (fail-fast) bila env kritis hilang, dengan pesan yang tidak membocorkan nilai.
- Bedakan dummy yang boleh di local vs wajib real di production (payment/prod SMTP/WA tidak boleh dummy saat live).

## 2. Build, Migrasi, dan Bagian Generik per Stack

- **Build:** install terkunci (lockfile), typecheck + lint + test kunci hijau, build aset (hash filename, kompresi), tanpa devDependency di production.
- **Migrasi:** jalankan dengan urutan `04`, mode maintenance bila perlu, backup dulu (Bagian 3), dry-run di staging, siapkan rollback yang teruji. Dilarang `db:fresh` / drop di production.
- **Per stack (sesuaikan `[EDIT: DEPLOYMENT]`):**
  - VPS + Docker: image pinned (bukan `latest` liar), volume untuk storage/DB, restart policy, healthcheck, reverse proxy + TLS.
  - Vercel/serverless + managed DB: env per environment, migration via CI aman, batas timeout/cold start untuk job berat (pindahkan ke queue/cron eksternal).
  - Shared hosting/cPanel: pastikan versi PHP/Node sesuai, tanpa Redis/queue bila tidak didukung (lihat batasan `01`), cron via panel, storage permission benar.
- **Storage/queue/cache/mail:** direktori writable, antrean worker jalan + dimonitor, cache terisi ulang setelah deploy, mail/WA teruji kirim 1 pesan sandbox → production.

## 3. Backup, Keamanan Transport, dan Konfigurasi Production

- Backup sebelum setiap rilis yang menyentuh schema/data: DB dump + file storage snapshot + catatan lokasi + retensi `[EDIT: BACKUP]`. Uji restore minimal sekali per perubahan besar (bukan hanya "backup ada").
- HTTPS wajib (HSTS), redirect HTTP→HTTPS, cookie `Secure`, CORS allowlist (bukan `*` bila credential), CSRF sesuai `05`, header aman (CSP, X-Frame-Options, nosniff).
- Matikan debug (`APP_DEBUG=false`), error page ramah (tanpa stack), log level tepat, rate limit aktif, registrasi terbuka ditutup bila seharusnya invite-only.
- File upload dir verifikasi: tanpa eksekusi script, batas ukuran/tipe aktif, signed URL untuk privat.

## 4. Health Check, Monitoring, dan Smoke Test

- Sediakan endpoint `/health` (app + DB + storage + queue depth) dan `/ready` untuk deploy check. Integrasikan ke uptime monitor + alert.
- Logging terpusat + trace id (lihat `08`) + alert untuk error rate, login gagal massal, job gagal, disk penuh.
- Smoke test pasca-deploy (otomatis/manual tercatat):

```text
[ ] health hijau [ ] login per role [ ] 1 transaksi happy path [ ] upload 1 file
[ ] webhook/payment sandbox [ ] kirim notifikasi [ ] laporan utama terbuka < budget
[ ] tidak ada 500 di log 10 mnt pertama [ ] rollback plan siap
```

## 5. Rollback & Versioning

- Setiap rilis punya tag/versi + changelog (`21`) + daftar migration yang ikut. Strategi rollback:
  - Kode: kembalikan ke tag sebelumnya (blue-green / redeploy cepat).
  - DB: `migrate:rollback` yang teruji ATAU forward-fix bila rollback destruktif (pilih sadar + catat). Jangan rollback buta bila migration menghapus kolom yang sudah dipakai kode baru.
  - Aset/storage: pertahankan 1 versi lama hingga rilis dinyatakan stabil.
- Tentukan pemilik keputusan rollback + ambang (contoh: >5% error 10 mnt / transaksi gagal / login down → rollback tanpa debat).

---

**Gate ke `20`:** staging lolos smoke + backup + rollback teruji + secret aman + health hijau. Tanpa ini, status rilis = DITOLAK.
