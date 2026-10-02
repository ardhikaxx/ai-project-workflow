# AI Fullstack Project Workflow

## Prompt Bundle & Workflow AI untuk Membangun Website Fullstack

Repository ini merupakan kumpulan system prompt, aturan pengembangan, workflow, checklist, dan template yang dirancang untuk membantu AI coding tools seperti Cursor, Claude Code, Gemini, ChatGPT, atau coding agent lainnya membangun aplikasi website fullstack secara terstruktur, konsisten, realistis, aman, dan siap dikembangkan.

Workflow ini tidak ditujukan hanya untuk satu jenis aplikasi. Template dapat digunakan untuk berbagai proyek seperti sistem informasi, dashboard administrasi, POS, inventory, rental, klinik, sekolah, kampus, UMKM, marketplace, CRM, SaaS, booking, sistem pelayanan publik, website perusahaan, CMS, dan aplikasi internal organisasi.

Tujuan utama workflow ini bukan membuat AI menghasilkan kode sebanyak mungkin, tetapi membuat AI memahami produk terlebih dahulu, kemudian membangun sistem secara bertahap berdasarkan kebutuhan nyata.

---

## Prinsip Utama

AI harus bertindak sebagai software engineer dan product engineer, bukan sekadar generator kode.

AI harus memahami:

* tujuan aplikasi
* pengguna aplikasi
* masalah yang diselesaikan
* business flow
* role dan permission
* struktur database
* hubungan antar data
* kebutuhan UI/UX
* validasi
* security
* error handling
* empty state
* loading state
* responsive behavior
* accessibility
* performance
* testing
* deployment

AI tidak boleh langsung membuat seluruh aplikasi hanya berdasarkan satu prompt besar tanpa melakukan analisis terlebih dahulu.

---

## Urutan Workflow

Gunakan file dalam urutan berikut:

1. `00-MASTER-SYSTEM.md`
2. `01-PROJECT-BRIEF.md`
3. `02-PRODUCT-REQUIREMENTS.md`
4. `03-UI-UX-DESIGN-SYSTEM.md`
5. `04-FRONTEND-RULES.md`
6. `05-BACKEND-RULES.md`
7. `06-DATABASE-RULES.md`
8. `07-AUTHORIZATION-SECURITY.md`
9. `08-AI-SLOP-PREVENTION.md`
10. `09-CODING-WORKFLOW.md`
11. `10-AI-PROMPT-TEMPLATES.md`
12. `11-TESTING-QA.md`
13. `12-UI-AUDIT.md`
14. `13-BACKEND-AUDIT.md`
15. `14-PERFORMANCE-SEO.md`
16. `15-DATA-SEEDER.md`
17. `16-DEPLOYMENT.md`
18. `17-FINAL-CHECKLIST.md`

Tidak semua file harus digunakan secara literal. Sesuaikan bagian yang ditandai dengan `[EDIT]`.

---

## Cara Menggunakan

Sebelum memulai proyek, isi:

`01-PROJECT-BRIEF.md`

Kemudian definisikan requirement dalam:

`02-PRODUCT-REQUIREMENTS.md`

Setelah itu tentukan visual direction:

`03-UI-UX-DESIGN-SYSTEM.md`

Selanjutnya gunakan rules:

* frontend
* backend
* database
* security

Barulah masuk ke:

`09-CODING-WORKFLOW.md`

AI harus mengerjakan aplikasi secara bertahap.

Contohnya:

```text
ANALYZE
↓
PLAN
↓
DATABASE
↓
BACKEND
↓
AUTHENTICATION
↓
CORE FEATURES
↓
FRONTEND
↓
INTEGRATION
↓
VALIDATION
↓
TESTING
↓
AUDIT
↓
POLISH
```

---

## Filosofi

Jangan meminta AI:

> "Buatkan website lengkap."

Lebih baik:

> "Analisis requirement terlebih dahulu. Identifikasi seluruh entity, role, business flow, edge case, dan dependency. Jangan menulis kode sebelum architecture plan selesai."

Kemudian:

> "Implementasikan modul authentication berdasarkan architecture yang sudah disepakati."

Kemudian:

> "Implementasikan modul X tanpa mengubah behavior modul sebelumnya."

Dengan pendekatan tersebut AI lebih kecil kemungkinannya menghasilkan kode yang tidak konsisten.

---

## Prinsip Anti AI Slop

Website harus terlihat seperti produk yang sengaja dirancang manusia.

Hindari:

* gradient berlebihan
* glassmorphism tanpa alasan
* terlalu banyak rounded card
* dashboard penuh card yang tidak memiliki fungsi
* warna random
* typography random
* icon yang tidak konsisten
* tombol terlalu banyak
* badge yang tidak diperlukan
* empty state generik
* lorem ipsum
* dummy text yang terlihat palsu
* animasi berlebihan
* shadow berlebihan
* layout template yang terlalu generik
* hero section yang tidak relevan
* "AI-generated looking UI"

UI harus memiliki hierarchy, spacing, density, visual rhythm, dan alasan desain yang jelas.

---

## Aturan Penting

Jangan mengubah requirement hanya karena AI merasa ada pendekatan yang lebih mudah.

Jika menemukan requirement yang ambigu:

1. identifikasi ambiguity
2. buat asumsi paling masuk akal
3. dokumentasikan asumsi
4. lanjutkan implementasi hanya jika tidak mengubah business logic utama

Jika requirement bertentangan, prioritaskan:

```text
Explicit User Requirement
>
Business Rule
>
Security
>
Data Integrity
>
Existing Architecture
>
Developer Convenience
```

---

## Definition of Done

Sebuah fitur belum dianggap selesai hanya karena kode berhasil dijalankan.

Fitur dianggap selesai jika:

* UI selesai
* backend selesai
* database selesai
* validation selesai
* authorization selesai
* error handling selesai
* empty state selesai
* loading state selesai
* responsive selesai
* accessibility dasar selesai
* test selesai
* security diperiksa
* query diperiksa
* tidak menghasilkan console error
* tidak menghasilkan broken UI
* tidak menghasilkan duplicate logic
* tidak merusak fitur lain

---

## Final Principle

AI harus selalu berpikir:

> "Apakah fitur ini benar-benar dibutuhkan oleh pengguna, apakah implementasinya konsisten dengan sistem, dan apakah hasil akhirnya terlihat seperti software production-ready?"

Bukan:

> "Bagaimana saya bisa menghasilkan kode sebanyak mungkin?"
