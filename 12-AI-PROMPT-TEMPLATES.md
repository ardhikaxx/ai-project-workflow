# 12 — AI PROMPT TEMPLATES (Kumpulan Prompt Reusable Siap Tempel)

> Cara pakai: salin blok prompt, ganti `[EDIT: ...]`, tempel ke Cursor / Claude Code / Gemini / ChatGPT. Setiap prompt sudah menanamkan: analisis dulu, jaga existing, hindari perubahan tak terkait, verifikasi setelah implementasi.
> Prasyarat: `00` sudah ditempel sebagai system instruction + `01` sudah diisi. Tabel konteks minimal ada di `README` Bagian 5 dan `24`.

## Aturan Umum Semua Prompt

- Satu prompt = satu tujuan. Jangan gabungkan "implementasi + refactor + redesign".
- Selalu minta: inspeksi → rencana → eksekusi kecil → verifikasi + laporan `BERUBAH:` bila ada.
- Selalu larang: tebakan business logic kritis, silent change, klaim tanpa bukti.

---

## P1 — Repository Discovery (Proyek Existing)

```text
Baca 00-MASTER-SYSTEM.md, 01-PROJECT-BRIEF.md, 24-CONTEXT-MANAGEMENT.md.
Lakukan repository inspection penuh TANPA mengubah kode:
stack + versi, struktur folder, routing, auth, DB schema, styling system,
dependency, test, dan konvensi.
Output: ringkasan 1 halaman + daftar file berisiko + 3 open questions maksimal.
Jangan coding pada langkah ini.
```

## P2 — Project Analysis (Proyek Baru / Kosong)

```text
Baca 00 + 01 + 02-PRODUCT-REQUIREMENTS.md.
KONDISI: [EDIT: repo kosong / sudah ada skeleton].
Validasi kelengkapan 01: field [EDIT:] apa yang masih kosong dan memblokir?
Usulkan urutan MVP + apa yang DEFERRED + alasan anti-overengineering.
Jangan membuat file kode sebelum plan disetujui.
```

## P3 — Architecture Planning

```text
Baca 00 + 01 + 02 + 03-ARCHITECTURE.md.
Buat implementation plan untuk [EDIT: FITUR/MODUL]:
batas modul, dependency direction, file DIBUAT vs DIUBAH,
yang TIDAK dibuat + alasan, risiko + mitigasi, urutan eksekusi.
Jika keputusan berdampak jangka panjang, draft decision log (lihat 22).
Minta persetujuan sebelum lanjut ke database/coding.
```

## P4 — Database Planning

```text
Baca 00 + 01 + 02 + 04-DATABASE-DESIGN.md.
Rancang untuk [EDIT: MODUL]: entity, atribut, relasi, ownership,
constraint/unique, onDelete, enum vs lookup, index, soft delete vs hard,
transaksi + risiko race, strategi N+1/pagination.
Output: definisi tabel + migration plan berurutan + rollback.
Jangan buat migration sebelum desain disetujui.
```

## P5 — Feature / Backend Implementation

```text
Baca 00 + 01 + 02 + 08-BACKEND-RULES.md + 09-API-RULES.md + 05-AUTHORIZATION-SECURITY.md.
Implementasikan [EDIT: FITUR/ENDPOINT] sesuai plan yang disetujui:
validation → authorization backend → service + transaksi → audit log
→ response/error standar. Ikuti konvensi existing, jangan ubah fitur lain.
Laporkan: file:baris yang diubah, BERUBAH: (bila ada perubahan kontrak/schema),
perintah verifikasi + hasil nyata, asumsi, dan sisa.
```

## P6 — Frontend Implementation

```text
Baca 00 + 01 + 02 + 06-UI-UX-DESIGN-SYSTEM.md + 07-FRONTEND-RULES.md + 10-AI-SLOP-PREVENTION.md.
Implementasikan UI [EDIT: FITUR] mengikuti [EDIT: DESIGN DIRECTION] dan token existing.
Sertakan loading/empty/error/success/confirm. Gunakan API client terpusat,
debounce search, cegah double submit. DILARANG redesign global / tambah UI lib
/ ubah visual identity tanpa izin. Laporkan verifikasi: console, responsive 360/768/1280,
keyboard dasar.
```

## P7 — Authentication & Authorization

```text
Baca 00 + 01 + 05-AUTHORIZATION-SECURITY.md + 08 + 09.
Implementasikan/verifikasi [EDIT: METODE AUTHENTICATION] + matriks [EDIT: ROLE PENGGUNA]:
login/logout, session/token, password policy, reset, rate limit, RBAC + ownership,
mass assignment guard, IDOR test (2 akun tukar ID), audit log.
Jawab 7 review questions di 05 Bagian 6 dengan bukti. Jangan mengandalkan proteksi frontend.
```

## P8 — API Implementation / Contract Change

```text
Baca 00 + 09 + 08 + 05.
Buat/ubah kontrak untuk [EDIT: RESOURCE]: naming, method, status code,
request/response/error standar, pagination/filter/sort, auth per endpoint,
versioning bila breaking, idempotency bila kritis.
Jika kontrak berubah: list semua consumer yang diperbarui + update docs/api
+ tandai BERUBAH:. Jangan silent change.
```

## P9 — Bug Investigation (Bukan Tambal Acak)

