# 23 — ERROR RECOVERY (Ketika AI Merusak / Hallucinasi / Regression)

> File ini mengatur cara keluar dari kegagalan secara terkontrol. Prinsip `00` (kejujuran, no silent change) + workflow `11` tetap berlaku.
> Tanda masuk ke file ini: >3x tambal gagal, regression muncul, AI tidak memahami error, atau AI mulai menebak liar.

## 1. Stop Rule (Berhenti Menambal Acak)

Segera hentikan bila:

- Error sama muncul setelah 3 fix berbeda.
- Satu fix menimbulkan 2 bug baru.
- AI mengubah file tak terkait "sekalian".
- AI mengusulkan rewrite besar / upgrade major / hapus data sebagai "solusi cepat".

Perintah yang harus diucapkan AI ke diri sendiri: "Berhenti. Kembali ke titik hijau terakhir + root cause."

## 2. Prosedur Recovery Bertahap

1. **Bekukan & amankan:** catat file yang berubah (`git status/diff`), backup branch/stash, jangan commit rusak. Bila DB terdampak, cek backup (`19`).
2. **Kembali ke hijau:** `git stash` / checkout ke commit hijau terakhir yang test-nya lolos. Verifikasi hijau benar (jalankan test kunci).
3. **Reproduksi minimal:** buat repro terkecil (request/error/log/langkah) + bedakan expected vs actual. Tulis 2–3 hipotesis + cara membuktikan tiap hipotesis (bukan langsung fix).
4. **Bandingkan dengan requirement:** baca `02` AC + `03` plan + `22` ADR. Apakah implementasi menyimpang? Apakah asumsi AI salah? Seringkali root cause = business rule yang dikarang.
5. **Fix minimal + terukur:** satu hipotesis per fix, diff kecil, jelaskan mengapa fix ini (bukan yang lain). Dilarang mengubah 5 file sekaligus "coba-coba".
6. **Regression test:** jalankan test area + test bug repro (red→green) + smoke modul tetangga sesuai impact analysis (`00`).
7. **Catat:** tulis `KNOWN ISSUE:` / `LESSON:` ke handoff (`25`) + changelog (`21`) bila perilaku berubah + ADR (`22`) bila keputusan berubah.

```text
Laporan recovery:
Titik hijau: [commit/tag] | Penyimpangan: [file:baris vs REQ-...]
Root cause: [...] | Fix: [...] + mengapa bukan alternatif [...]
Verifikasi: [perintah + hasil] | Regression dicek: [...] | Sisa risiko: [...]
```

## 3. Pola Kegagalan Khas AI & Responsnya

- **Hallucinasi API/library:** AI memakai fungsi yang tidak ada di versi terpasang → cek versi + docs terpasang, bukan menebak versi terbaru. Pin versi di `01`.
- **Schema drift:** kode mengasumsikan kolom yang tidak ada → baca migration aktual + `16`, selaraskan kode ATAU buat migration baru yang aman (bukan keduanya diam-diam).
- **Auth/permission rusak setelah refactor:** kembalikan middleware/policy semula, uji matriks `05` 2-akun sebelum klaim pulih.
- **UI hancur setelah update CSS global:** revert token/layout global, perbaiki scoped per halaman, audit `14` sebelum lanjut.
- **Data setengah jadi:** cek transaksi (`08`); tambah transaksi + idempotency + test concurrent; bersihkan data yatim dengan skrip teraudit (backup dulu).

## 4. Kapan Meminta Bantuan Manusia / Rollback Rilis

- Minta manusia bila: butuh secret/akses prod, keputusan bisnis untuk memilih root fix, atau risiko hapus data.
- Rollback rilis bila (`19`): error > ambang, login/transaksi down, atau fix membutuhkan migration destruktif yang belum siap. Rollback bukan kegagalan — menunda rollback yang merusak data adalah kegagalan.

---

**Setelah pulih:** jangan langsung lanjut fitur baru. Pastikan titik hijau + test + catatan lengkap, baru kembali ke `11` fase semula.
