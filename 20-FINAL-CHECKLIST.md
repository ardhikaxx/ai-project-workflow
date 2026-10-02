# 20 — FINAL CHECKLIST (Release Gate Sebelum Production)

> Ini adalah gerbang rilis, bukan formalitas. Proyek TIDAK boleh disebut selesai hanya karena "halaman bisa dibuka".
> Semua requirement MUST harus traceable: `REQ (02) → implementasi (03/04/08/07/09) → test (13) → audit (14–17) → deploy (19)`.
> Penilai mengisi LULUS / GAGAL + bukti. Satu Critical terbuka = DITOLAK.

## 1. Cara Menilai

1. Baca `00` + `01` (DoD proyek) + `02` traceability + hasil `13` + temuan `14–17` + bukti `19`.
2. Untuk tiap baris: tandai ✅ LULUS / ❌ GAGAL / ⚠️ WAIVED (oleh siapa + alasan + risiko diterima).
3. Keputusan akhir: **LULUS** (semua LULUS/waived) / **LULUS BERSYARAT** (sisa Minor terjadwal) / **DITOLAK** (ada Critical/High blokir atau bukti hilang).
4. AI dilarang menilai karyanya sendiri sebagai LULUS tanpa bukti eksekusi. Pemilik/tech lead mengesahkan.

## 2. Checklist Rilis

### A. Product Completeness & Traceability
- [ ] Semua MUST di `02` punya AC + implementasi + test ID (tabel trace lengkap, tanpa "menyusul").
- [ ] Alur uang/stok/status berjalan end-to-end dengan data realistis (`18`), bukan data palsu.
- [ ] Tidak ada `[EDIT:]` kritis kosong tanpa keputusan + tidak ada TODO tanpa issue.
- [ ] Non-goals/V2 benar-benar tidak ikut terkirim (atau ditandai beta + alasan).

### B. Database & Backend & API
- [ ] Migration reversible + rollback teruji + tanpa orphan/duplikat kritis (`16`).
- [ ] N+1 = 0 untuk list utama; pagination + batas `per_page`; agregat laporan di DB (`15`/`16`).
- [ ] Controller tipis, bisnis di service, transaksi untuk multi-tulis, idempotency untuk kritis (`15`).
- [ ] Kontrak API konsisten (format sukses/error/pagination, status code, versioning bila publik) + docs sinkron (`09`).

### C. Auth, Authorization, Security
- [ ] Login/logout, session/token expiry + revoke, reset, rate limit bekerja (`05`).
- [ ] Matriks RBAC + ownership + IDOR + mass assignment diuji negatif (bukti 2-akun) (`05`/`15`).
- [ ] Upload aman, secret aman, error tidak bocor, audit log untuk uang/status/permission/hapus (`05`).
- [ ] Dependency + header aman dicek (CSP/HSTS/frame) + tidak ada debug di production (`19`).

### D. UI/UX, Responsive, Accessibility
- [ ] Hierarki + token + state (loading/empty/error/success/confirm) lengkap per fitur MUST (`14`).
- [ ] Bebas slop kritis (`10`): tanpa gradient/glass/chart/testimoni/metrik palsu tanpa izin.
- [ ] Responsive 360/768/1280 + touch target + tanpa overflow perusak aksi (`14`).
- [ ] Keyboard + fokus + kontras + label + heading + alt dasar lolos (`14`).
- [ ] Setia pada `[EDIT: DESIGN DIRECTION]`; drift sudah dikembalikan atau di-waiver + `22`.

### E. Testing & Performance & SEO
- [ ] Perintah test + hasil nyata tercatat; area wajib hijau; skip/known issues disetujui (`13`).
- [ ] Budget `17` terpenuhi (LCP/CLS/bundle/p95 API/EXPLAIN) atau deviasi diterima pemilik.
- [ ] SEO proporsional: publik lengkap (title/desc/canonical/OG/sitemap/robots/structured bila relevan); privat `noindex` + auth (`17`).

### F. Deployment, Monitoring, Docs
- [ ] Env/secret aman, build reproducible, backup + restore teruji, rollback siap, health hijau, smoke lolos (`19`).
- [ ] Changelog (`21`), decision log (`22`), handoff (`25`) terisi. Konteks tersimpan (`24`).
- [ ] Pemilik mendapat: URL, akun + cara ganti password, cara backup/restore, kontak darurat, batasan diketahui.

## 3. Definition of Done & Release Gate

```text
Keputusan: [ LULUS / LULUS BERSYARAT / DITOLAK ]
Critical terbuka: [0 / list]
High terbuka: [0 / list + jadwal]
Bukti test: [perintah + hasil]
Bukti audit: [14/15/16/17 ringkasan]
Bukti deploy: [backup id, tag versi, smoke hasil]
Sisa (bila bersyarat): [list + pemilik + tanggal]
Pengesah: [nama + tanggal] | Penolak (bila ditolak): [alasan + syarat ulang]
```

Aturan: DITOLAK bila: satu Critical, bukti test/audit hilang, backup/rollback belum teruji, atau ada silent change yang belum dilaporkan. LULUS BERSYARAT hanya untuk Minor + tanggal fix jelas.

---

**Setelah LULUS:** kunci tag rilis, arsipkan handoff (`25`), jadwalkan review pasca-rilis 7–14 hari (bug, performa, feedback user) sebagai input iterasi berikutnya.
