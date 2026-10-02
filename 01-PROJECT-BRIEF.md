# 01 — PROJECT BRIEF (Template Konfigurasi Proyek — WAJIB Diisi per Proyek)

> Satu-satunya file yang wajib diedit manusia sebelum AI mulai bekerja. File lain adalah sistem; file ini adalah konfigurasi.
> Cara mengisi: ganti/isi nilai setelah setiap `[EDIT: ...]`. Jangan hapus label `[EDIT:]`. Jika field tidak relevan, tulis `N/A (disengaja)` + alasan.
> Jika placeholder masih kosong, AI wajib berhenti untuk tugas yang bergantung padanya (lihat `00` Bagian 12).

## 1. Identitas Proyek

- **Nama proyek:** `[EDIT: NAMA PROYEK]`
- **Tagline (1 kalimat):** `[EDIT: TAGLINE]`
- **Deskripsi (3–5 kalimat masalah → pengguna → solusi → nilai):** `[EDIT: DESKRIPSI PROYEK]`
- **Jenis aplikasi:** `[EDIT: JENIS APLIKASI — contoh: SaaS / dashboard admin / POS / inventory / rental / klinik / sekolah / kampus / marketplace / CRM / booking / pelayanan publik / company profile+CMS / UMKM / internal system]`
- **Domain bisnis:** `[EDIT: DOMAIN BISNIS + PROSES INTI, contoh: rental alat berat — reservasi, deposit, overtime, denda]`
- **Platform:** `[EDIT: PLATFORM — contoh: web responsive only / web + PWA / desktop-optimized]`
- **Status:** `[EDIT: STATUS — proyek baru dari nol / proyek existing, repo: <url/branch>]`

## 2. Tujuan, Pengguna, Masalah, Solusi

- **Tujuan utama (1–3):** `[EDIT: TUJUAN UTAMA, contoh: menertibkan stok, mempercepat kasir < 60 detik]`
- **Target pengguna (persona + literasi digital):**
  `[EDIT: TARGET PENGGUNA — contoh: Admin 25–40 non-teknis; Owner 40+ hanya butuh laporan; Kasir butuh keyboard-only]`
- **Masalah utama yang diselesaikan:** `[EDIT: MASALAH UTAMA]`
- **Solusi yang ditawarkan (maks 3 bullet):** `[EDIT: SOLUSI 1, 2, 3]`
- **Mengapa sekarang (urgensi):** `[EDIT: URGENSI]`
- **Success metric (terukur):** `[EDIT: SUCCESS METRIC — contoh: checkout < 2 mnt, Lighthouse > 90, 0 bug kritis saat launch]`

## 3. Tech Stack (Wajib Versi Eksplisit)

- **Frontend:** `[EDIT: FRAMEWORK FRONTEND + VERSI — contoh: Next.js 14.2 App Router + React 18 + Tailwind 3.4]`
- **Backend:** `[EDIT: FRAMEWORK BACKEND + VERSI — contoh: Laravel 11 / NestJS 10 / Django 5 / Next.js Route Handler]`
- **Database:** `[EDIT: DATABASE + VERSI — contoh: PostgreSQL 16 / MySQL 8.0 / MongoDB 7]`
- **ORM / Query builder:** `[EDIT: ORM — contoh: Prisma 5 / Eloquent / TypeORM / Drizzle]`
- **Authentication method:** `[EDIT: METODE AUTHENTICATION — contoh: session cookie + CSRF / JWT httpOnly + refresh rotation / OAuth Google]`
- **Storage file:** `[EDIT: STORAGE — contoh: S3-compatible / local disk / Cloudinary]`
- **Cache / Queue / Job (hanya jika benar-benar perlu, lihat anti-overengineering):** `[EDIT: CACHE/QUEUE — contoh: N/A / Redis 7 + queue database]`
- **External service / third-party API:** `[EDIT: EXTERNAL SERVICE — contoh: Midtrans, WA gateway, SMTP, Maps]`
- **Package / library yang disetujui:** `[EDIT: PACKAGE DISETUJUI]`
- **Deployment target:** `[EDIT: DEPLOYMENT — contoh: VPS Ubuntu + Docker / Vercel + Neon / Shared hosting cPanel]`

