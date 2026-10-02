# 12 — UI AUDIT

Jalankan per halaman sebelum merge.

## 1. Anti-Slop (P0)
- [ ] Tanpa lorem ipsum / John Doe / gradient ungu-biru default
- [ ] Tanpa klaim tanpa bukti ("terbaik", "revolusioner")
- [ ] Ikon konsisten 1 set (lucide/heroicons), bukan emoji campur

## 2. Layout & Responsive (P0)
- [ ] 360px, 768px, 1280px tidak overflow horizontal
- [ ] Tabel → card/scroll di mobile
- [ ] Max-width + padding konsisten dengan 03
- [ ] Tap target ≥44px, jarak antar tombol cukup

## 3. States (P0)
- [ ] Loading skeleton (bukan blank / spinner fullscreen)
- [ ] Empty state: judul + deskripsi + 1 CTA
- [ ] Error state: pesan ID + tombol retry
- [ ] Disabled + loading pada button saat submit

## 4. Form & Copy (P1)
- [ ] Label di atas, error di bawah + aria-describedby
- [ ] Pesan error Bahasa Indonesia jelas: "Email wajib diisi", bukan "Invalid input"
- [ ] Format tanggal/angka Rupiah konsisten (id-ID)

## 5. Aksesibilitas (P1)
- [ ] Kontras ≥4.5:1, focus-visible jelas
- [ ] Alt image bermakna, tombol ikon ada aria-label
- [ ] Navigasi keyboard: Tab order logis, Esc tutup modal
- [ ] Lighthouse Accessibility ≥90

## Format Temuan
`[P0] file.tsx:42 — tombol submit tanpa loading state → tambah disabled+spinner saat isPending`
