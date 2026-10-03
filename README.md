# AI Fullstack Development Operating System — Prompt Bundle & AI Coding Workflow

> Bundle dokumentasi Markdown yang berfungsi sebagai **operating system untuk AI coding agent** dalam membangun aplikasi website fullstack dari nol sampai production-ready. Berlaku generik untuk sistem informasi, dashboard admin, SaaS, POS, inventory, rental, klinik, sekolah, kampus, marketplace, CRM, booking system, pelayanan publik, company profile + CMS, aplikasi UMKM, dan internal management system.

## 1. Apa Ini dan Apa yang Bukan

**Ini adalah sistem kerja (development playbook), bukan sekadar kumpulan prompt.**

Bundle ini mengontrol perilaku AI selama software development:

- Bagaimana AI berpikir (lihat `00-MASTER-SYSTEM.md`).
- Apa yang boleh diasumsikan dan apa yang wajib ditanyakan.
- Urutan kerja yang benar: discovery → requirement → arsitektur → database → backend → auth → frontend → integrasi → testing → audit → optimasi → rilis.
- Standar kualitas untuk database, backend, API, frontend, UI/UX, security, testing, seeder, dan deployment.
- Mekanisme audit, anti-AI-slop, anti-overengineering, change impact analysis, context management, handoff, decision log, dan error recovery.

**Yang bukan:**

- Bukan template kode untuk satu framework.
- Bukan boilerplate yang di-copy paste.
- Bukan daftar prompt "buatkan aplikasi lengkap dalam satu klik".
- Bukan pengganti keputusan bisnis dari pemilik produk.

Prinsip inti:

> AI bertugas menghasilkan software yang benar-benar bisa dipakai, bukan kode sebanyak mungkin.

## 2. Kapan Menggunakan Bundle Ini

Gunakan bundle ini ketika:

1. Memulai proyek fullstack baru dari nol.
2. Melanjutkan atau memperbaiki proyek existing dengan AI tanpa merusak yang sudah ada.
3. Bekerja dengan AI coding tools seperti Cursor, Claude Code, Gemini, ChatGPT Coding Agent, atau agent lain.
4. Tim kecil yang membutuhkan disiplin arsitektur, security, dan QA tanpa birokrasi berat.
5. Proyek yang harus lolos audit production (security, performance, data integrity).

Jangan gunakan bundle ini secara membabi buta untuk:

- Script sekali pakai / prototipe throwaway — gunakan subset saja (`00` + `01` + `11`).
- Tugas non-coding murni (penulisan artikel, desain grafis).

## 3. Struktur Folder dan Fungsi Setiap File

Semua file berada di folder yang sama dengan repository (disarankan di `docs/ai-os/` atau root bila repo baru). Jangan menggabungkan isi menjadi satu file.

