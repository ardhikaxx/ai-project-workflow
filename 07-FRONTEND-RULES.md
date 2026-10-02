# 07 — FRONTEND RULES (Engineering Frontend)

> File ini mengatur rekayasa frontend. Bahasa visual di `06`. Anti-slop di `10`. API contract di `09`. Security boundary di `05` (frontend bukan boundary).
> Sesuaikan dengan `[EDIT: FRONTEND STACK + VERSI]` di `01`. Jangan memaksakan pola SPA ke server-render dan sebaliknya.

## 1. Arsitektur Komponen

- **Hierarki:** `route/page` (data + layout) → `feature component` (logika fitur) → `shared UI` (button, input, table, modal dari design system) → `primitive` (token dari `06`).
- **Ukuran:** hindari dua ekstrem — atomisasi berlebihan (satu file per 10 baris tanpa reuse) dan God component (>300 baris tanpa pemisahan). Pecah bila: ada state/logika independen yang bisa diuji sendiri, atau dipakai di ≥2 tempat.
- **Tanggung jawab:** page mengambil data + menangani state halaman; child fokus render + emit event. Dilarang fetch tersebar tanpa pola (lihat Bagian 3).
- **Proyek existing:** gunakan component + styling system yang sudah ada. Dilarang menambah UI library baru tanpa persetujuan + analisis dampak (bundle size, konsistensi).

## 2. State Management Proporsional

Pilih level terendah yang cukup (anti-overengineering):

1. **Local state** untuk UI ephemeral (open/close, tab, input draft).
2. **Server state via fetch/cache bawaan framework** (React Query / SWR / fetch + cache Next.js / store bawaan) untuk data API: caching, revalidasi, retry, deduplikasi.
3. **Global client state** (zustand/redux/pinia) hanya untuk state lintas halaman yang benar-benar global (auth user, outlet aktif, keranjang, preferensi). Dilarang menyimpan seluruh response API di global store tanpa alasan.
4. **URL sebagai state** untuk search/page/filter/sort yang harus shareable/bookmarkable.

Aturan: bedakan `server state` (sumber di backend, bisa stale) vs `client state` (sumber di browser). Jangan mencampur keduanya dalam satu store tanpa pemisah yang jelas.

## 3. Integrasi API (Kontrak dengan `09`)

- Satu **API client terpusat**: base URL dari env, timeout, retry terbatas untuk GET idempoten, penanganan `401` (redirect login / refresh), `403` (halaman ditolak + sembunyikan aksi), `422` (petakan ke field form), `429`/`500` (pesan + retry aman).
- Jangan hardcode URL per file. Jangan menelan error (`catch {}` kosong). Selalu tampilkan state error/empty/loading sesuai `06`.
- **Form:** validasi client untuk UX cepat + validasi server sebagai kebenaran. Petakan error server per field. Cegah double submit. Untuk file upload: progress, batas `[EDIT: ...]`, preview, dan pesan tipe/ukuran yang jelas.
- **Search/filter/pagination:** debounce 250–400ms, batalkan request basi (AbortController), sinkronkan dengan URL, reset ke halaman 1 saat filter berubah. Max `per_page` ikut `09`.
- **Optimistic update:** hanya untuk aksi kecil yang mudah rollback (toggle, like, checklist) + sediakan rollback eksplisit saat gagal. Dilarang untuk transaksi uang/stok/status kritis — gunakan blocking + konfirmasi.

## 4. Loading, Error, Empty, dan Error Boundary

- Setiap fetch wajib menangani 3 state + 1 pengaman: loading (skeleton), error (retry), empty (CTA), dan error boundary / fallback crash bila framework mendukung agar satu widget rusak tidak meruntuhkan halaman.
- Dilarang menampilkan `undefined`, `NaN`, `Invalid Date`, atau JSON mentah ke user. Format tanggal/angka/mata uang konsisten (locale + timezone dari `01`).
- Log frontend minimal untuk error tak terduga (tanpa PII/token). Detail teknis hanya di console/log server, bukan di UI.

## 5. Responsive, Aksesibilitas, dan Browser

- Implementasikan responsive dari `06`: uji di 360 / 768 / 1280 minimal. Pastikan tidak ada overflow horizontal tak disengaja, tabel punya strategi mobile, dan aksi kritis tetap terjangkau.
- Aksesibilitas implementatif: elemen interaktif memakai tag semantik (`button`, `a`, `label`), fokus tidak hilang setelah modal menutup, kontras diperiksa, target sentuh cukup, navigasi keyboard untuk menu/dialog/combobox.
- Kompatibilitas: tentukan browser baseline di `01` bila perlu (contoh: Chrome/Edge/Safari 2 versi terakhir). Jangan memakai API eksperimental tanpa fallback.

## 6. Performance Frontend

- **Image:** ukuran responsif (`srcset`/`sizes` atau komponen image framework), lazy di bawah fold, placeholder blur/warna, alt sesuai `06`. Dilarang gambar hero 5MB.
- **Bundle:** code splitting per route, lazy untuk modal/chart/editor berat, impor ikon selektif (bukan seluruh library). Ukur sebelum menambah library (lihat `17`).
- **Render:** hindari fetch waterfall berantai yang bisa diparalelkan; memoisisasi hanya bila terbukti berat (jangan premature memo di semua komponen); virtualisasi untuk list sangat panjang bila pagination tidak cukup.
- **Client security limitation:** jangan menyimpan token sensitif di `localStorage` untuk sesi kritis (lihat `05`); jangan menaruh logika harga/diskon final di client — hitung ulang di backend.

## 7. Larangan Frontend yang Sering Dilakukan AI

- Membuat 20 component mungil yang hanya dipakai sekali demi "modularitas".
- Duplikasi logika format/validasi di banyak file tanpa helper terpusat.
- Fetch langsung di 10 tempat tanpa API client + tanpa penanganan error standar.
- Menyimpan permission di client lalu percaya begitu saja (harus dicek backend per `05`).
- Menambah library animasi/chart/state baru untuk satu kebutuhan kecil yang bisa native.

---

**Gate:** setiap fitur frontend harus menunjukkan: state loading/empty/error, validasi konsisten dengan backend, tidak ada console error, dan lolos cek responsive + keyboard dasar sebelum lanjut ke integrasi/testing (`13`).
