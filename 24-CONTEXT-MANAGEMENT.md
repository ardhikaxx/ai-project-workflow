# 24 — CONTEXT MANAGEMENT (Agar AI Tidak Amnesia di Proyek Besar)

> File ini mengatur bagaimana AI memilih, menyimpan, dan mewariskan konteks. Tabel baca-minimal ada di `README` Bagian 5. File ini menjelaskan strategi di baliknya.
> Prinsip: dokumentasi adalah source of truth, bukan memori percakapan.

## 1. Aturan Pemilihan Konteks (Minimal Sufficient Context)

- Selalu bawa: `00` + `01` + tugas spesifik (`02` AC untuk fitur itu) + file yang akan diubah (penuh, bukan cuplikan).
- Tambahkan hanya yang relevan: `04` untuk DB, `05` untuk auth, `06`+`10` untuk UI, `09` untuk kontrak.
- Jangan baca seluruh repo membabi buta saat context terbatas. Prioritas:

```text
01 brief + 02 AC relevan + 03 plan modul + 04 schema relevan + 05 matriks
> file yang diubah + importer langsung
> ADR relevan (22) + handoff terakhir (25)
> file lain bila ada sisa budget
```

- Untuk tugas audit: bawa checklist audit (`14`/`15`/`16`) + temuan sebelumnya, bukan seluruh kode.
- Prompt anti-boros: "Baca [list file eksplisit]. Jangan baca file lain kecuali kamu menyebut alasannya."

## 2. Source of Truth & Pencatatan Berjalan

- Yang mengikat: `01` (konfigurasi), `02` (AC), `03` plan + `22` ADR, schema aktual + migration, test lolos.
- Yang tidak mengikat: klaim AI di chat lama ("kemarin katanya sudah fix") tanpa bukti file/test.
- Selama bekerja, AI wajib memperbarui: traceability `02` (REQ→file→TEST), `21` draft changelog, `22` draft ADR bila keputusan besar, dan catatan `ASUMSI:`/`KEPUTUSAN:` untuk handoff.
- Bila requirement berubah di tengah jalan: perbarui `01`/`02` dulu, tulis `BERUBAH:`, nilai dampak (`00` Bagian 7), baru ubah kode. Jangan ubah kode lalu "menyusul" dokumen.

## 3. Strategi Sesi Panjang & Context Window Terbatas

- Pecah per modul/fase (`11`). Tutup sesi dengan handoff (`25`) sebelum context penuh, bukan setelah AI mulai ngawur.
- Ringkasan berjalan per 3–5 langkah: apa selesai (file:baris), apa belum, asumsi, risiko, langkah berikutnya. Simpan sebagai `25` draft, bukan hanya di chat.
- Jika AI mulai mengulang pertanyaan / melupakan konvensi / mengarang field: hentikan, minta AI membaca ulang `01`+`02`+file terkait, atau mulai sesi baru dengan handoff.
- Simpan artefak besar (dump schema, log panjang, screenshot) sebagai file, bukan tempel berulang ke chat. Referensikan path-nya.

## 4. Anti-Pattern Konteks

- Menempel 26 file sekaligus lalu meminta "buatkan semuanya" → dijamin hallucinasi + inkonsistensi. Dilarang oleh `11`.
- Mengandalkan "kamu ingat kan kemarin..." tanpa file → tolak, minta sumber file.
- Membaca hanya frontend lalu menebak backend (atau sebaliknya) → wajib baca kontrak `09` + implementasi sisi lain.
- Menyimpan secret/PII di chat/log sebagai "konteks" → dilarang (`05`/`19`).

---

**Gate:** setiap sesi baru dimulai dengan: baca `00`+`01`+`25` terakhir + `22` relevan + verifikasi 1 file kunci masih sesuai sebelum melanjutkan.
