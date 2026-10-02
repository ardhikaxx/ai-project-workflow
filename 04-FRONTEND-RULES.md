# 04 — FRONTEND RULES

## 1. Stack & Struktur
- Framework: [Next.js App Router / Vite React / dll]
- Styling: [Tailwind / CSS Modules] — 1 cara saja.
- State: Server state = [TanStack Query / fetch], Client state = minimal.
- Struktur folder:
```
src/
  app/ (routes)
  components/ui/ (design system)
  components/features/
  lib/ (api, auth, utils)
  hooks/
```

## 2. Aturan Komponen
- 1 file = 1 komponen utama. Max ~200 baris, pecah jika lebih.
- Props typed eksplisit, tidak ada `any`.
- Komponen `ui/` murni presentasional, tanpa fetch langsung.
- Form: gunakan React Hook Form + Zod. Validasi client + server sama.

## 3. Data Fetching
- Jangan fetch di render tanpa cache / abort.
- Gunakan loading skeleton, bukan spinner fullscreen kecuali transisi halaman.
- Error boundary + retry button di setiap segmen.
- Optimistic update hanya untuk aksi reversibel (like, toggle).

## 4. Routing & Auth Guard
- Proteksi route di server (middleware/loader), bukan hanya sembunyikan tombol.
- Redirect: unauthenticated → `/login?next=...`, unauthorized → `/403`.

## 5. Kualitas
- ESLint + TypeScript strict lolos tanpa warning.
- No `console.log`, no kode komentar mati.
- Aksesibilitas: label, alt, keyboard navigable, focus trap di modal.
- Mobile-first: test di 360px dulu.

## 6. Checklist per Halaman
- [ ] Loading / empty / error state ada
- [ ] Validasi + pesan error jelas (ID)
- [ ] Responsive 360px / 768px / 1280px
- [ ] Keyboard + screen reader dasar lolos
