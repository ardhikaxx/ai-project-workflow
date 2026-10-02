# 10 — AI PROMPT TEMPLATES

Copy-paste, ganti [BRACKET].

## 1. Starter Sesi (wajib)
```
Ikuti ai-project-workflow/00-MASTER-SYSTEM.md.
Konteks tambahan: [01,02,03 ...].
Kerjakan HANYA: [F-01 ...]. Jangan ubah file lain tanpa izin.
Rencana dulu 3-7 langkah sebelum coding.
```

## 2. Bangun Fitur
```
Buat [nama fitur] sesuai 02 F-0X dan 03 design system.
Sertakan: validasi zod, loading/empty/error state, responsive mobile.
File yang boleh disentuh: [list file].
Verifikasi: typecheck + lint lolos.
```

## 3. Fix Bug
```
Bug: [gejala + langkah reproduksi + expected vs actual].
Baca file: [list].
Jangan tebak. Jelaskan root cause dulu, lalu perbaiki minimal.
Sertakan cara verifikasi + test regresi.
```

## 4. Buat API
```
Buat POST /api/[resource] sesuai 05-BACKEND-RULES.md.
Zod schema: [fields]. Auth: [user/admin]. Ownership check: ya.
Return success/error standar. Sertakan test integration.
```

## 5. Audit UI
```
Audit halaman [URL/file] memakai checklist 12-UI-AUDIT.md + 08 anti-slop.
Kembalikan: daftar masalah P0/P1/P2 + file:baris + saran fix konkret.
Jangan langsung refactor besar.
```

## 6. Audit Backend
```
Audit endpoint [METHOD /path] memakai 13-BACKEND-AUDIT.md + 07 security.
Cek: validasi, auth, IDOR, rate limit, N+1, error leakage.
Kembalikan tabel temuan + severity + fix.
```

## 7. Tulis Test
```
Buatkan test untuk [file/fitur] sesuai 11-TESTING-QA.md.
Cakup: happy path, validasi gagal, unauthorized, empty.
Framework: [vitest/playwright].
```

## 8. Ringkas Sesi
```
Buatkan ringkasan sesi ini: apa selesai, file diubah, apa belum, risiko/next step.
Format untuk ditempel di sesi AI berikutnya (max 20 baris).
```
