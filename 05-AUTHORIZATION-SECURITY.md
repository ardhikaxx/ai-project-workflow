# 05 — AUTHORIZATION & SECURITY (Security Playbook)

> Security boundary ada di **backend**. Proteksi frontend (sembunyikan tombol) hanya UX, bukan keamanan. Semua permission penting wajib diverifikasi di backend per request.
> File ini melengkapi `08` (implementasi) dan `15` (audit). Jangan menduplikasi format error (`00`/`09`).

## 1. Authentication (Siapa Kamu)

Sesuaikan dengan `[EDIT: METODE AUTHENTICATION]` di `01`. AI dilarang mengganti method tanpa persetujuan.

- **Session cookie + CSRF:** default aman untuk web monolith. Set `HttpOnly`, `Secure`, `SameSite=Lax/Strict`, expiry pendek, regenerasi ID session saat login (anti fixation).
- **Token (JWT/opaque) + refresh rotation:** untuk SPA/API/mobile. Access token pendek (5–15 mnt), refresh di httpOnly cookie, deteksi reuse (bila refresh dipakai ulang → revoke semua sesi user tersebut).
- **Password:** hashing adaptif (bcrypt/argon2id, bukan MD5/SHA1 polos). Kebijakan sesuai risiko: min length 8–12, cek breach umum, lockout/rate limit, bukan kompleksitas absurd.
- **Reset & verifikasi:** token reset sekali pakai + expiry pendek + invalidate setelah dipakai; enum user tidak bocor (pesan generik "jika email terdaftar..."); verifikasi email bila akun sensitif.
- **Brute force:** rate limiting per IP + per akun, backoff eksponensial, captcha adaptif, log + alert anomali.
- **Logout:** invalidate server-side, hapus cookie/token, tutup semua sesi bila password diganti.

Dilarang: menyimpan password/token di localStorage yang rentan XSS untuk sesi kritis, menaruh secret di kode, atau mengekspos stack trace auth ke user.

## 2. Authorization (Apa yang Boleh Kamu Lakukan)

Bangun matriks eksplisit dari `[EDIT: ROLE PENGGUNA]` + `[EDIT: MODEL PERMISSION]` + ownership di `01`:

```text
Contoh matriks (sesuaikan):
                  | lihat | buat | edit own | edit all | hapus | approve | export
Superadmin        |  Y    |  Y   |    Y     |    Y     |   Y   |    Y    |   Y
Admin outlet A    |  Y(A) |  Y(A)|    Y(A)  |    N     |  N*   |    N    |   Y(A)
Kasir             |  Y(A) |  Y(A)|    N     |    N     |   N   |    N    |   N
Owner (read-only) |  Y(all laporan) ... dst
N* = hanya soft delete draft sendiri, selain itu N
(A) = scope outlet sendiri saja
```

Aturan:

- Cek di backend setiap request: `authenticated? → role allowed? → owns resource? → status allows?`. Urutan ini mencegah IDOR.
- Ownership wajib dicek via query scoped (`WHERE outlet_id = current_outlet`), bukan hanya `find(id)` lalu percaya.
- Mass assignment: whitelist field yang boleh diisi user (`fillable/guarded` + DTO validation). Field sensitif (`role`, `price`, `is_paid`, `owner_id`) tidak boleh diambil dari input mentah.
- IDOR/insecure direct object: uji dengan menukar ID milik orang lain — harus 403/404 aman, bukan bocor.
- Privilege escalation: endpoint update profil dilarang menerima `role`; endpoint admin terpisah + middleware ganda.
- Frontend boleh menyembunyikan UI, tetapi backend tetap menolak. Jangan percaya `isAdmin` dari client.

## 3. Input, Injection, dan File Upload

