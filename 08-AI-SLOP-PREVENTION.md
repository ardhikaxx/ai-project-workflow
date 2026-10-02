# 08 — AI SLOP PREVENTION

Daftar pola generik AI yang DILARANG + penggantinya.

## 1. Visual Slop → Dilarang
- ❌ Gradient ungu-biru `#6366F1→#A855F7`, glassmorphism ungu, background blob acak.
- ✅ Gunakan token di `03`. 1 warna primer dominan, 1 aksen.

- ❌ Hero: "Selamat Datang di Platform Kami yang Revolusioner" + 2 tombol generik.
- ✅ Hero: 1 kalimat manfaat + 1 CTA + bukti (testimoni/angka/logo).

- ❌ 3 kartu fitur dengan ikon generik (🚀 ⚡ 💡) + teks "Mudah & Cepat".
- ✅ Fitur spesifik dengan screenshot/data nyata + contoh kasus.

## 2. Copy Slop → Dilarang
- Kata terlarang tanpa bukti: revolusioner, cutting-edge, seamless, delights, elevate, unlock, supercharge.
- Setiap klaim wajib bukti: angka, nama, tanggal, atau hapus klaimnya.

## 3. UX Slop → Dilarang
- ❌ Lorem ipsum, avatar placeholder, "John Doe", `test@test.com` di UI final.
- ❌ Tabel desktop yang hancur di mobile.
- ❌ Form tanpa label, tombol tanpa loading, hapus tanpa konfirmasi.
- ✅ Lihat `12-UI-AUDIT.md` sebelum selesai.

## 4. Code Slop → Dilarang
- ❌ `any`, `// @ts-ignore`, `console.log`, kode mati, duplikasi 3x.
- ❌ `useEffect` fetch tanpa cleanup, tanpa dependency benar.
- ❌ API tanpa validasi, tanpa auth check.
- ✅ Ikuti `04, 05, 06`.

## 5. Prompt Pencegah Slop (tempel ke AI)
```
Jangan hasilkan desain/kode generik. Ikuti 03-UI-UX-DESIGN-SYSTEM.md secara ketat.
Tidak ada lorem ipsum, gradient ungu-biru, atau klaim tanpa bukti.
Jika butuh konten, minta ke saya atau pakai data dari 15-DATA-SEEDER.md.
```

## 6. Gate
PR / output ditolak jika mengandung 1 saja pola ❌ di atas.
