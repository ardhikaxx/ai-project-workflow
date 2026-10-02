# 02 — PRODUCT REQUIREMENTS (PRD)

Turunan detail dari Project Brief. Satu fitur = satu blok lengkap.

## Template Fitur (copy per fitur)

### F-01: [Nama Fitur]
- **Prioritas:** [P0 Wajib MVP / P1 / P2 Nanti]
- **User Story:** Sebagai [peran], saya ingin [aksi], agar [manfaat].
- **Flow:**
  1.
  2.
  3.
- **Acceptance Criteria (testable):**
  - [ ] Diberikan ..., ketika ..., maka ...
  - [ ] Validasi gagal menampilkan pesan "..."
  - [ ] Empty state menampilkan "..."
- **UI Reference:** link / file di `03`
- **API Terkait:** `POST /api/...`
- **Edge Cases:**
  - [ ] Offline?
  - [ ] Data kosong?
  - [ ] Akses tanpa login?
- **Out of Scope fitur ini:**
  -

---

## Daftar Fitur MVP (maks 5-7)
1. F-01:
2. F-02:
3. F-03:

## User Roles & Permissions (ringkas, detail di 07)
| Role | Bisa akses | Tidak bisa |
|------|------------|------------|
| Guest | | |
| User | | |
| Admin | | |

## Sitemap / Navigasi
```
- / (landing)
- /login, /register
- /dashboard
- /dashboard/[fitur]
- /settings
```

## Definisi Selesai (DoD) per Fitur
- [ ] Kode + validasi + error handling
- [ ] Loading / empty / error state
- [ ] Test manual + otomatis (lihat 11)
- [ ] Terdokumentasi di README project jika API publik

> Aturan: Fitur tanpa Acceptance Criteria = belum boleh dikerjakan AI.
