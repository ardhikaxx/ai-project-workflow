# 25 — AI HANDOFF (Estafet Antar AI Agent / Sesi)

> Format summary agar Claude dapat melanjutkan pekerjaan Cursor, Gemini melanjutkan Claude, atau sesi baru melanjutkan tanpa kehilangan konteks.
> Handoff ditulis AI yang SELESAI bekerja, dibaca AI yang MELANJUTKAN (bersama `00`+`01`+`24`). Terkait: `21` (riwayat), `22` (mengapa), `20` (status rilis).

## 1. Kapan Menulis Handoff

- Setiap akhir sesi / akhir fase `11` / sebelum ganti AI / setelah audit-fix / saat terbBlocked.
- Jangan menunggu "selesai total". Handoff parsial yang jujur lebih baik daripada tidak ada.

## 2. Template Handoff (Salin Tiap Sesi)

```text
# HANDOFF — [EDIT: NAMA PROYEK] — [EDIT: TANGGAL/JAM] — Penulis: [AI/tool + model]
## 1. Tujuan sesi & status
- Tujuan: [EDIT: ...]
- Status: [SELESAI / SELESAI SEBAGIAN / TERBLOKIR]
- Keputusan rilis (bila relevan): [lihat 20: LULUS/BERSYARAT/DITOLAK + alasan]
## 2. Yang sudah dilakukan (file:baris + perilaku)
- [path:baris — apa berubah + mengapa]
## 3. BERUBAH: (silent-change candidate — wajib diisi atau tulis NIHIL)
- [NIHIL / BERUBAH: <apa> | SEBELUM | SESUDAH | ALASAN | DAMPAK]
## 4. Database & kontrak
- Migration: [nama + reversible? + rollback teruji?]
- Kontrak API: [endpoint + versi + consumer diperbarui? + docs di mana?]
## 5. Dependency & env
- Ditambah/dihapus: [nama@versi + alasan + alternatif ditolak mengapa]
- Env baru: [NAMA_ENV (tanpa nilai) + di mana diisi + default dev]
## 6. Test & verifikasi (bukti, bukan klaim)
- Perintah: [...] | Hasil: [passed/failed/skipped + durasi]
- Gagal/skip: [ID + log ringkas + rencana]
- Manual: [langkah + viewport/akun yang dipakai + hasil]
## 7. Audit & temuan
- Audit dijalankan: [14/15/16/17/10 — ringkasan Critical/High/Med/Low]
- Sisa temuan: [ID + severity + pemilik + target]
## 8. Asumsi & keputusan (ASUMSI: / KEPUTUSAN: / REKOMENDASI:)
- [daftar eksplisit — jangan campur dengan requirement 01/02]
## 9. Masalah belum selesai & risiko
- [apa + di mana + mengapa + apa yang sudah dicoba (rujuk 23 bila recovery)]
## 10. Langkah berikutnya (siap eksekusi AI berikutnya)
1. [perintah konkret + file yang dibaca + gate yang harus dipenuhi]
2. [...]
## 11. Referensi
- REQ: [...] | TEST: [...] | ADR: [...] | Changelog: [versi] | Branch/commit: [...]
```

## 3. Aturan Kualitas Handoff

- Spesifik (file:baris, perintah, angka) — bukan "sudah diperbaiki sebagian".
- Jujur: bedakan SELESAI vs SEBAGIAN vs TERBLOKIR. Dilarang menandai SELESAI bila test/audit belum bukti.
- Lengkap untuk 10 menit pertama AI berikutnya: AI baru harus bisa lanjut hanya dari `00`+`01`+handoff ini + `22` tanpa membaca 50 pesan chat lama.
- Tanpa secret/PII. Env hanya nama, bukan nilai. Data user hanya ID sintetis dev.

Contoh buruk: "Auth sudah beres, tinggal poles." Contoh baik:

```text
Status: SELESAI SEBAGIAN. Login session + RBAC kasir selesai (auth/SessionController.php:40).
Sisa: refresh rotation + test IDOR outlet B (P7 di 12). Blokir: butuh [EDIT: SESSION EXPIRY] dari pemilik.
BERUBAH: NIHIL. Test: php artisan test --filter=AuthTest → 12 passed. Audit 15: 1 High (rate limit) OPEN.
Berikutnya: (1) tambah rate limit login (baca 05 Bag 1 + 15C), (2) uji IDOR 2-akun + catat TEST-AUTH-003.
```

---

**Gate:** sesi dianggap selesai tertib bila handoff terisi + traceability `02` + draft `21` diperbarui. Tanpa ini, sesi berikutnya wajib memulai dengan `P1` discovery ulang.
