# 08 — BACKEND RULES (Engineering Backend)

> File ini mengatur lifecycle request sampai response. Validasi kontrak API di `09`. Security di `05`. Schema di `04`. Jangan menduplikasi aturan tersebut — file ini fokus pada pembagian tanggung jawab dan transaksi.

## 1. Request Lifecycle Baku

Setiap request harus melewati urutan ini, tanpa lompat:

```text
route → middleware (auth, scope/tenant, rate limit, CORS/CSRF)
→ validation (tipe, format, batas, whitelist field)
→ authorization (role + ownership + status) [lihat 05]
→ business logic di service/domain (bukan di controller)
→ akses database via model/query builder dalam transaksi bila perlu
→ audit log untuk aksi kritis
→ response konsisten (lihat 09) + error aman (lihat 05)
→ logging server (tanpa secret/PII berlebih)
```

Dilarang: business logic di middleware, validasi di service yang seharusnya di validation layer, atau query langsung di route tanpa lapisan yang jelas (kecuali read sederhana yang didokumentasikan).

## 2. Pembagian Tanggung Jawab (Anti God Controller)

- **Route:** daftarkan endpoint + middleware saja. Tanpa `if` bisnis.
- **Controller/handler:** tipis — panggil validation + authorization + satu service method, kembalikan response. Target <100 baris logika. Bila membengkak, pecah ke service.
- **Service/domain:** tempat rumus, transisi status, perhitungan uang, orkestrasi multi-model + transaksi. Satu method = satu use-case (`CreateOrder`, `ApproveDiscount`, `CloseBooking`).
- **Model/entity:** relasi, cast, scope query umum, validasi level data (unique). Bukan tempat memanggil HTTP/session atau mengirim notifikasi langsung.
- **Event/queue/notification:** untuk efek samping (kirim email/WA, generate PDF, sinkron eksternal). Jangan blokir request utama untuk pekerjaan berat yang bisa antre — tetapi jangan menambah queue bila belum ada di `01`/`03` (lihat anti-overengineering).

Contoh yang salah: controller 400 baris menghitung total + diskon + pajak + update stok + kirim WA sekaligus tanpa transaksi. Contoh benar: `TransactionService.checkout(dto)` dalam `DB::transaction()` + event `OrderCreated` untuk notifikasi.

## 3. Validasi, Otorisasi, dan Mass Assignment

- Validasi di backend adalah kebenaran: tolak tipe salah, panjang berlebih, enum asing, cross-field tidak konsisten (tanggal kembali < pinjam), dan field tak dikenal bila kontrak ketat.
- Otorisasi setelah validasi, sebelum bisnis: gunakan policy/gate/middleware sesuai framework. Uji dengan matriks `05` Bagian 2.
- Mass assignment: definisikan `fillable` / DTO eksplisit. Contoh larangan: `$model->update($request->all())` yang memungkinkan user mengubah `role`, `price`, `is_paid`.

## 4. Transaksi, Konkurensi, dan Idempotensi

- Bungkus alur multi-tulis (order+items+stok+jurnal) dalam satu transaksi. Tentukan batas transaksi sependek mungkin untuk menghindari lock lama.
- Untuk race (stok, saldo, slot booking): kunci baris (`SELECT ... FOR UPDATE` / pessimistic lock) atau constraint DB (`CHECK stock >= 0` + handling konflik). Jangan andalkan cek di aplikasi lalu tulis tanpa proteksi.
- Sediakan idempotency untuk endpoint yang bisa di-retry (pembayaran, webhook, submit ganda): `Idempotency-Key` + simpan hasil pertama. Cegah double submit di frontend (`07`) + di backend.
- Queue/job harus idempoten (aman dijalankan ulang) + punya retry/backoff + dead-letter handling bila didukung.

## 5. Akses Database & Query

- Gunakan eager loading untuk relasi yang dipakai (lihat `04` Bagian 5). Dilarang query di loop.
- Pagination/filter/sort konsisten dengan `09`. Batasi `per_page`, batasi kolom, hindari `SELECT *` pada tabel lebar.
- Pisahkan read ringan vs laporan berat. Laporan agregat di DB, bukan tarik ribuan baris lalu hitung di memori.
- Soft delete + audit trail sesuai `04` Bagian 7 dan `05` Bagian 5. Hapus permanen hanya dengan persetujuan + backup.

## 6. File, Notifikasi, dan Integrasi

- File: validasi tipe/ukuran, rename acak, simpan via abstraction (local/S3), serve privat via signed URL. Hapus file yatim via job terjadwal, bukan manual.
- Notifikasi (email/WA): via queue/event, template terpusat, jangan bocorkan token/link sensitif di log. Sediakan fallback bila provider down + status pengiriman yang bisa dilacak.
- Integrasi eksternal (`[EDIT: EXTERNAL SERVICE]`): timeout eksplisit, retry terbatas + backoff, circuit breaker sederhana bila kritis, sandbox/mock untuk test, verifikasi webhook signature (lihat `05`).

## 7. Error Handling, Logging, dan Response

- Tangkap error terduga (validasi, not found, forbidden, konflik) menjadi response standar `09` dengan `code` stabil (`VALIDATION_ERROR`, `FORBIDDEN`, `NOT_FOUND`, `CONFLICT`, `RATE_LIMITED`, `INTERNAL_ERROR`).
- Error tak terduga: log lengkap di server (trace id, actor, input ringkas tanpa secret) + kembalikan pesan generik ke user.
- Logging: satu format (level, timestamp UTC, trace id, actor, action, duration, outcome). Jangan log password/token/PII penuh. Sediakan trace id yang juga dikembalikan di header untuk korelasi frontend-backend.
- Rate limiting + caching: terapkan di endpoint mahal/login (lihat `05`/`09`/`17`). Cache hanya data idempoten + invalidasi jelas. Dilarang cache data per-user tanpa key scoping yang benar.

## 8. Larangan Backend Khas AI

- Menaruh seluruh business logic di controller lalu menyebutnya "service".
- Menyebar rumus (diskon/pajak/denda) di 4 tempat (frontend, controller, model, seeder) tanpa sumber tunggal.
- Menambahkan repository + interface + DTO untuk CRUD 1 tabel tanpa alasan di `03`.
- Mengembalikan pesan error teknis SQL ke user.
- Membuat endpoint "super" yang melakukan 5 use-case sekaligus agar "praktis".

---

**Gate:** setiap endpoint harus lolos: validasi + authorization backend + transaksi/concurrency bila relevan + response standar + audit log untuk aksi kritis + test (`13`) sebelum diintegrasikan ke frontend.
