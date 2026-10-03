# Panduan Cepat Vibe Coder: Bikin Aplikasi Rapi Tanpa Pusing

> **Apa ini?**  
> Ini adalah **guardrail (pagar pengaman)** untuk Cursor, Windsurf, Claude Code, atau ChatGPT kamu.  
> Kamu tetap bisa *vibe coding* santai dengan bahasa manusia sehari-hari, tapi bundle ini memaksa AI kamu bekerja seperti **Senior Software Engineer & Tech Lead**—sehingga aplikasi kamu gak gampang crash, kodenya gak spaghetti, dan databasenya gak berantakan pas proyek mulai membesar.

---

## ⚡ Setup 60 Detik (Cara Pasang)

1. **Extract bundle ini** ke dalam folder proyek aplikasi kamu.  
   Disarankan taruh di dalam folder `docs/ai-os/` atau langsung di root proyek.
2. Buka editor favorit kamu (**Cursor**, **Windsurf**, atau **VS Code**).
3. Selesai! Kamu siap memakai *prompt-prompt sakti* di bawah ini.

---

## 🎯 3 Prompt Sakti: Copas & Langsung Jalan

Kamu **tidak perlu membaca ke-26 file satu per satu**. Biarkan AI yang membaca dan mematuhinya untukmu!

### 1. Mulai Proyek Baru (Biarkan AI yang Wawancarai Kamu)
Ketik prompt ini di chat AI saat baru mulai:

```text
Baca @00-MASTER-SYSTEM.md dan @01-PROJECT-BRIEF.md.
Saya ingin membuat aplikasi [CONTOH: Aplikasi kasir warung kopi sederhana].
Jangan langsung tulis kode dulu! Wawancarai saya maksimal 5 pertanyaan penting untuk mengisi 01-PROJECT-BRIEF.md sampai jelas.
```

### 2. Bikin Fitur Baru (Anti-Spaghetti & Anti-Crash)
Saat mau minta AI bikin fitur tertentu (misal: fitur autentikasi / keranjang belanja / database):

```text
Baca @00-MASTER-SYSTEM.md, @03-ARCHITECTURE.md, dan @10-AI-SLOP-PREVENTION.md.
Saya mau buat fitur: [Sebutkan fitur, misal: Checkout & Bayar via QRIS].
1. Buatkan rencana langkah kerjanya dulu secara singkat.
2. Sebutkan file apa saja yang akan dibuat/diubah.
3. Jangan pasang library baru yang tidak perlu tanpa izin saya.
Tunggu konfirmasi saya sebelum menulis kodenya.
```

### 3. Penyelamat Saat AI Mulai Error Looping / Macet
Pernah ngalamin AI memperbaiki error A, tapi malah bikin error B, dan muter-muter terus? Pakai prompt ini:

```text
STOP! Jangan coba-coba tebak perbaikan lagi.
Baca @23-ERROR-RECOVERY.md dan @10-AI-SLOP-PREVENTION.md.
Analisis root cause error ini dari file log dan konfigurasi.
Jelaskan ke saya dengan bahasa manusia: kenapa error ini terjadi dan 1 solusi paling bersih untuk memperbaikinya tanpa merusak fitur lain.
```

---

## 🗺️ Cheat Sheet: Kapan Panggil File Tertentu di Cursor (`@`)

Di Cursor / Windsurf, cukup ketik `@` lalu pilih file yang sesuai kebutuhan fase proyekmu:

| Fase yang Sedang Kamu Kerjakan | Panggil File Ini di Chat AI (`@`) | Fungsinya untuk AI Kamu |
| :--- | :--- | :--- |
| **Mulai & Aturan Dasar** | `@00-MASTER-SYSTEM.md` | Mengunci cara pikir AI agar gak malas & gak halusinasi |
| **Bikin Database & Tabel** | `@04-DATABASE-DESIGN.md` | Mencegah relasi tabel kacau & query lambat |
| **Bikin Login & Hak Akses** | `@05-AUTHORIZATION-SECURITY.md` | Mencegah celah keamanan, password bocor, auth bolong |
| **Bikin Tampilan / UI** | `@06-UI-UX-DESIGN-SYSTEM.md` & `@07-FRONTEND-RULES.md` | Tampilan rapi, konsisten, responsif mobile |
| **Bikin API / Endpoint** | `@09-API-RULES.md` & `@08-BACKEND-RULES.md` | Format JSON konsisten & error message jelas |
| **Cegah Kode Sampah (Slop)**| `@10-AI-SLOP-PREVENTION.md` | AI dilarang install library aneh & bikin kode redundant |
| **Mau Cek Kualitas / Audit** | `@20-FINAL-CHECKLIST.md` | Checklist sebelum aplikasi dirilis ke publik |
| **Ganti Sesi Chat Baru** | `@25-AI-HANDOFF.md` | Agar chat baru langsung paham progres tanpa lupa konteks |

---

## 💡 4 Aturan Emas untuk Vibe Coder Sukses

1. **Satu Fitur Per Prompt**: Jangan minta "Buatkan sistem e-commerce lengkap beserta admin dashboard dan payment dalam 1 prompt". Pecah jadi langkah kecil: DB dulu, Auth dulu, baru halaman produk.
2. **Review Sebelum Terima (Accept)**: Jangan asal klik "Accept All" di Cursor. Lihat apakah AI menghapus kode penting yang sebelumnya sudah jalan.
3. **Minta Konfirmasi**: Selalu biasakan bilang *"Jelaskan rencanamu dulu sebelum tulis kode"*. AI yang tergesa-gesa adalah AI yang sering bikin bug.
4. **Gunakan Handoff**: Kalau chat sudah terlalu panjang dan AI mulai lemot/lupa, minta AI membuat ringkasan menggunakan template `@25-AI-HANDOFF.md`, lalu buka sesi chat baru.

*Selamat vibe coding dengan hasil sekelas Senior Software Engineer!* 🚀
