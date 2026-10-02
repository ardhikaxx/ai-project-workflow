# 16 — DEPLOYMENT

## 1. Env Matrix
| Var | Local | Staging | Prod | Public? |
|-----|-------|---------|------|---------|
| DATABASE_URL | ✅ | ✅ | ✅ | ❌ |
| NEXTAUTH_SECRET | ✅ | ✅ | ✅ | ❌ |
| NEXT_PUBLIC_APP_URL | ✅ | ✅ | ✅ | ✅ |

- `.env.local` tidak di-commit. Contoh ada di `.env.example` tanpa nilai asli.
- Kunci prod dibuat ulang, tidak copy dari dev.

## 2. Pre-Deploy Checklist
- [ ] `npm run typecheck && lint && test && build` hijau lokal
- [ ] Migration dry-run lolos di staging
- [ ] Backup DB prod sebelum migrate destruktif
- [ ] Smoke E2E 5 skenario kritis hijau di staging

## 3. Deploy Steps (contoh Vercel + Postgres)
1. Push ke `staging` → auto deploy preview → QA manual.
2. Backup prod: `pg_dump ...`.
3. Merge `staging` → `main` → deploy prod.
4. Jalankan migration prod.
5. Verifikasi: `/api/health`, login, 1 transaksi utama.

## 4. Rollback
- DB: restore backup + down migration yang sudah disiapkan.
- App: revert ke deployment sebelumnya (Vercel: instant rollback).
- Punya kontak on-call + status page sederhana.

## 5. Post-Deploy
- [ ] Cek log error 30 menit pertama
- [ ] Lighthouse prod ≥90
- [ ] Monitor: uptime, 5xx rate, P95 latency
