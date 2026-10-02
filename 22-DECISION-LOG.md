# 22 — DECISION LOG (Arsitektur Decision Record Ringan)

> Catat keputusan jangka panjang (sulit dibalik), bukan keputusan trivial. Tujuan: AI sesi berikutnya (atau manusia 6 bulan lagi) memahami MENGAPA, bukan hanya APA.
> Terkait: `03` (plan), `21` (apa yang berubah), `24` (konteks), `25` (handoff).

## 1. Kapan Mencatat vs Tidak

Catat (ADR): pilihan stack/DB/auth, batas modul, strategi transaksi, model permission, storage, versioning API, denormalisasi, cache/queue, arah visual global.

Jangan catat: nama variabel, urutan import, padding 12 vs 16px dalam token, pilihan yang mudah dibalik dalam 1 jam.

Uji: "Apakah keputusan ini akan menyakitkan bila dibalik 3 bulan lagi?" Jika ya → catat.

## 2. Template ADR (Maks 1 Halaman per Keputusan)

```text
## ADR-007 — [EDIT: JUDUL KEPUTUSAN] — 2026-09-20 — Status: Accepted/Rejected/Superseded
Konteks: [masalah + batasan 01/02, 2-3 kalimat]
Keputusan: [apa yang dipilih + scope modul]
Alternatif dipertimbangkan:
  A. [opsi] — Pro: ... Kontra: ...
  B. [opsi] — Pro: ... Kontra: ...
Alasan: [mengapa A menang, kaitkan ke requirement/beban/biaya]
Konsekuensi: [positif + negatif + utang yang diterima + syarat membuka kembali]
Referensi: [REQ-..., file:baris, 21-CHANGELOG versi]
```

Contoh:

```text
## ADR-004 — Tetap monolith Laravel, tanpa microservice — Accepted
Konteks: 8 tabel, 30 user internal, deploy cPanel tanpa Redis (01).
Keputusan: monolith modular Modules/Sales, Modules/Inventory.
Alternatif: A. Microservice order+inventory — Pro: skala independen Kontra: ops 3x, tanpa DevOps.
Alasan: beban tidak membenarkan; modular cukup + murah dirawat solo dev.
Konsekuensi: bila >200 konkuren / multi-region → buka ADR baru untuk extract service.
```

## 3. Aturan Rawat

- Nomor berurutan (`ADR-001...`), tidak pernah dihapus — yang dibatalkan ditandai `Superseded by ADR-0xx` + alasan.
- AI yang membuat keputusan jangka panjang wajib membuat draft ADR di sesi yang sama, bukan "nanti".
- Sebelum mengubah keputusan lama, baca ADR tersebut + tulis impact analysis (`00`). Mengubah tanpa membaca = pelanggaran preserve.

---

**Gate:** `03` plan untuk fitur inti + `20` rilis wajib merujuk ADR yang relevan. Tanpa ADR untuk keputusan besar = temuan Medium di audit.
