# 05 — BACKEND RULES

## 1. Arsitektur
- Style: [REST / tRPC / Server Actions] — pilih 1 konsisten.
- Layer: `route/controller → service → repo/db`. Logic bisnis di service, bukan di route.
- Response standar:
```json
{ "success": true, "data": {} }
{ "success": false, "message": "Pesan ID yang jelas", "code": "NOT_FOUND" }
```

## 2. Validasi & Error
- Semua input divalidasi dengan Zod/Valibot di boundary (API + Server Action).
- Gunakan kode error stabil: `VALIDATION_ERROR, UNAUTHORIZED, FORBIDDEN, NOT_FOUND, CONFLICT, RATE_LIMITED, INTERNAL_ERROR`.
- Jangan bocorkan stack trace / query SQL ke client.
- Idempotency untuk POST penting (payment, create order) via `Idempotency-Key`.

## 3. Auth & RBAC (detail di 07)
- Cek session di server setiap request sensitif.
- Cek ownership: `resource.userId === session.userId` ATAU role cukup.
- Rate limit login + endpoint publik.

## 4. DB Access
- Gunakan ORM/query builder resmi (Prisma/Drizzle). Dilarang string SQL mentah kecuali perlu + parameterized.
- Selalu paginasi: `?page&limit` (max 100). Default 20.
- N+1 query dilarang. Gunakan include/join + index.

## 5. Kualitas
- Timeout + retry untuk call eksternal. Circuit breaker jika kritis.
- Logging terstruktur: `reqId, userId, route, duration, errorCode`.
- Test: unit untuk service, integration untuk endpoint P0.

## 6. Checklist per Endpoint
- [ ] Zod schema + pesan ID
- [ ] Auth + authorization check
- [ ] 200/201 sukses, 400/401/403/404/409/422/500 tepat
- [ ] Log + tidak bocor data sensitif
