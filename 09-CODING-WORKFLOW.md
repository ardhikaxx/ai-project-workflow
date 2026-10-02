# 09 — CODING WORKFLOW

Alur harian agar AI tidak ngawur.

## 1. Loop per Fitur (1 fitur = 1 loop)
1. **Pilih:** 1 item dari `02` prioritas P0.
2. **Rencana:** AI buat rencana 3-7 langkah + file yang disentuh.
3. **Eksekusi kecil:** 1 langkah = 1-3 file, langsung typecheck.
4. **Verifikasi:** lint + test terkait + cek manual (loading/empty/error).
5. **Commit:** pesan jelas `feat(auth): login dengan rate limit`.

Dilarang: kerjakan 3 fitur paralel, refactor besar + fitur baru sekaligus.

## 2. Perintah Standar ke AI
- Mulai sesi: tempel `00-MASTER-SYSTEM.md` + sebut file relevan: "Ikuti 03 dan 04, kerjakan F-01 saja".
- Lanjut: "Lanjut step 2, hanya file X. Jangan ubah file lain."
- Stuck: "Berhenti coding. Jelaskan 2 opsi + tradeoff + rekomendasi."

## 3. Aturan Edit
- Baca file dulu sebelum edit. Edit minimal, jangan rewrite 500 baris untuk 1 bug.
- Jika file >200 baris, pecah dulu baru tambah fitur.
- Setiap fix bug: tulis penyebab + cara cegah, bukan cuma tambal.

## 4. Definisi Break
- Jika 3x percobaan AI gagal di masalah sama → stop, sederhanakan scope atau minta desain ulang.
- Jika context chat >50k token → buka sesi baru + tempel `00` + ringkasan progres.

## 5. Contoh Ritme Harian
Pagi: 1 fitur P0 selesai + tested. Siang: review + audit kecil (12/13). Sore: commit bersih + update PRD checklist.
