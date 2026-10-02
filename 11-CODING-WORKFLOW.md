# 11 — CODING WORKFLOW (Urutan Kerja Fase demi Fase)

> File ini mengatur URUTAN dan GATE. Detail tiap fase ada di file masing-masing. Prinsip `00` (inspeksi, anti-overengineering, no silent changes) mengikat semua fase.
> Aturan emas: **jangan mengerjakan seluruh proyek dalam satu generasi panjang.** Pecah per fase agar konsisten dan mudah diverifikasi.

## 1. Peta Fase

```text
F0 Discovery (inspeksi repo + stack) — [00, 24]
F1 Requirement analysis (02) — output: feature spec + AC + ambiguitas
F2 Architecture planning (03) — output: implementation plan disetujui
F3 Database planning + migration (04) — output: schema + migration reversible
F4 Backend implementation (08+09) — output: endpoint + validasi + transaksi
F5 Authentication & authorization (05) — output: login + RBAC + audit log
F6 Frontend implementation (06+07+10) — output: UI + state lengkap
F7 Integration (07+09) — output: frontend↔API + error mapping
F8 Testing & QA (13) — output: bukti test lolos
F9 Audit (10+14+15+16+17) — output: temuan severity + rencana fix
F10 Optimization & refactor (17+23 bila perlu) — output: ukur sebelum/sesudah
F11 Deployment prep (19) — output: env + migrasi + backup + rollback teruji
F12 Finalization (20+21+22+25) — output: release gate + changelog + handoff
```

Untuk bugfix kecil, boleh ringkas (F0 → F4/F6 → F8 → F12) tetapi tetap tulis impact analysis + test. Untuk prototipe throwaway, cukup F0–F2 + catatan `PROTOTIPE — bukan production`.

## 2. Detail Tiap Fase (Tujuan, Input, Output, Gate, Risiko)

### F0 — Discovery / Inspection
- Tujuan: memahami existing sebelum menyentuh apa pun.
- Input: `00` + `01` (+ repo).
- Output: ringkasan stack+versi, struktur folder, konvensi, area berisiko, open questions. Tanpa kode.
- Gate: pemilik menyetujui pemahaman AI (atau AI menandai `ASUMSI:` untuk yang aman).
- Risiko bila dilewati: merusak auth/styling/schema karena menebak.

### F1 — Requirement Analysis
- Tujuan: mengubah `[EDIT: FITUR]` menjadi spec testable (`02`).
- Output: feature spec + AC Given-When-Then + prioritas + traceability awal.
- Gate: AC disetujui; ambiguitas kritis sudah ditanyakan.
- Risiko: fitur "jadi" tetapi tidak bisa dipakai karena business rule salah.

### F2 — Architecture Planning
- Tujuan: memutuskan batas modul + file yang dibuat/diubah + yang TIDAK dibuat (`03`).
- Output: implementation plan 1–2 halaman + persetujuan.
- Gate: plan disetujui; keputusan jangka panjang dicatat di `22`.
- Risiko: overengineering atau God object bila dilewati.

### F3 — Database
- Tujuan: schema + constraint + index + migration plan (`04`).
- Output: migration reversible + rollback plan + strategi N+1/pagination.
- Gate: desain disetujui; `EXPLAIN` untuk query berat bila ada.
- Risiko: relasi salah, N+1, data yatim, migrasi destruktif.

### F4 — Backend
- Tujuan: endpoint + service + transaksi + error standar (`08`+`09`).
- Output: endpoint teruji manual + logika terpusat di service.
- Gate: validasi + transaksi + error format + audit log kritis.
- Risiko: logika tersebar, tanpa transaksi untuk uang/stok.

### F5 — Auth & Authorization
- Tujuan: login + session/token + RBAC + ownership (`05`).
- Output: matriks permission teruji (positif+negatif) + rate limit.
- Gate: semua pertanyaan review `05` Bagian 6 terjawab YA dengan bukti.
- Risiko: IDOR, escalation, session fixation bila digabung sembarangan dengan F4.

