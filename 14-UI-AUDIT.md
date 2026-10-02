# 14 — UI AUDIT (Checklist + Prompt Audit Antarmuka)

> Audit ini dijalankan setelah `06` diimplementasikan dan `10` dicek. Fokus: apakah UI benar, konsisten, bisa dipakai semua user, dan setia pada `[EDIT: DESIGN DIRECTION]` di `01`.
> Hasil audit adalah TEMUAN ber-severity, bukan auto-fix massal. Fix Critical/High butuh persetujuan bila berdampak global.

## 1. Cara Menjalankan

1. Baca `00` + `01` (`[EDIT: DESIGN DIRECTION]`, `[EDIT: WARNA UTAMA]`, `[EDIT: RESPONSIVE]`) + `06` + `10`.
2. Buka setiap halaman MUST di 3 viewport (360 / 768 / 1280) + 1 sesi keyboard-only.
3. Isi checklist Bagian 2 dengan LULUS/GAGAL + bukti (screenshot / file:baris).
4. Bandingkan dengan project brief (Bagian 3). Tandai penyimpangan visual direction.
5. Klasifikasikan tiap GAGAL: Critical (tidak bisa dipakai / data salah) / High (sering ditemui, workaround sulit) / Medium (mengganggu tapi ada workaround) / Low (kosmetik).
6. Buat rencana fix terpisah. Jangan auto-redesign global dalam sesi audit yang sama.

Prompt siap tempel ada di `12` P12–P13. Inti perintah: "Audit [EDIT: HALAMAN], jangan ubah kode sebelum temuan disetujui."

## 2. Checklist Mendalam

### A. Information Architecture & Hierarchy
- [ ] Satu H1 + satu aksi primer per layar? Tidak ada dua CTA primer bersaing?
- [ ] Alur sesuai `02` (user tahu langkah berikutnya tanpa menebak)?
- [ ] Navigasi/sidebar/breadcrumb konsisten di semua halaman?
- [ ] Search/filter/sort polanya tunggal + filter aktif terlihat + bisa reset?

### B. Spacing, Tipografi, Warna, Konsistensi
- [ ] Spacing mengikuti scale `06` (tanpa angka acak)?
- [ ] Tipografi dalam scale + weight terbatas + hierarki jelas?
- [ ] Warna sesuai token (`[EDIT: WARNA UTAMA]` hanya untuk primer; status semantik tunggal)?
- [ ] Radius/shadow/border konsisten? Tidak ada card dengan gaya berbeda per halaman?
- [ ] Ikon satu set + ukuran konsisten + bermakna (bukan dekorasi)?

### C. Interaction, Form, Tabel
- [ ] Button hierarchy jelas (primer/sekunder/danger)? Danger butuh konfirmasi dengan konsekuensi tertulis?
- [ ] Form: label terlihat, urutan logis, validasi inline + summary, cegah double submit, fokus ke field error pertama?
- [ ] Tabel: header jelas, angka rata kanan, status sebagai badge konsisten, aksi baris konsisten, bulk action aman, pagination server-side?
- [ ] Modal/drawer: fokus trap, ESC, tidak hilang saat form kotor tanpa konfirmasi? Toast/alert tidak menutupi aksi kritis?

### D. State (Wajib Ada Bukti Screenshot)
- [ ] Loading: skeleton meniru layout (bukan blank/spinner fullscreen yang membingungkan)?
- [ ] Empty: 1 kalimat + 1 CTA relevan (bukan "No data")?
- [ ] Error: pesan manusiawi + retry + ID error (bukan stack trace / JSON mentah / `undefined`)?
- [ ] Success + confirmation untuk aksi destruktif/uang/status?

### E. Responsive & Mobile Usability
- [ ] 360px: tanpa overflow horizontal, tabel punya strategi (stack/prioritas/scroll terkontrol), aksi kritis terjangkau jempol?
- [ ] 768px: layout tidak pecah, sidebar/drawer berperilaku benar?
- [ ] Touch target ≥44px? Tidak ada aksi hover-only yang hilang di sentuh?

### F. Accessibility & Keyboard
- [ ] Semua aksi bisa Tab/Enter/ESC? Fokus visible + tidak hilang setelah modal?
- [ ] Kontras teks ≥4.5:1? Heading hierarkis? Label terasosiasi? Alt bermakna?
- [ ] `prefers-reduced-motion` dihormati? Tidak ada konten yang hanya mengandalkan warna?

### G. Visual Polish & Slop (Rujuk `10`)
- [ ] Bebas gradient/glass/blob/glow tanpa alasan? Bebas chart/metrik/testimoni palsu?
- [ ] Copy spesifik produk (bukan generik yang cocok untuk aplikasi apa pun)?
- [ ] Tidak ada lorem ipsum / `User 1` / email palsu di area yang bisa terlihat user?

## 3. Perbandingan dengan Project Brief (Anti-Drift)

Jawab tertulis:

1. Apakah visual masih dalam `[EDIT: DESIGN DIRECTION]`? Jika menyimpang, di halaman apa + mengapa?
2. Apakah `[EDIT: WARNA UTAMA]` / tipografi / density masih dipatuhi?
3. Apakah ada redesign tak diminta pada layout global / component system existing? (Bila proyek existing, ini temuan High minimal.)
4. Apakah ada pola dari `[EDIT: POLA UI YANG DILARANG]` yang lolos? Sebutkan.

Jika drift terjadi tanpa persetujuan, status audit = GAGAL — kembalikan ke arah brief atau minta waiver + catat di `22`.

## 4. Format Laporan Temuan

```text
[AUDIT-UI-001] [Severity: Critical/High/Medium/Low] [Halaman: ...] [Viewport: ...]
Masalah: ...
Bukti: screenshot / file:baris
Dampak pengguna: ...
Saran fix minimal (tanpa redesign global): ...
Status: OPEN / FIXED (commit/branch) / WAIVED (oleh siapa + alasan)
```

Contoh: `[AUDIT-UI-007][High][/orders 360px] Tabel overflow tanpa scroll terkontrol sehingga tombol Bayar tak terjangkau. Bukti: ... Saran: ubah ke stack card prioritas + sticky action.`

---

**Gate:** tidak ada Critical terbuka; High punya rencana fix terjadwal; drift visual diselesaikan sebelum `20-FINAL-CHECKLIST.md`.