```text
docs/ai-os/
├── QUICKSTART-VIBE-CODER.md    # Panduan instan 60 detik untuk Vibe Coder & pemula
├── README.md                   # Entry point manusia + AI (file ini)
├── 00-MASTER-SYSTEM.md         # System prompt global, berlaku untuk semua file
├── 01-PROJECT-BRIEF.md         # Konfigurasi proyek, satu-satunya file yang WAJIB diedit per proyek
├── 02-PRODUCT-REQUIREMENTS.md  # Cara menerjemahkan bisnis menjadi requirement + acceptance criteria
├── 03-ARCHITECTURE.md          # Perancangan arsitektur modular tanpa overengineering
├── 04-DATABASE-DESIGN.md       # Database planning: entity, relasi, index, migrasi, anti N+1
├── 05-AUTHORIZATION-SECURITY.md # Security playbook: auth, authorization, OWASP, audit log
├── 06-UI-UX-DESIGN-SYSTEM.md   # Design system: hierarchy, spacing, tipografi, state
├── 07-FRONTEND-RULES.md        # Engineering frontend: komponen, state, API integration
├── 08-BACKEND-RULES.md         # Engineering backend: lifecycle, service layer, transaksi
├── 09-API-RULES.md             # API design: REST/RPC, versioning, error format, pagination
├── 10-AI-SLOP-PREVENTION.md    # Pencegahan visual/code/content/dependency slop
├── 11-CODING-WORKFLOW.md       # Workflow fase demi fase + gate antar fase
├── 12-AI-PROMPT-TEMPLATES.md   # Prompt reusable siap tempel ke AI agent
├── 13-TESTING-QA.md            # Strategi testing: pyramid, scenario, regression
├── 14-UI-AUDIT.md              # Checklist + prompt audit UI
├── 15-BACKEND-AUDIT.md         # Checklist + prompt audit backend (severity-based)
├── 16-DATABASE-AUDIT.md       # Checklist + prompt audit database & query
├── 17-PERFORMANCE-SEO.md       # Performance budget + SEO proporsional
├── 18-DATA-SEEDER.md           # Data sintetis realistis, relasional, kronologis
├── 19-DEPLOYMENT.md            # Deployment, env, migrasi, rollback, observability
├── 20-FINAL-CHECKLIST.md       # Release gate sebelum production
├── 21-CHANGELOG-WORKFLOW.md    # Cara mencatat perubahan tanpa noisy commit
├── 22-DECISION-LOG.md          # Arsitektur Decision Record (ADR) ringan
├── 23-ERROR-RECOVERY.md        # Recovery ketika AI merusak / hallucinasi / regression
├── 24-CONTEXT-MANAGEMENT.md    # Manajemen konteks agar AI tidak amnesia di proyek besar
└── 25-AI-HANDOFF.md            # Format handoff antar AI agent / sesi
```

Tanggung jawab tiap file sengaja dibuat tidak tumpang tindih:

- Aturan global hanya ada di `00`. File lain mereferensikannya, tidak menyalinnya.
- `01` adalah konfigurasi. File lain adalah sistem.
- `02–09` adalah aturan bangun. `13–17` adalah aturan uji. `19–20` adalah aturan rilis.
- `21–25` adalah aturan rawat (maintainability workflow).

## 4. Cara Menggunakan pada Proyek Baru dari Nol

Langkah baku (estimasi 30–60 menit setup):

**Langkah 1 — Copy bundle.**

```text
# Opsi A: repo baru
git init [EDIT: NAMA PROYEK]
cp -r docs/ai-os /path/to/repo-baru/docs/ai-os

# Opsi B: monorepo — taruh di docs/ai-os, bukan root service
```

**Langkah 2 — Edit hanya `01-PROJECT-BRIEF.md`.**

Ganti semua placeholder `[EDIT: ...]`. Jangan mengedit file lain kecuali ada alasan arsitektural. Contoh placeholder:

```text
[EDIT: NAMA PROYEK]
[EDIT: JENIS APLIKASI]
[EDIT: FRONTEND STACK + VERSI]
[EDIT: BACKEND STACK + VERSI]
[EDIT: DATABASE + VERSI]
[EDIT: METODE AUTHENTICATION]
[EDIT: ROLE PENGGUNA]
[EDIT: ATURAN BISNIS INTI]
[EDIT: DESIGN DIRECTION]
[EDIT: WARNA UTAMA + WARNA SEKUNDER]
[EDIT: POLA UI YANG DILARANG]
[EDIT: BATASAN PROYEK / FORBIDDEN TECH]
```

Aturan placeholder:

- AI harus memperlakukan `[EDIT: ...]` sebagai konfigurasi yang belum diisi, bukan nilai sebenarnya.
- Jika placeholder masih kosong saat AI mulai bekerja, AI wajib berhenti dan meminta pengisian — kecuali file `01` menyatakan field tersebut opsional.
- Manusia dilarang menghapus label `[EDIT:]` sebelum diisi. Isi nilainya, biarkan label sebagai jejak audit. Contoh: `[EDIT: DATABASE] PostgreSQL 16`.

**Langkah 3 — Tempel system prompt ke AI.**

Tempel `00-MASTER-SYSTEM.md` sebagai system instruction / custom instruction / project rules (tergantung tool):

- Cursor: `Settings → Rules → Project Rules` atau `.cursorrules` yang me-referensikan `docs/ai-os/00-MASTER-SYSTEM.md`.
- Claude Code: `CLAUDE.md` di root yang berisi `baca docs/ai-os/00-MASTER-SYSTEM.md terlebih dahulu`.
- ChatGPT / Gemini: tempel isi `00` di awal percakapan, lalu upload `01`.