### F6 — Frontend
- Tujuan: UI sesuai `06` + `07` + bebas slop (`10`).
- Output: halaman + loading/empty/error/success/confirm + responsive dasar.
- Gate: tidak ada console error; keyboard + mobile dasar lolos.
- Risiko: redesign global tanpa izin; fake data lolos ke demo.

### F7 — Integration
- Tujuan: kontrak frontend↔API klop (`09`), error mapping (`401/403/422/429/500`), tanpa silent change kontrak.
- Output: alur end-to-end jalan + dokumentasi perubahan kontrak bila ada.
- Gate: semua consumer diperbarui bila kontrak berubah; test integrasi lolos.
- Risiko: field/route berubah diam-diam.

### F8 — Testing
- Tujuan: bukti, bukan klaim (`13`).
- Output: hasil perintah test + coverage area kritis + bug yang ditemukan.
- Gate: happy + validation + authorization + edge minimal lolos.
- Risiko: klaim "berhasil" tanpa eksekusi (dilarang `00`).

### F9 — Audit
- Tujuan: temukan masalah sebelum user menemukannya (`14`+`15`+`16`+`10`+`17`).
- Output: daftar temuan Critical/High/Medium/Low + rencana fix (tanpa auto-fix Critical sebelum izin).
- Gate: tidak ada Critical terbuka untuk rilis.
- Risiko: audit dilewati → slop + lubang keamanan lolos.

### F10 — Optimization & Refactor
- Tujuan: perbaiki temuan High ke atas + ukur dampak (query count, bundle, Lighthouse).
- Output: sebelum/sesudah + test regression.
- Gate: tidak ada regression; perbaikan kecil terkontrol (lihat `23` bila rusak).
- Risiko: "optimasi" yang merusak correctness.

### F11 — Deployment Prep
- Tujuan: env + build + migrasi + backup + rollback + observability (`19`).
- Output: checklist deploy + hasil dry-run staging.
- Gate: rollback teruji; secret aman; health check hijau.
- Risiko: deploy tanpa backup/migrasi plan.

### F12 — Finalization
- Tujuan: release gate (`20`) + changelog (`21`) + decision log (`22`) + handoff (`25`).
- Output: status DONE/PARTIAL/BLOCKED yang jujur.
- Gate: semua MUST traceable ke test + audit.
- Risiko: "selesai" karena halaman bisa dibuka.

## 3. Aturan Perpindahan Fase

- Setiap fase memiliki **exit criteria** di atas. AI dilarang maju bila gate belum terpenuhi, kecuali pemilik memberi waiver tertulis + risiko dicatat.
- Satu fase = satu fokus. Dilarang mencampur "sekalian refactor auth + redesign sidebar + upgrade dependency" dalam satu langkah.
- Setiap perubahan file penting wajib menyertakan change impact analysis (`00` Bagian 7) dan laporan `BERUBAH:` bila ada silent-change candidate.
- Konteks per fase minimal (lihat `24`): baca hanya file yang relevan + file yang akan diubah, bukan seluruh repo membabi buta.

## 4. Prompt Penggerak Fase (Ringkasan — Lengkap di `12`)

```text
"F0: Baca 00+01+24. Inspeksi repo, jangan coding."
"F1: Baca 02. Ubah [EDIT: FITUR] menjadi spec + AC. Tandai ambiguitas."
"F2: Baca 03. Buat implementation plan + apa yang TIDAK dibuat."
"F3: Baca 04. Rancang schema + migration plan. Jelaskan N+1 & pagination."
"F4+F5: Baca 08+09+05. Implementasikan endpoint + RBAC + audit log."
"F6: Baca 06+07+10. Bangun UI + 6 state. Jangan redesign global."
"F7: Integrasikan + petakan error. Laporkan perubahan kontrak."
"F8: Baca 13. Tulis + jalankan test. Laporkan perintah + hasil nyata."
"F9: Jalankan 14+15+16+10. Klasifikasikan Critical/High/Medium/Low."
"F10: Perbaiki High+. Ukur sebelum/sesudah."
"F11+F12: Baca 19+20+21+22+25. Verifikasi rilis + tulis handoff."
```

---

**Catatan:** jika AI terjebak menambal error berulang (>3x) di fase mana pun, hentikan dan pindah ke `23-ERROR-RECOVERY.md`.
