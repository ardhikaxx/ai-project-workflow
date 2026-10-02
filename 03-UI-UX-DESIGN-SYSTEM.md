# 03 — UI/UX DESIGN SYSTEM

Semua keputusan visual terkunci di sini. AI dilarang mengarang gaya baru.

## 1. Brand Personality (pilih 3 kata)
Contoh: [Profesional, Tenang, Cepat] — Bukan: [Modern, Bersih, Menarik]

Dilarang: gradient ungu-biru default, glassmorphism berlebihan, emoji sebagai ikon.

## 2. Warna (hex fix, max 5)
| Token | Hex | Pakai untuk |
|-------|-----|-------------|
| `--bg` | `#FFFFFF` | background |
| `--fg` | `#111827` | teks utama |
| `--primary` | `#...` | CTA, link |
| `--muted` | `#...` | border, muted |
| `--danger` | `#DC2626` | error |

Dark mode: [Ya / Tidak] — jika Ya, definisikan token dark.

## 3. Typography
- Display / H1: font ..., size ..., weight ..., tracking ...
- Body: font ..., size 15-16px, line-height 1.6
- Mono (kode/angka): ...
- Bahasa: ID, tone: lugas, tidak formal berlebihan.

## 4. Spacing & Radius & Shadow
- Spacing base: 4 / 8 pt grid
- Radius: sm 6px, md 10px, lg 16px (pilih 1 dominan)
- Shadow: hanya 1 level subtle, tanpa shadow warna-warni.

## 5. Komponen Standar
- Button: Primary / Secondary / Ghost / Danger + Disabled + Loading. Height min 40px.
- Input: label selalu di atas, error di bawah + `aria-describedby`, border merah saat error.
- Card: 1 border 1px + radius md, tanpa gradient.
- Empty State: ilustrasi sederhana + judul + aksi 1 tombol.
- Toast: sukses/gagal, max 4 detik, ada close button.

## 6. Layout Rules
- Max width konten: 1200px, padding mobile 16px, desktop 24px.
- Navbar: logo kiri, aksi kanan, mobile hamburger.
- Tabel → jadi card list di mobile (<640px).
- Semua halaman wajib: loading skeleton, empty, error + retry.

## 7. Aksesibilitas Dasar
- Kontras min 4.5:1, focus-visible jelas, tap target min 44px.
- Semua ikon punya label / aria-label.

---
Referensi inspirasi (2-3 link produk nyata, bukan Dribbble generik):
1.
2.
