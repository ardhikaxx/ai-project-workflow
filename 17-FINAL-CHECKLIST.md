# 17 — FINAL CHECKLIST (Gate Sebelum Launch)

Semua harus ✅. Satu ❌ = tidak launch.

## 1. Produk
- [ ] Semua fitur P0 di 02 DONE + acceptance terpenuhi
- [ ] Tidak ada lorem ipsum / data dummy di prod
- [ ] Sitemap + navigasi + 404 + 403 + 500 page ada

## 2. UI/UX (lihat 12)
- [ ] Responsive 360/768/1280 lolos, tanpa overflow
- [ ] Loading/empty/error + retry di semua halaman P0
- [ ] Aksesibilitas dasar + Lighthouse A11y ≥90

## 3. Backend (lihat 13)
- [ ] Semua endpoint P0: validasi + auth + ownership + rate limit login
- [ ] IDOR tested, error tidak bocor, log rapi
- [ ] Backup + migration rollback siap

## 4. Kualitas (lihat 11,14)
- [ ] Typecheck + lint + test hijau
- [ ] E2E 5 flow kritis hijau
- [ ] LCP <2.5s, CLS <0.1, Performance ≥90

## 5. Security (lihat 07)
- [ ] Secret di env server, headers security aktif, CORS allowlist
- [ ] Upload aman, RBAC sesuai matriks, audit log aktif

## 6. Rilis
- [ ] `.env.example` update, README cara jalanin + seed ada
- [ ] Smoke test prod: register → login → fitur utama → logout
- [ ] Tanda tangan: Owner ... / Dev ... / Tanggal ...

> Setelah launch: pantau 24 jam pertama, catat bug ke backlog, jangan hotfix panik tanpa test.