```text
Baca 00 + 23-ERROR-RECOVERY.md + file relevan (jangan seluruh repo).
Selidiki [EDIT: DESKRIPSI BUG + LANGKAH REPRO + LOG]: root cause, file:baris,
mengapa terjadi, fix minimal + regression test, dan apa yang TIDAK diubah.
Jika sudah 3x gagal menambal, hentikan dan usulkan rollback / pendekatan berbeda.
```

## P10 — Debugging Session

```text
Reproduksi [EDIT: BUG] dengan langkah: [EDIT: ...].
Tunjukkan: input, expected, actual, log/error, hipotesis 1-3 + cara membuktikan tiap hipotesis.
Jangan mengubah kode sebelum hipotesis terkuat didukung bukti.
Setelah fix: perintah test + hasil + area regression yang dicek.
```

## P11 — Refactoring Terkendali

```text
Baca 00 + 03 + 23 + test relevan.
Refactor [EDIT: FILE/MODUL] dengan tujuan [EDIT: ...] TANPA mengubah behavior:
tulis perilaku yang dipertahankan, change impact analysis, langkah kecil reversible,
dan test sebelum/sesudah. Laporkan metrik (kompleksitas, duplikasi, query count).
Hentikan bila test gagal dan kembalikan ke titik hijau terakhir.
```

## P12 — UI Improvement (Tanpa Redesign Liar)

```text
Baca 00 + 06 + 10 + 14-UI-AUDIT.md.
Perbaiki UI [EDIT: HALAMAN] berdasarkan temuan [EDIT: ...]:
jaga token + layout global, fokus hierarki/spacing/konsistensi/state.
Sebelum/sesudah screenshot. Dilarang menambah gradient/glass/animasi/metrics palsu.
Verifikasi responsive + keyboard + kontras dasar.
```

## P13 — Responsive / Accessibility Audit

```text
Baca 00 + 06 + 14 + 17-PERFORMANCE-SEO.md (bagian relevan).
Audit [EDIT: HALAMAN] di 360/768/1280 + keyboard-only + cek kontras/fokus/label.
Klasifikasikan temuan + fix minimal. Jangan ubah visual direction.
```

## P14 — Security Audit

```text
Baca 00 + 05 + 15-BACKEND-AUDIT.md.
Audit [EDIT: FITUR/ENDPOINT]: auth, RBAC+ownership, IDOR, mass assignment,
validasi, upload, rate limit, secret/log exposure, audit trail.
Klasifikasikan Critical/High/Medium/Low. Jangan auto-fix Critical tanpa izin.
Sertakan cara mereproduksi tiap temuan.
```

## P15 — Database Audit

```text
Baca 00 + 04 + 16-DATABASE-AUDIT.md.
Audit schema + query [EDIT: MODUL]: relasi/onDelete, constraint, index,
N+1, pagination, SELECT *, transaksi/race, soft delete, orphan, retensi.
Sertakan EXPLAIN untuk query berat + rekomendasi index yang proporsional.
```

## P16 — Performance Audit

```text
Baca 00 + 17-PERFORMANCE-SEO.md + 07 + 08.
Ukur [EDIT: HALAMAN/ENDPOINT]: LCP/CLS/bundle/image (frontend),
query count + p95 latency (backend), EXPLAIN (DB).
Tetapkan budget, perbaiki 3 penghambat terbesar, laporkan sebelum/sesudah.
Jangan optimasi prematur di luar budget.
```

## P17 — Code Review

```text
Review perubahan [EDIT: PR/COMMIT/FILE] terhadap 00 + 01 + 02 AC + 05 + 08/07 + 10:
correctness, security, data integrity, konsistensi konvensi, slop, test.
Output: temuan Critical/High/Medium/Low + saran fix minimal + apa yang sudah bagus.
Jangan me-review gaya di luar konvensi proyek.
```

## P18 — Testing & Test Failure Analysis

```text
Baca 00 + 02 AC + 13-TESTING-QA.md.
Buat/jalankan test untuk [EDIT: FITUR]: happy + validation + authz + edge.
Perintah: [EDIT: PERINTAH TEST]. Laporkan hasil nyata (bukan klaim).
Jika gagal: log penuh, hipotesis, fix, dan ulangi. Tandai test yang di-skip + alasan.
```

## P19 — Deployment Preparation

```text
Baca 00 + 01 + 19-DEPLOYMENT.md + 20-FINAL-CHECKLIST.md (bagian deploy).
Siapkan rilis [EDIT: VERSI]: env/secret, build, migration + rollback, backup,
storage/queue/mail, HTTPS/CORS, health check, smoke test, rencana rollback.
Lakukan dry-run di staging. Jangan deploy tanpa backup + persetujuan.
```

## P20 — Final Production Audit

```text
Baca 00 + 20-FINAL-CHECKLIST.md + 21 + 22 + 25.
Verifikasi [EDIT: NAMA PROYEK] sebagai release gate: requirement coverage,
DB/backend/frontend/auth/security/UI/responsive/a11y/test/perf/SEO/deploy/monitoring/docs.
Output: LULUS / LULUS BERSYARAT (sisa: ...) / DITOLAK (alasan: ...).
Tulis changelog + decision log + handoff summary.
Dilarang menandai LULUS bila ada Critical terbuka atau tanpa bukti test.
```
