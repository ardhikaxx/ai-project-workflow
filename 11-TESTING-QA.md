# 11 — TESTING & QA

## 1. Piramida Test
- Unit (vitest/jest): logic service, utils, validasi zod. Target: semua P0.
- Integration: API endpoint + DB (pakai DB test terpisah). Target: semua endpoint P0.
- E2E (playwright): flow kritis saja (register→login→CRUD utama→logout). Max 5-10 skenario.

## 2. Checklist per Fitur P0
- [ ] Happy path lolos
- [ ] Validasi gagal → 400 + pesan jelas
- [ ] Unauthorized (401) & Forbidden (403) teruji
- [ ] Empty + error + loading state terlihat
- [ ] Mobile 360px tidak rusak

## 3. Template QA Manual (copy per rilis)
| ID | Skenario | Langkah | Expected | Hasil |
|----|----------|---------|----------|-------|
| QA-01 | Login gagal | password salah 5x | rate limit + pesan | ⬜ |
| QA-02 | Buat data kosong | submit kosong | error per field | ⬜ |
| QA-03 | Akses data orang | GET /api/x/milik-orang | 403/404 | ⬜ |
| QA-04 | Offline / refresh | matikan net, reload | error + retry | ⬜ |

## 4. Perintah Cepat
```bash
npm run typecheck
npm run lint
npm run test
npx playwright test --ui
```

## 5. Aturan
- Test gagal = fitur belum selesai. Tidak ada "nanti saja".
- Data test terisolasi, tidak pakai DB dev/prod.
- Flaky test diperbaiki langsung, tidak di-skip tanpa issue.
