# 02 — PRODUCT REQUIREMENTS (Menerjemahkan Bisnis Menjadi Requirement yang Bisa Diuji)

> File ini mengatur BAGAIMANA AI memahami fitur. AI dilarang mengimplementasikan fitur hanya dari nama fitur ("buatkan CRUD produk") tanpa mengisi struktur di bawah.
> Prinsip global di `00` tetap berlaku. File ini hanya menambahkan disiplin requirement.

## 1. Mengapa File Ini Ada

Kegagalan umum AI: membuat tabel + form + tombol simpan, tetapi tidak memahami workflow nyata. Contoh: modul "rental" tanpa konsep deposit, overtime, denda, inspeksi pengembalian — sehingga software tidak bisa dipakai.

Aturan: setiap fitur harus dipahami sebagai **alur kerja pengguna**, bukan sebagai halaman.

## 2. Struktur Wajib per Fitur (Feature Spec)

Salin blok ini untuk setiap fitur di `[EDIT: FITUR]`. Jika satu field tidak relevan, tulis `N/A + alasan`, jangan dikosongkan diam-diam.

```text
[FITUR-XXX] [EDIT: NAMA FITUR] — Prioritas: [EDIT: MUST/SHOULD/COULD] — Modul: [EDIT: MODUL]
1. Konteks & tujuan: siapa memakai, masalah apa yang diselesaikan, nilai bisnis.
2. Actor: [EDIT: ROLE PENGGUNA yang boleh akses] + actor sekunder.
3. Precondition: kondisi yang harus true sebelum fitur bisa dipakai.
4. Trigger: apa yang memulai alur (klik, event, schedule, webhook).
5. Input: field + tipe + required/optional + contoh valid/invalid.
6. Processing & business rule: langkah + rumus + referensi ke [EDIT: ATURAN BISNIS INTI] di 01.
7. State transition: status sebelum → sesudah + siapa boleh transisi + efek samping.
8. Output: apa yang berubah (DB, file, notifikasi, audit log) + apa yang dilihat user.
9. Validasi: batas panjang, format, rentang, uniqueness, cross-field.
10. Authorization: role apa boleh apa + ownership rule + apa yang dicek di backend.
11. Success condition: kondisi objektif fitur disebut berhasil.
12. Error scenario: apa yang terjadi bila validasi gagal / stok kurang / pembayaran gagal.
13. Empty scenario: tampilan + CTA saat belum ada data.
14. Loading scenario: skeleton / spinner / optimistic vs blocking.
15. Edge case: data duplikat, concurrent edit, timezone, hapus data terkait, import massal.
16. Dependency: fitur/API/tabel lain yang harus ada dulu.
17. Acceptance criteria (testable, Given-When-Then).
18. Definition of Done fitur (lihat Bagian 5).
19. Traceability: ID requirement → file implementasi → test ID.
```

Contoh acceptance criteria yang benar:

```text
AC1: Given kasir terotentikasi outlet A, When scan SKU yang stoknya 0,
Then sistem menolak dengan error OUT_OF_STOCK dan tidak membuat transaksi.
AC2: Given owner, When approve diskon >10%, Then audit log mencatat actor+alasan.
```

Contoh yang salah (tidak testable): "tampilan bagus dan responsif".

## 3. User Journey & Business Process (Wajib untuk Alur Uang/Status)

Untuk setiap alur yang melibatkan uang, stok, status, atau approval, AI wajib membuat:

1. Diagram alur tekstual: `draft → confirmed → active → returned → closed (dengan aktor tiap panah)`.
2. Tabel transisi: dari status, ke status, syarat, aktor, efek (contoh: kembalikan deposit, kurangi stok).
3. Daftar aksi yang DILARANG per status (contoh: tidak boleh edit setelah `closed` kecuali Superadmin dengan alasan + audit).

Alasan: tanpa ini AI akan membuat status sebagai string bebas tanpa kontrol, menyebabkan data chaos.

Kapan tidak perlu: halaman statis, profil sederhana, CMS satu arah. Tulis `N/A — tidak ada state lifecycle`.