> Jika masih `[EDIT:]` kosong, AI dilarang menebak stack. AI harus bertanya.

## 4. User Role & Permission Model

- **Daftar role:** `[EDIT: ROLE PENGGUNA — contoh: Superadmin, Admin, Kasir, Owner (read-only laporan), Customer]`
- **Model permission:** `[EDIT: MODEL PERMISSION — contoh: RBAC sederhana role×menu / RBAC + ownership (data milik sendiri) / ABAC]`
- **Ownership rule:** `[EDIT: ATURAN KEPEMILIKAN DATA — contoh: kasir hanya melihat transaksi outlet-nya; owner melihat semua]`
- **Akun default / seeding role:** `[EDIT: AKUN DEFAULT — contoh: superadmin@... (di-seed, wajib ganti password)]`
- **Status akun (aktif/nonaktif/suspend):** `[EDIT: STATUS AKUN + SIAPA YANG BISA MENONAKTIFKAN]`

## 5. Modul & Batasan Scope

### 5.1 In-scope MVP (maks 5–7 modul utama)

- `[EDIT: MODUL 1 + 1 kalimat tujuan]`
- `[EDIT: MODUL 2 + ...]`
- `[EDIT: MODUL 3 + ...]`

### 5.2 Out-of-scope / V2

- `[EDIT: FITUR V2 — contoh: multi-cabang, loyalty point]`

### 5.3 Non-goals (tegas tidak dikerjakan)

- `[EDIT: NON-GOALS — contoh: tidak ada mobile native, tidak ada multi-bahasa di MVP]`

### 5.4 Business Rules Inti (jangan biarkan AI mengarang)

- `[EDIT: ATURAN BISNIS INTI — contoh: denda rental 10%/hari, deposit dikembalikan setelah inspeksi, diskon hanya owner yang bisa approve >10%]`
- `[EDIT: STATUS LIFECYCLE — contoh: booking: draft → confirmed → active → returned → closed; siapa yang bisa transisi]`
- `[EDIT: VALIDASI KRITIS — contoh: stok tidak boleh negatif, tanggal kembali ≥ tanggal pinjam]`
- `[EDIT: PERHITUNGAN — contoh: rumus total, pajak, overtime, pembulatan]`

## 6. Design Direction (Pengendali Anti-Slop)

- **Design direction:** `[EDIT: DESIGN DIRECTION — contoh: admin padat fungsional ala ERP, bukan landing page; dense table, bukan kartu besar-besar]`
- **Referensi visual (URL / screenshot):** `[EDIT: REFERENSI VISUAL]`
- **Tipografi:** `[EDIT: TIPOGRAFI — contoh: Inter, scale 12/14/16/20/24, heading semibold only]`
- **Warna:** `[EDIT: WARNA UTAMA + WARNA SEKUNDER + WARNA SEMANTIK sukses/warning/danger/info — sertakan hex]`
- **Density:** `[EDIT: DENSITY — contoh: compact untuk admin, comfortable untuk customer-facing]`
- **Responsive strategy:** `[EDIT: RESPONSIVE — contoh: desktop-first admin, mobile-first untuk katalog; breakpoint 360/768/1280]`
- **Accessibility requirement:** `[EDIT: A11Y — contoh: keyboard navigable, focus visible, kontras ≥ 4.5:1]`
- **SEO requirement:** `[EDIT: SEO — contoh: full SEO untuk halaman publik, noindex untuk /admin/*]`
- **Pola UI yang dilarang:** `[EDIT: POLA UI YANG DILARANG — contoh: dilarang gradient ungu-biru, glassmorphism, hero blob, fake testimonial]`

Jika bagian ini kosong, AI wajib memakai default konservatif dari `06` + `10` (fungsional, bukan dekoratif) dan menandai `ASUMSI:`.

## 7. Performance, Security, Compliance Target

