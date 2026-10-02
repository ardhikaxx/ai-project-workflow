# 06 — UI/UX DESIGN SYSTEM (Kualitas Visual ala Desainer Manusia)

> File ini mengatur BAHASA VISUAL. Implementasi komponen ada di `07`. Pencegahan slop ada di `10`. Audit ada di `14`.
> Semua nilai di bawah mengikuti `[EDIT: DESIGN DIRECTION]`, `[EDIT: WARNA UTAMA]`, `[EDIT: TIPOGRAFI]` di `01`. Jika `01` kosong, gunakan default konservatif di sini + labeli `ASUMSI:`.

## 1. Prinsip Desain

1. **Fungsi dulu, dekorasi belakangan.** Setiap elemen harus menjawab: membantu user apa? Jika jawabannya "agar terlihat modern", hapus.
2. **Satu hierarki per layar.** Satu H1 visual, satu aksi primer, sisanya sekunder/tersier. Dua tombol primer berdampingan = kegagalan hierarki.
3. **Konsistensi > kreativitas per halaman.** Radius, shadow, spacing, warna sukses/danger harus sama di seluruh aplikasi.
4. **Density sesuai konteks:** admin/ERP padat (`[EDIT: DENSITY]` compact), customer-facing lega. Jangan samakan keduanya.
5. **Hormati referensi:** jika `01` memberi referensi visual / aplikasi existing, ikuti ritmenya. Jangan menimpa dengan gaya AI generik.

## 2. Token Fondasi (Wajib Didefinisikan Sekali)

Definisikan di satu tempat (CSS variable / Tailwind config / theme), bukan tersebar per file:

- **Spacing scale (4pt base):** `4, 8, 12, 16, 24, 32, 48, 64`. Dilarang angka acak (`13px`, `27px`) tanpa alasan.
- **Tipografi:** family dari `[EDIT: TIPOGRAFI]`, scale terbatas (contoh: `12 / 14 / 16 / 20 / 24 / 32`), weight terbatas (`regular 400, medium 500, semibold 600`), line-height `1.4–1.6` body, tracking normal kecuali caption.
- **Warna semantik** (jangan pakai warna brand untuk status):
  `[EDIT: WARNA UTAMA]` untuk aksi primer + navigasi aktif saja.
  Netral (slate/zinc) untuk surface/teks/border.
  Sukses / warning / danger / info masing-masing SATU warna + SATU background tint.
  Dilarang warna acak per card untuk "variasi".
- **Surface, border, radius, shadow:**
  Radius kecil konsisten (`6–10px` untuk admin; lebih besar hanya bila `[EDIT: DESIGN DIRECTION]` meminta). Satu level shadow untuk raised, satu untuk overlay. Border `1px` netral, bukan multi-border dekoratif.
- **Ikonografi:** satu set ikon, satu ketebalan stroke, ukuran `16/20/24`. Ikon harus bermakna, bukan dekorasi. Dilarang emoji sebagai ikon aksi.

Contoh token:

```css
:root {
  --space-1: 4px; --space-2: 8px; --space-3: 12px; --space-4: 16px; --space-6: 24px;
  --font-body: [EDIT: TIPOGRAFI];
  --color-primary: [EDIT: WARNA UTAMA];
  --color-success: #15803d; --color-warning: #b45309; --color-danger: #b91c1c;
  --radius-md: 8px; --shadow-card: 0 1px 2px rgb(0 0 0 / 0.06);
}
```

## 3. Layout, Navigasi, dan Information Architecture

- **Sidebar/navbar/breadcrumb:** satu pola navigasi global. Breadcrumb untuk depth ≥2. Pagination untuk list, bukan infinite scroll kecuali feed.
- **Page header:** judul + deskripsi 1 baris + aksi primer (maks 1) + sekunder. Dilarang header tanpa aksi yang memaksa user menebak langkah berikutnya.
- **Search/filter/sort:** pola tunggal di semua list (search kiri, filter collapsible, sort di header tabel). Debounce search (lihat `07`). Filter aktif harus terlihat + bisa di-reset.
- **Tabel (admin):** header sticky, alignment kanan untuk angka, status sebagai badge teks (bukan warna-warni acak), aksi baris konsisten (view/edit/delete), bulk action dengan konfirmasi. Kolom harus punya alasan; sembunyikan kolom sekunder di mobile menjadi detail drawer.
- **Form:** label selalu terlihat (bukan placeholder-only), urutan logis, grouping per section, validasi inline per field + summary di atas bila banyak, tombol submit disable saat submitting + cegah double submit.
- **Modal/drawer:** untuk konfirmasi dan form pendek. Form panjang → halaman sendiri. Modal wajib: fokus trap, ESC close, backdrop click yang aman (jangan tutup bila form kotor tanpa konfirmasi).
- **Toast/alert/badge:** toast untuk hasil aksi (sukses/gagal), alert inline untuk konteks halaman, badge hanya untuk status/count penting. Dilarang badge "New!" / "Pro" dekoratif.

## 4. State UI yang Wajib (Bukan Opsional)

Setiap fitur yang menampilkan data wajib merancang 6 state (lihat juga `02` dan `07`):

1. Loading (skeleton yang meniru layout akhir, bukan spinner fullscreen bila bisa dihindari).
2. Empty (ilustrasi minimal + 1 kalimat + 1 CTA yang relevan, bukan "No data found" polos).
3. Error (pesan manusiawi + aksi retry + ID error untuk lapor, bukan stack trace).
4. Success (konfirmasi + langkah berikutnya).
5. Confirmation (untuk hapus, refund, approve — sebutkan konsekuensi).
6. Partial/offline (data stale, mode read-only, antrean retry bila PWA).

AI dilarang menyerahkan fitur tanpa state 1–3 minimal.

## 5. Responsive & Accessibility Baseline

- **Responsive:** dari `[EDIT: RESPONSIVE]` di `01`. Tabel padat → card/stack di mobile atau kolom prioritas + horizontal scroll terkontrol. Touch target ≥44px. Jangan sembunyikan aksi kritis di hover-only (tidak ada hover di sentuh).
- **A11y:** kontras teks ≥4.5:1, fokus visible, semua aksi bisa keyboard (Tab/Enter/ESC), label terasosiasi, heading hierarkis (`h1` satu per halaman), alt bermakna untuk gambar informatif + alt kosong untuk dekorasi, motion yang menghormati `prefers-reduced-motion`.
- **Microinteraction:** transisi 120–200ms untuk hover/focus, tanpa animasi masuk berlebihan. Animasi tidak boleh menunda tugas atau menggeser layout (CLS).

## 6. Larangan Visual (Diperkuat di `10`)

Tanpa perintah eksplisit di `01`, AI dilarang:

- Gradient heavy (terutama ungu-biru), glassmorphism di mana-mana, blob/glow acak.
- Card bulat raksasa berderet tanpa fungsi, bayangan tebal bertumpuk.
- Tipografi hero raksasa untuk aplikasi admin/internal.
- Chart/metrik/testimoni/logo palsu untuk mengisi kekosongan.
- Warna badge acak per baris, ikon oversize, animasi berlebihan.

Setiap keputusan visual yang menyimpang dari token harus beralasan tertulis: masalah pengguna apa yang diselesaikan.

---

**Gate:** sebelum coding UI, pastikan token + pola navigasi + state sudah disepakati. Setelah implementasi, jalankan `14-UI-AUDIT.md` + `10-AI-SLOP-PREVENTION.md`.