**Langkah 4 — Jalankan workflow bertahap (lihat `11`).**

Jangan pernah meminta "buatkan semuanya". Ikuti contoh urutan di Bagian 7.

## 5. File Mana yang Harus Dibaca AI

AI tidak perlu membaca 26 file sekaligus. Gunakan prinsip **minimal sufficient context** (detail di `24-CONTEXT-MANAGEMENT.md`):

| Tugas AI | File wajib dibaca |
|---|---|
| Semua tugas | `00` + `01` |
| Merancang fitur baru | `00` + `01` + `02` + `03` |
| Mendesain database | `00` + `01` + `02` + `04` + `16` (audit) |
| Implementasi backend | `00` + `01` + `02` + `08` + `09` + `05` |
| Implementasi frontend | `00` + `01` + `02` + `06` + `07` + `10` |
| Mendesain API | `00` + `09` + `08` + `05` |
| Audit UI | `00` + `06` + `10` + `14` |
| Audit backend | `00` + `08` + `05` + `15` |
| Testing | `00` + `02` + `13` |
| Seeder | `00` + `04` + `18` |
| Deploy / rilis | `00` + `19` + `20` |
| Lanjut sesi / ganti AI | `00` + `01` + `25` + `22` + `24` |
| AI merusak kode | `00` + `23` |

Instruksikan AI dengan kalimat eksplisit, contoh:

> "Baca `00-MASTER-SYSTEM.md`, `01-PROJECT-BRIEF.md`, `04-DATABASE-DESIGN.md`. Lalu rancang skema database untuk modul [EDIT: FITUR]. Jangan coding sebelum rencana disetujui."

Kumpulan kalimat siap pakai ada di `12-AI-PROMPT-TEMPLATES.md`.

## 6. Adaptasi untuk Framework Berbeda

Bundle ini framework-agnostic. `01-PROJECT-BRIEF.md` menentukan stack konkret. AI wajib mengikuti stack tersebut, bukan preferensinya.

Contoh adaptasi:

- Laravel + Blade + MySQL: abaikan aturan code-splitting SPA di `07`, terapkan aturan server-render + progressive enhancement. `09-API-RULES.md` hanya berlaku untuk endpoint yang benar-benar API, bukan web route.
- Next.js App Router + Prisma + PostgreSQL: patuhi Server Component vs Client Component, Server Actions vs Route Handler sesuai `03` dan `09`.
- Django + HTMX: tidak perlu state management library; gunakan aturan local state + server state di `07` secara proporsional.
- Express + Vue + MongoDB: sesuaikan aturan migrasi di `04` menjadi skema validasi + index management karena NoSQL.

Yang tidak boleh berubah antar framework:

- Disiplin requirement (`02`), security boundary di backend (`05`), anti-slop (`10`), impact analysis (`00`), dan release gate (`20`).

Jika stack di `01` masih `[EDIT: ...]` kosong, AI dilarang menebak framework. AI harus bertanya.

## 7. Contoh Workflow Lengkap (Discovery → Finalization)

Contoh urutan prompt (salin dari `12`):