- **Performance target:** `[EDIT: TARGET PERFORMANCE — contoh: LCP < 2.5s di 4G, list 1000 row pakai pagination server-side]`
- **Security requirement:** `[EDIT: KEBUTUHAN SECURITY — contoh: 2FA opsional, rate limit login, audit log untuk transaksi uang]`
- **Data sensitif:** `[EDIT: DATA SENSITIF — contoh: KTP, rekam medis, password hashing argon2/bcrypt]`
- **Regulasi / compliance:** `[EDIT: REGULASI — contoh: N/A / UU PDP / retensi 5 tahun]`
- **Backup & retensi:** `[EDIT: BACKUP — contoh: daily backup DB, retensi 30 hari]`

## 8. Coding Convention & Forbidden Tech

- **Bahasa, style, linter:** `[EDIT: KONVENSI — contoh: TS strict, ESLint+Prettier, PSR-12, Conventional Commits]`
- **Struktur folder yang diharapkan:** `[EDIT: STRUKTUR FOLDER — contoh: ikuti default framework / custom: modules/...]`
- **Forbidden technology / pattern:** `[EDIT: TEKNOLOGI DILARANG — contoh: dilarang Mongo jika sudah Postgres; dilarang jQuery; dilarang microservice di MVP]`
- **Batasan proyek:** `[EDIT: BATASAN — contoh: shared hosting tanpa Redis, tanpa root access]`

## 9. Things AI Must Not Assume (Dilarang Ditebak AI)

- `[EDIT: HAL YANG TIDAK BOLEH DIASUMSIKAN — contoh: jangan asumsikan tarif, denda, pajak, approval flow, role baru]`
- Setiap item di sini bila ditemui AI wajib bertanya, bukan membuat asumsi aman.

## 10. Things AI Must Preserve (Proyek Existing Wajib Diisi)

- `[EDIT: HAL YANG WAJIB DIPERTAHANKAN — contoh: jangan ubah skema tabel X, jangan redesign sidebar, jangan upgrade Tailwind, jangan rename route /api/v1/...]`
- Jika proyek baru, tulis `N/A — repo kosong`.

## 11. Project-Specific Constraints

- `[EDIT: ATURAN KHUSUS — contoh: outlet A tidak boleh melihat stok outlet B; laporan hanya bisa diekspor owner; jam operasional 08–21 membatasi booking]`

## 12. Known Risks & Open Questions

- **Risiko terbesar:** `[EDIT: RISIKO — teknis + bisnis]`
- **Plan B:** `[EDIT: PLAN B]`
- **Open questions (yang belum diputuskan pemilik):**
  1. `[EDIT: PERTANYAAN TERBUKA 1]`
  2. `[EDIT: PERTANYAAN TERBUKA 2]`

## 13. Definition of Done (Project Level)

Proyek dianggap selesai bila semua terpenuhi (ditambah `20-FINAL-CHECKLIST.md`):

- [ ] Semua modul MVP lolos acceptance criteria di `02`.
- [ ] Tidak ada `[EDIT:]` kritis yang masih kosong tanpa keputusan tercatat.
- [ ] Audit `14–17` tanpa temuan Critical.
- [ ] Test kunci lolos dengan bukti eksekusi (`13`).
- [ ] Deployment + rollback teruji (`19`).
- [ ] Changelog + decision log + handoff terisi (`21`, `22`, `25`).

---

```text
Contoh pengisian cepat:
[EDIT: NAMA PROYEK] SinarPOS
[EDIT: JENIS APLIKASI] POS + inventory untuk UMKM retail 1 outlet
[EDIT: FRONTEND STACK + VERSI] Next.js 14 App Router + Tailwind 3.4
[EDIT: DATABASE] PostgreSQL 16 via Prisma 5
[EDIT: ROLE PENGGUNA] Owner, Admin, Kasir
[EDIT: DESIGN DIRECTION] Kasir super padat, tombol besar, keyboard-first, minim dekorasi
[EDIT: WARNA UTAMA] #0F766E (teal-700), netral slate, danger red-600 only untuk void
```
