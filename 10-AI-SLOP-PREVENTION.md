# 10 — AI SLOP PREVENTION (Mencegah Output Generik ala Generator Otomatis)

> File ini adalah "rem" untuk `06` (visual), `07`/`08` (kode), dan `18` (data). Jika ragu apakah sesuatu adalah slop, jawab: apakah elemen ini membantu user menyelesaikan tugas nyata? Jika tidak, hapus.
> AI wajib mempertimbangkan referensi di `[EDIT: REFERENSI VISUAL]` + aplikasi existing sebelum menentukan desain. Preferensi AI tidak boleh mengalahkan keduanya.

## 1. Definisi AI Slop dalam Konteks Website

- **Visual slop:** dekorasi tanpa fungsi — gradient ungu-biru di admin, glassmorphism di semua card, blob/glow, shadow tebal, rounded raksasa, ikon oversize, animasi masuk di setiap scroll.
- **Content slop:** copy generik ("Selamat datang di solusi inovatif..."), fitur palsu ("AI-powered..."), testimoni/logo palsu, metrik karangan ("10.000+ pengguna puas").
- **Architecture slop:** layer, service, library, microservice yang ditambahkan karena "best practice" tanpa masalah nyata (lihat `00`/`03`).
- **Code slop:** component/abstraksi sekali pakai, util duplikat, dependency untuk 5 baris yang bisa native, komentar yang mengulang kode, TODO tanpa issue.
- **Fake-data slop:** chart naik sempurna, tabel "User 1..10", email `test@test.com`, harga acak, tanggal tidak kronologis yang membuat demo terlihat palsu (lihat `18`).
- **UX slop:** 5 CTA bersaing, badge "New/Pro" di mana-mana, hero 2 layar untuk aplikasi internal, 3 level modal bertumpuk, tombol yang tidak melakukan apa-apa.

## 2. Audit Deteksi Slop (Jalankan Tiap Selesai UI/Fitur)

Tandai YA/TIDAK + bukti screenshot/file:baris. Satu YA = temuan yang harus dibenahi atau diberi alasan tertulis.

**Visual:**

- [ ] Apakah ada >2 gradient / glass panel tanpa alasan produk?
- [ ] Apakah ada card/kartu yang isinya bisa menjadi baris tabel atau teks biasa?
- [ ] Apakah ada chart yang tidak menjawab pertanyaan pengguna (lihat `02`)?
- [ ] Apakah ada warna/badge acak per baris tanpa makna status?
- [ ] Apakah spacing/radius/shadow konsisten dengan token `06`?
- [ ] Apakah ada animasi yang menunda tugas atau menggeser layout?

**Content:**

- [ ] Apakah ada copy generik yang bisa ditempel ke aplikasi lain tanpa berubah?
- [ ] Apakah ada testimoni/logo/mitra/metrik tanpa sumber di `01`/`02`?
- [ ] Apakah empty state memberi langkah berikutnya (bukan "No data")?

**Code/dependency:**

- [ ] Apakah ada dependency baru yang bisa diganti native 10–20 baris?
- [ ] Apakah ada component/abstraksi yang hanya dipakai sekali tanpa rencana reuse?
- [ ] Apakah ada duplikasi rumus/format/validasi di ≥2 tempat?

Jika YA tanpa alasan produk tertulis, hapus/sederhanakan. Kesederhanaan yang fungsional lebih baik daripada "modern" yang mengganggu.

## 3. Aturan Keputusan Desain (Agar Tidak "Terlihat Modern" Tanpa Alasan)

Sebelum menambahkan elemen visual, tulis 1 kalimat:

> "Elemen X membantu [persona di 01] melakukan [tugas di 02] lebih [cepat/jelas/aman] karena [alasan]."

Contoh benar: "Badge merah hanya untuk status overdue membantu kasir memprioritaskan penagihan." Contoh salah: "Gradient agar terlihat premium."

- Maksimal: 1 aksi primer per layar, 1 warna aksen (`[EDIT: WARNA UTAMA]`), 1 set ikon, 2 weight font dominan.
- Chart hanya bila: (a) ada di `02` sebagai kebutuhan, (b) datanya real dari query `04`, (c) sumbu/label jelas + empty state. Selain itu gunakan angka + tabel.
- Ilustrasi/stock photo hanya bila membantu pemahaman; untuk admin padat, teks + tabel yang baik lebih berharga daripada hero image.

## 4. Larangan Pola Generik (Kecuali Diminta Eksplisit di `01`)

Dilarang tanpa perintah tertulis di `[EDIT: DESIGN DIRECTION]` / `[EDIT: POLA UI YANG DILARANG]` yang mengizinkan:

- Hero generik (headline besar + sub generik + 2 tombol + blob + logo palsu) untuk aplikasi internal/admin.
- Dashboard "4 kartu statistik + chart acak" yang angkanya tidak terhubung ke business rule.
- Pricing/testimoni/partner section palsu.
- Dark glass navbar + gradient border + glow button di aplikasi operasional.
- Lorem ipsum, `User 1`, `test@test.com` di UI yang bisa terlihat user (lihat `18` untuk data dev yang benar).

Bila pengguna memang meminta gaya dekoratif (landing publik), tetap terapkan token `06` + batasi 1 momen dekoratif per halaman, bukan di semua section.

## 5. Cara Memperbaiki yang Sudah Terlanjur Slop (Recovery Visual)

1. Kunci dulu referensi + token (`01` + `06`). Jangan redesign total tanpa persetujuan (lihat `00` preserve).
2. Hapus dekorasi lapis demi lapis (gradient → shadow → animasi → badge) sambil screenshot tiap langkah.
3. Kembalikan hierarki: tentukan 1 H1 + 1 aksi primer per layar, rapikan spacing ke scale `06`.
4. Ganti fake data dengan data real/realistis dari `18` atau kosongkan dengan empty state yang benar.
5. Verifikasi dengan `14-UI-AUDIT.md`. Catat keputusan di `22` bila arah visual berubah.

---

**Gate:** tidak ada temuan slop kritis (fake data publik, chart palsu, redesign tanpa izin) sebelum lanjut ke testing (`13`) / rilis (`20`).