```text
1. DISCOVERY
"Baca 00 + 01. Lakukan repository inspection. Ringkas stack, struktur folder,
konvensi routing/auth/styling, dan list open questions. Jangan coding."

2. REQUIREMENT
"Baca 02. Ubah [EDIT: FITUR] menjadi requirement lengkap: actor, precondition,
trigger, business rule, acceptance criteria, edge case. Tandai ambiguitas."

3. ARCHITECTURE
"Baca 03. Buat implementation plan: modul, dependency direction, file yang
akan dibuat/diubah, dan apa yang TIDAK akan dibuat (anti-overengineering)."

4. DATABASE
"Baca 04. Rancang entity, relasi, constraint, index, dan migration plan.
Jelaskan risiko N+1 dan strategi eager loading / pagination."

5. BACKEND
"Baca 08 + 09 + 05. Implementasikan endpoint [EDIT: FITUR] dengan validasi,
authorization backend, transaksi, dan error format standar. Jangan ubah fitur lain."

6. AUTH
"Baca 05. Implementasikan [EDIT: METODE AUTHENTICATION] + RBAC
[EDIT: ROLE PENGGUNA]. Verifikasi IDOR, mass assignment, dan rate limiting."

7. FRONTEND
"Baca 06 + 07 + 10. Implementasikan UI [EDIT: FITUR] mengikuti design direction
[EDIT: DESIGN DIRECTION]. Sertakan loading/empty/error state. Jangan redesign global."

8. INTEGRATION
"Integrasikan frontend ↔ API. Tangani 401/403/422/500. Tambahkan optimistic update
hanya jika aman. Verifikasi tidak ada silent change pada field/route."

9. TESTING
"Baca 13. Buat dan jalankan test untuk happy path + validation + authorization +
edge case. Laporkan hasil test nyata, bukan klaim."

10. AUDIT
"Jalankan 14 + 15 + 16 + 10. Klasifikasikan temuan Critical/High/Medium/Low.
Jangan auto-fix Critical tanpa persetujuan."

11. REFACTOR + OPTIMASI
"Baca 17 + 23 bila perlu. Perbaiki temuan High ke atas. Ukur sebelum/sesudah
(query count, bundle size, Lighthouse)."

12. FINALISASI
"Baca 19 + 20 + 21 + 22 + 25. Verifikasi release gate, tulis changelog,
decision log, dan handoff summary."
```

Setiap fase memiliki **entry criteria, exit criteria, dan risiko jika dilewati** — lihat `11-CODING-WORKFLOW.md`.

## 8. Menggunakan pada Proyek Existing

Aturan keras untuk proyek existing (diperkuat di `00`):

1. **Inspection dulu.** AI wajib membaca struktur folder, konfigurasi, route, model, migration, auth, dan styling sebelum mengubah apa pun.
2. **Preserve by default.** Jangan redesign UI, jangan ganti library, jangan rename field/route, jangan upgrade major version kecuali diminta eksplisit di `01` bagian "Things AI Must Preserve" dan "Project-Specific Constraints".
3. **Ikuti konvensi existing** meskipun AI mengetahui pola yang "lebih modern".
4. **Perubahan kecil terkontrol.** Satu PR / satu sesi = satu tujuan. Sertakan change impact analysis.
5. **No Silent Changes.** Setiap perubahan behavior, schema, permission, atau visual identity harus dilaporkan eksplisit.

Prompt pembuka untuk proyek existing:

> "Baca 00 + 01 + 24. Lakukan repository inspection penuh. Petakan arsitektur existing, konvensi, dan area berisiko. Jangan mengubah kode apa pun pada langkah ini."

## 9. Prinsip Efisiensi vs Correctness

- Prioritaskan **correctness** di atas speed untuk business logic, security, dan data model.
- AI boleh mengambil keputusan teknis minor tanpa konfirmasi (nama variabel lokal, urutan import, detail styling yang masih dalam design token) selama tidak mengubah business logic, security model, data model utama, atau visual direction.
- AI wajib meminta klarifikasi bila ambiguitas berdampak pada business logic, pricing, permission, atau retensi data.
- AI dilarang mengklaim fitur selesai / test lolos tanpa bukti eksekusi.

## 10. Cara Merawat Bundle Ini

- `01` boleh sering diubah (per proyek). `00` dan `11` jarang diubah.
- Jika menemukan aturan yang bertentangan, `00` menang. Laporkan konflik sebagai issue.
- Catat perubahan bundle di `21-CHANGELOG-WORKFLOW.md`, keputusan arsitektural di `22-DECISION-LOG.md`.
- Internal consistency review terakhir bundle ini: tidak ada aturan bertentangan; terminologi konsisten; `10` konsisten dengan `06`; `05` konsisten dengan `08`; `04` konsisten dengan `18`; `13` konsisten dengan `20`.

---

**Mulai dari sini:** isi `01-PROJECT-BRIEF.md`, lalu ikuti `11-CODING-WORKFLOW.md` fase demi fase.