- **Validasi ganda:** frontend untuk UX, backend untuk keamanan (tipe, panjang, format, rentang, whitelist enum). Tolak unknown field bila kontrak ketat.
- **SQL injection:** hanya ORM/query builder dengan binding. Dilarang string concatenation SQL. Raw query hanya dengan parameter binding + review.
- **XSS:** escape output, CSP header, sanitasi rich text (allowlist tag), `HttpOnly` cookie, hati-hati `dangerouslySetInnerHTML` / `v-html` / `{!! !!}`.
- **CSRF:** token untuk session-cookie flow; `SameSite` + origin check untuk API cookie; tidak perlu CSRF untuk Bearer header murni tetapi tetap cek origin.
- **File upload:** whitelist ekstensi+MIME+magic byte, batas ukuran `[EDIT: ...]`, rename acak, simpan di luar webroot / object storage, scan bila tersedia, blokir eksekusi (no PHP/SVG aktif di direktori publik), path traversal protection (`../` dinetralkan), serve via signed URL bila privat.
- **Path traversal & SSRF:** normalisasi path, allowlist host untuk fetch eksternal, timeout + egress terbatas.
- **Command injection:** dilarang shell dengan input user. Bila perlu, allowlist argumen + tanpa shell.

## 4. API, Session, Secret, dan Transport

- Semua auth di API via header/cookie aman, bukan via query string. Token tidak boleh di-log.
- CORS: allowlist origin eksplisit, bukan `*` bila credential. Method/header minimal.
- HTTPS wajib production (HSTS). Cookie `Secure`. Redirect HTTP→HTTPS.
- Secret di env/secret manager, bukan repo. Bedakan `.env.example` (template) vs `.env` (nyata, gitignored). Rotasi bila bocor.
- Rate limiting: login, reset, OTP, upload, dan endpoint mahal (laporan, export). Kembalikan `429` dengan `Retry-After`.
- Webhook: verifikasi signature (HMAC), timestamp tolerance, idempotency key, tolak replay.
- Error disclosure: pesan user generik ("Terjadi kesalahan"), detail di log server. Dilarang membocorkan SQL, path, env, atau user enumeration via pesan error berbeda.

## 5. Data Sensitif, Logging Aman, dan Audit Trail

- Klasifikasikan `[EDIT: DATA SENSITIF]` di `01`: KTP, kesehatan, password, token, payment. Enkripsi at-rest bila regulasi meminta; masking di UI/log (contoh: `****1234`).
- Log boleh berisi: timestamp, actor, action, entity id, hasil. Log dilarang berisi: password, token, OTP, full PII, full kartu.
- Audit log terpisah + append-only untuk: login/logout, ganti password/role, transaksi uang, perubahan status, hapus/restore, export data. Sertakan `before/after` + alasan bila relevan. Retensi ikut `[EDIT: REGULASI]`.
- Backup terenkripsi + uji restore (lihat `19`). Backup bukan alasan menyimpan data selamanya — patuhi retensi.

## 6. Security Review Questions (Gate per Fitur)

AI wajib menjawab tertulis sebelum fitur dianggap selesai:

1. Siapa boleh akses? Bagaimana backend menolak yang tidak boleh (bukti kode/test)?
2. Bisakah user A mengakses data user B dengan menukar ID (IDOR)? Bagaimana diuji?
3. Field apa yang bisa dimanipulasi dari client (harga, role, owner)? Bagaimana dicegah?
4. Apa yang terjadi bila input jahat (SQLi, XSS, upload shell, path traversal)?
5. Apakah rate limit + lockout + audit log sudah ada untuk endpoint sensitif?
6. Apakah pesan error membocorkan info? Apakah secret aman dari log/repo?
7. Apakah session/token expire + revoke bekerja (termasuk ganti password)?

Jika satu jawaban "belum", status fitur = `PARTIAL`, bukan `DONE`. Temuan audit dicatat di `15` dengan severity.

## 7. Security Testing Minimal

- Test authorization per role (positif + negatif) — lihat `13`.
- Test IDOR manual: login 2 akun berbeda, tukar ID.
- Test mass assignment: kirim field terlarang, pastikan diabaikan/ditolak.
- Test upload: file `.php`, SVG ber-script, file oversize, path traversal.
- Test rate limit: brute force login di staging.
- Dependency scan + header check (CSP, HSTS, X-Frame-Options) sebelum rilis — lihat `20`.

---

**Rujukan:** implementasi di `08`/`09`, testing di `13`, audit di `15`, deploy di `19`. Jika `01` menyatakan aplikasi internal tanpa data sensitif, terapkan baseline tetap, jelaskan pengurangan secara eksplisit + risiko yang diterima pemilik.
