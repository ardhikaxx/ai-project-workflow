# 03 — ARCHITECTURE (Merancang Arsitektur Sebelum Coding)

> File ini mengatur bagaimana AI memilih dan mendokumentasikan arsitektur. Prinsip global (no rewrite, anti-overengineering, impact analysis) ada di `00` dan tidak diulang penuh di sini.
> Output file ini adalah **implementation plan** yang disetujui sebelum database (`04`) dan kode (`07`/`08`) dibuat.

## 1. Prinsip Pemilihan Arsitektur

Pilih arsitektur berdasarkan, bukan berdasarkan tren:

1. Ukuran aplikasi: jumlah modul MUST di `01`/`02`, estimasi tabel ( <15 = kecil, 15–40 = menengah, >40 = besar).
2. Kompleksitas domain: CRUD datar vs workflow status + uang + approval + integrasi.
3. Beban: jumlah pengguna konkuren, pola baca/tulis, kebutuhan realtime.
4. Deployment di `01`: shared hosting vs VPS vs serverless. Arsitektur harus muat di batasan tersebut.
5. Maintainability: siapa yang merawat (solo dev / tim kecil) dan literasi mereka.

Aturan keras:

- Aplikasi sederhana → arsitektur sederhana (monolith modular, MVC bawaan framework). Jangan minta maaf karena "terlalu sederhana".
- Aplikasi kompleks → modularisasi bertahap per domain, bukan microservice langsung.
- Dilarang menambahkan pattern hanya karena populer. Setiap pattern harus menyelesaikan masalah SEKARANG yang tertulis di `02`.

Contoh keputusan yang benar:

> "CRUD inventory 8 tabel, 30 user internal, deploy cPanel tanpa Redis. Keputusan: Laravel monolith, tanpa queue, tanpa repository layer. Alasan: ... Alternatif dipertimbangkan: ... Konsekuensi: ..."

## 2. Separation of Concerns & Batas Modul

Tentukan secara eksplisit:

- **Domain boundaries:** modul apa saja (contoh: `sales`, `inventory`, `billing`) dan entity milik siapa.
- **Dependency direction:** modul mana boleh memanggil siapa. Contoh: `sales → inventory (kurangi stok)`, tetapi `inventory ↛ sales`. Circular dependency dilarang.
- **Shared kernel:** apa yang boleh di-shared (auth user, audit log, money helper, upload helper) dan apa yang tidak boleh (business logic antar modul).
- **Lapisan backend** (sesuaikan framework, lihat `08`):
  - Route/middleware: hanya routing + auth.
  - Validation: hanya aturan input.
  - Controller/handler: orkestrasi tipis, tanpa rumus bisnis.
  - Service/domain: tempat business rule + transaksi.
  - Model/entity: relasi + scope query + cast, bukan God object.
- **Lapisan frontend** (lihat `07`): route/page, feature component, shared UI, API client, state.

Contoh struktur folder (sesuaikan `[EDIT: STRUKTUR FOLDER]` di `01`, jangan dipaksakan bila framework punya konvensi):

```text
app/
  Modules/Sales/ (controller, service, request, resource)
  Modules/Inventory/
  Shared/ (money, upload, audit)
frontend/
  app/(admin)/sales/
  components/ui/ (design system dari 06)
  lib/api-client.ts
```

Jika proyek existing, struktur existing menang. Dokumentasikan deviasi bila harus berbeda + alasan + dampak.

## 3. Yang Wajib Diputuskan (dan Kapan TIDAK Perlu)

| Keputusan | Wajib diputuskan sekarang | Boleh ditunda / dilarang di MVP |
|---|---|---|
| Auth method + session/token strategy | Ya (lihat `05`) | 2FA penuh bila tidak di `01` |
| Hak akses per modul (RBAC matrix) | Ya | ABAC kompleks |
| Transaksi DB per alur uang/stok | Ya | Saga / distributed transaction |
| Pagination, filter, search strategy | Ya | Full-text engine terpisah |
| File storage + batas ukuran/tipe | Ya | CDN privat kompleks |
| Background job / queue / cron | Hanya bila ada di `01` | Menambahkan "untuk jaga-jaga" |
| Cache | Hanya bila ada bukti beban | Cache prematur |
| Logging, monitoring, health check | Minimal versi `19` | Observability enterprise |
| API versioning | Bila API publik / mobile | Bila hanya web internal |

Setiap "ya" harus menghasilkan bagian di implementation plan. Setiap "tunda" harus tercatat sebagai `DEFERRED:` + syarat kapan dibuka kembali.

## 4. Konfigurasi, Environment, dan Integrasi Eksternal

- Daftar env yang dibutuhkan (tanpa nilai rahasia): `DATABASE_URL`, `[EDIT: ...]`, integrasi `[EDIT: EXTERNAL SERVICE]`.
- Bedakan config per environment: local / staging / production. Apa yang boleh dummy di local, apa yang wajib real di production.
- Untuk setiap integrasi eksternal: timeout, retry, idempotency, fallback bila down, dan cara testing tanpa memukul API asli (mock/sandbox).
- Secret dilarang di kode. Rujuk `05` dan `19` untuk penyimpanan dan rotasi.

## 5. Error Handling & Observability Arsitektural

Tentukan di level arsitektur (implementasi di `08`/`09`/`19`):

- Format error standar (warisi dari `00`/`09`, jangan buat baru per modul).
- Apa yang di-log vs apa yang ditampilkan ke user (pesan user aman, detail teknis hanya di log).
- Audit trail untuk aksi kritis (uang, status, permission) — tabel/ mekanisme + retensi.
- Health check dan readiness untuk dependency (DB, storage, queue bila ada).

## 6. Implementation Plan (Output Wajib File Ini)

Sebelum coding, AI harus menghasilkan dokumen rencana maksimal 2 halaman yang berisi:

```text
Konteks: [EDIT: NAMA PROYEK] — modul [EDIT: MODUL]
Keputusan arsitektur: [monolith modular / ...] + alasan 3 kalimat
Modul & dependency direction: [diagram teks]
File yang akan DIBUAT: [list path]
File yang akan DIUBAH: [list path + perilaku yang dipertahankan]
Yang TIDAK akan dibuat (anti-overengineering): [list + alasan]
Risiko + mitigasi: [2-4 item]
Urutan eksekusi: [DB → backend → frontend → integrasi → test]
Estimasi dampak: [area regression yang harus diuji ulang]
Persetujuan: [ ] Owner / [ ] Tech lead
```

Tanpa persetujuan plan untuk fitur MUST/arsitektur inti, AI dilarang lanjut ke `04`. Untuk fitur kecil non-kritis, approval implisit boleh bila `01` mengizinkan dan perubahan dilabeli `KEPUTUSAN:`.

## 7. Anti-Pattern Arsitektur yang Sering Dibuat AI

- God controller / God component yang tahu segalanya.
- Business logic tersebar acak di frontend + controller + model + trigger DB.
- Abstraksi dini: interface untuk satu implementasi, service untuk pass-through.
- Shared util menjadi tempat sampah logika bisnis.
- Menyalin pola microservice (API gateway, event bus) ke monolith kecil.
- Menambahkan GraphQL / tRPC / gRPC tanpa alasan di `01`/`09`.

Jika AI mengusulkan salah satunya, wajib menyertakan bukti kebutuhan sekarang + biaya. Jika tidak bisa, tolak usulan tersebut.

---

**Gate:** plan disetujui → lanjut ke `04-DATABASE-DESIGN.md`. Jika plan mengubah keputusan jangka panjang, catat juga di `22-DECISION-LOG.md`.
