# 07 — AUTHORIZATION & SECURITY

## 1. Auth
- Provider: [NextAuth/Auth.js / Supabase Auth / Custom JWT + httpOnly cookie]
- Password: Argon2/bcrypt min cost 12. Session: httpOnly + Secure + SameSite=Lax.
- Wajib: login rate limit, lockout sementara, reset via token 1-jam sekali pakai.

## 2. Matriks RBAC (isi fix)
| Aksi | Guest | User | Admin |
|------|-------|------|-------|
| Baca publik | ✅ | ✅ | ✅ |
| Buat data sendiri | ❌ | ✅ | ✅ |
| Edit milik orang | ❌ | ❌ | ✅ |
| Hapus | ❌ | ❌(milik? ya/tidak) | ✅ |
| Akses /admin | ❌ | ❌ | ✅ |

Aturan ownership: selalu cek di server, bukan hanya di UI.

## 3. Input Security Checklist
- [ ] Zod di semua boundary
- [ ] Escape / sanitasi HTML, CSP header aktif
- [ ] Upload: whitelist mime + size limit + scan + simpan di luar public + nama acak
- [ ] No IDOR: cek kepemilikan tiap `/:id`
- [ ] No mass assignment: whitelist field update

## 4. Headers & Env
- Headers: `HSTS, CSP, X-Frame-Options DENY, X-Content-Type-Options nosniff, Referrer-Policy`.
- Secret hanya di env server (`*_SECRET` tidak boleh `NEXT_PUBLIC_`).
- CORS allowlist domain eksplisit.

## 5. Audit
- Log: login gagal, akses forbidden, ganti role, hapus data.
- Review tiap sprint: daftar admin, token lama, dependensi CVE.

> Gagal di file ini = gagal launch. Tidak bisa ditawar.
