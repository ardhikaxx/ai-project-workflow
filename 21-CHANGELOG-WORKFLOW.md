# 21 — CHANGELOG WORKFLOW (Mencatat Perubahan Tanpa Noise)

> Changelog adalah riwayat yang dibaca manusia + AI sesi berikutnya. Bukan dump commit. Setiap perubahan perilaku/schema/kontrak/permission/visual harus tercatat di sini.
> Terkait: `22` (mengapa diputuskan), `25` (apa yang sedang berjalan), `20` (gate rilis).

## 1. Format (Keep a Changelog, Disederhanakan)

Satu file `CHANGELOG.md` di root/repo (bukan per modul). Entri terbaru di atas:

```text
## [1.3.0] — 2026-09-28
### Added
- Modul refund parsial (REQ-POS-014). File: services/RefundService.php:20.
### Changed
- BERUBAH: POST /api/v1/orders kini wajib Idempotency-Key. Consumer diperbarui: kasir-web. Alasan: cegah double order.
### Fixed
- N+1 di list order (15 temuan #2): tambah eager items.product. Query 47→3.
### Security
- Rate limit login 5/mnt/IP + audit log refund. Lihat 05.
### Deprecated / Removed
- GET /api/v1/reports/legacy → diganti /v1/reports/sales (sunset 2026-10-31).
```

Kategori baku: `Added / Changed / Fixed / Security / Deprecated / Removed`. Dilarang kategori bebas per entri.

## 2. Aturan Penulisan

- Satu bullet = satu perubahan perilaku + dampak + referensi (REQ/TEST/AUDIT/file:baris). Tulis untuk pembaca 3 bulan dari sekarang.
- Perubahan kontrak/schema/permission/visual = wajib `BERUBAH:` + SEBELUM→SESUDAH (lihat `00` Bagian 8). Tanpa ini = silent change.
- Jangan catat keputusan trivial (typo, formatting). Jangan gabungkan 10 perubahan tak terkait dalam satu bullet.
- Setiap rilis (`19`/`20`) wajib punya seksi versi + tag git yang sama. Tanpa tag = belum rilis.

## 3. Siapa Menulis & Kapan

- AI menulis draft entri setiap selesai fitur/fix (bukan di akhir proyek). Pemilik/tech lead menyetujui bahasa user-facing.
- Pre-commit check: apakah diff mengubah perilaku? Jika ya, changelog sudah diperbarui? Jika tidak, tolak klaim DONE.

---

**Gate:** `20` menolak rilis tanpa changelog untuk versi tersebut.
