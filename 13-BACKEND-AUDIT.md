# 13 — BACKEND AUDIT

Jalankan per endpoint sebelum merge.

## 1. Validasi (P0)
- [ ] Zod schema di boundary, pesan error ID
- [ ] Whitelist field update (no mass assignment)
- [ ] Limit + page max 100, file size + mime whitelist

## 2. Auth & Autz (P0)
- [ ] Session dicek di server, bukan hanya di client
- [ ] Ownership check `resource.userId === session.userId` atau role admin
- [ ] Test IDOR: akses milik user lain → 403/404
- [ ] Rate limit login + endpoint publik

## 3. DB & Performa (P1)
- [ ] Tidak ada N+1 (cek query log)
- [ ] Index di FK + filter utama
- [ ] Transaksi untuk multi-write
- [ ] P95 < 300ms untuk endpoint P0 di lokal

## 4. Error & Log (P1)
- [ ] Status code tepat (400/401/403/404/409/422/500)
- [ ] Tidak bocor stack/SQL/secret ke client
- [ ] Log terstruktur ada reqId + userId + duration

## Format Temuan
| Endpoint | Issue | Severity | Fix |
|----------|-------|----------|-----|
| GET /api/orders/:id | tanpa ownership check | P0 | cek order.userId |
| POST /api/upload | tanpa mime check | P0 | whitelist jpg/png max 2MB |