## 4. Prioritas, MVP, dan Out-of-Scope

Gunakan MoSCoW yang ketat:

- **MUST (MVP):** tanpa ini produk tidak bisa dipakai. Maks 5–7 fitur. Jika lebih, pangkas atau pecah rilis.
- **SHOULD:** penting tapi bisa manual sementara (contoh: export Excel bisa diganti screenshot dulu).
- **COULD / V2:** catat di `01` Bagian 5.2, jangan diimplementasikan diam-diam.
- **WON'T (non-goals):** tegaskan agar AI tidak "berbaik hati" menambahkan (contoh: tidak ada chat realtime di MVP).

AI dilarang memperluas scope atas inisiatif sendiri. Rekomendasi penambahan scope harus diajukan sebagai `REKOMENDASI:` terpisah, bukan langsung dikodekan.

## 5. Definition of Done per Fitur (Mengikat)

Fitur belum DONE bila hanya "halaman bisa dibuka". Wajib:

- [ ] Backend + validasi + authorization backend selesai.
- [ ] Frontend + loading + empty + error + success + konfirmasi aksi destruktif selesai.
- [ ] Validasi konsisten frontend-backend (frontend untuk UX, backend untuk keamanan).
- [ ] Responsive + keyboard + focus state dasar (lihat `06`).
- [ ] Tidak ada console error, tidak merusak fitur lain (regression check).
- [ ] Test relevan ditulis + dijalankan dengan bukti (lihat `13`).
- [ ] Audit log bila menyentuh uang/status/permission (lihat `05`).

AI dilarang menandai DONE tanpa bukti. Gunakan status: `DONE / PARTIAL (sisa: ...) / BLOCKED (butuh: ...)`.

## 6. Traceability (Menghubungkan Requirement → Kode → Test)

Setiap requirement harus bisa dilacak. Format minimal:

```text
REQ-POS-001 (diskon >10% perlu approval owner)
→ impl: backend/TransactionService.php:120, api/transactions:45, frontend/DiscountApproval.tsx:30
→ test: TEST-POS-001, TEST-POS-002
→ audit: 15-BACKEND-AUDIT temuan #3 (fixed)
```

Tanpa traceability, audit final (`20`) tidak bisa memverifikasi coverage. AI wajib memperbarui tabel ini setiap selesai fitur, bukan di akhir proyek.

## 7. Menangani Ambiguitas Requirement

Ikuti `00` Bagian 4. Tambahan khusus file ini:

- Pisahkan kolom `DIPUTUSKAN PEMILIK` vs `ASUMSI AI`. Jangan mencampur.
- Jika `[EDIT: ATURAN BISNIS INTI]` di `01` masih kosong untuk fitur tersebut, AI wajib berhenti dan meminta aturan, bukan mengarang rumus.
- Contoh ambiguitas kritis yang wajib ditanyakan: rumus denda, siapa boleh refund, apakah soft delete atau hard delete, retensi data, timezone laporan.

## 8. Template Siap Salin untuk Proyek Baru

```text
Proyek: [EDIT: NAMA PROYEK] | Modul: [EDIT: MODUL] | Penulis: [EDIT: NAMA] | Tanggal: [EDIT: TANGGAL]

[FITUR-001] [EDIT: NAMA FITUR]
Konteks: [EDIT: ...]
Actor: [EDIT: ROLE PENGGUNA]
Precondition: [EDIT: ...]
Trigger: [EDIT: ...]
Business rule: merujuk 01#[EDIT: ATURAN BISNIS INTI] + detail: [EDIT: ...]
Authorization: [EDIT: ...]
AC1 (Given-When-Then): [EDIT: ...]
AC2: [EDIT: ...]
Edge case: [EDIT: ...]
Dependency: [EDIT: ...]
Prioritas: [EDIT: MUST/SHOULD/COULD/WON'T]
```

---

**Gate ke fase berikutnya:** jangan lanjut ke `03`/`04` bila fitur MUST belum memiliki acceptance criteria testable dan business rule yang jelas sumbernya.
