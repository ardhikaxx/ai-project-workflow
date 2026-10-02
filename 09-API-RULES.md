# 09 — API RULES (Desain API yang Reusable)

> File ini mengatur kontrak API. Implementasi lifecycle di `08`, auth di `05`, konsumsi di `07`.
> Placeholder arsitektur: `[EDIT: API STYLE — REST / GraphQL / RPC / Server Actions]` di bawah mengikat seluruh file ini. Jika kosong, AI wajib bertanya sebelum membuat endpoint.

## 1. Menentukan Gaya API

- **Pilihan utama:** `[EDIT: API STYLE — contoh: REST JSON untuk /api/v1/* + web route server-render untuk admin]`
- Aturan:
  - REST JSON: default untuk CRUD + integrasi umum. Gunakan konvensi di bawah.
  - GraphQL: hanya bila consumer butuh seleksi field fleksibel + ada di `01`. Tetap butuh auth per-field + depth/cost limit.
  - RPC / Server Actions: boleh untuk aplikasi monolith framework (Next.js, Livewire) bila tidak ada consumer eksternal. Tetap butuh validasi + auth + error standar per action.
  - Campuran harus eksplisit: bagian mana REST, mana action, dan mengapa. Dilarang dua gaya untuk use-case yang sama tanpa alasan.

Jika hanya web internal tanpa consumer eksternal, API boleh minimal — jangan overdesign versioning/dokumentasi publik.

## 2. Konvensi REST (Bila Dipilih)

- **Naming:** resource plural, lowercase, hyphen bila perlu: `GET /api/v1/products`, `POST /api/v1/orders`, `GET /api/v1/orders/{id}`. Nested hanya 1 level (`/orders/{id}/items`); lebih dalam gunakan flat + filter (`/items?order_id=`).
- **Method:** `GET` baca (idempoten, tanpa efek), `POST` buat/aksi, `PUT/PATCH` update (PUT full, PATCH parsial), `DELETE` hapus (soft vs hard harus eksplisit di dokumen + response).
- **Aksi non-CRUD:** gunakan sub-resource kata kerja: `POST /orders/{id}/approve`, `POST /bookings/{id}/cancel` + body `{reason}`. Jangan memaksakan ke `PUT` ambigu.
- **Versioning:** `/api/v1` sejak awal bila ada kemungkinan consumer eksternal/mobile. Jangan ubah kontrak v1 secara breaking; buat v2 + masa tenggang + changelog (`21`).

## 3. Request, Response, dan Error Standar

- **Request:** JSON `snake_case` atau `camelCase` — pilih SATU konsisten proyek. Validasi ketat (tipe, required, enum, batas). Tolak unknown field bila kontrak ketat. File upload via `multipart/form-data` + batas di `05`/`08`.
- **Response sukses konsisten:**

```json
{ "success": true, "message": "OK", "data": {}, "meta": { "page": 1 } }
```

- **Response list paginated:**

```json
{
  "success": true, "data": [],
  "meta": { "page": 1, "per_page": 20, "total": 235, "last_page": 12 },
  "links": { "next": "/api/v1/products?page=2" }
}
```

- **Response error konsisten (warisi `00`, jangan buat varian):**

```json
{ "success": false, "message": "Validasi gagal", "code": "VALIDATION_ERROR", "details": [{ "field": "sku", "message": "..." }] }
```

- **Status code:** `200` OK, `201` created (+ `Location`), `204` no content, `400` bad request, `401` unauthenticated, `403` forbidden (jangan bedakan 401/403 secara bocor bila perlu), `404` not found (tanpa konfirmasi keberadaan bila sensitif), `409` conflict (stok/versi), `422` validation, `429` rate limited (+ `Retry-After`), `500` internal (generik).

## 4. Filtering, Sorting, Search, dan Pagination

- Parameter standar: `?search= &filter[status]= &sort=-created_at &page= &per_page=` (maks `[EDIT: MAX PER PAGE — contoh: 100]`, default `[EDIT: DEFAULT PAGINATION — contoh: 20]`).
- Whitelist kolom `filter`/`sort` yang boleh (hindari sort kolom berat tanpa index). Validasi nilai enum.
- Search: definisikan cakupan (kolom apa saja) + strategi (`ILIKE` vs full-text) + batas panjang. Dokumentasikan agar frontend (`07`) sinkron.
- Jangan kembalikan seluruh tabel tanpa pagination pada resource bertumbuh. Untuk export besar, gunakan job + file unduhan, bukan response raksasa.

## 5. Auth, Rate Limit, Idempotensi, dan Keamanan API

- Setiap endpoint tulis di dokumen: `auth: public / authenticated / role:X + ownership:Y`. Default: authenticated. Public hanya eksplisit.
- Rate limiting per endpoint sensitif + global (lihat `05`). Dokumentasikan limit di dokumentasi API.
- Idempotency untuk POST kritis (order, payment, webhook): header `Idempotency-Key`, simpan respons pertama 24 jam, kembalikan sama bila key diulang.
- CORS, token di header/cookie aman (bukan query), tidak ada secret di URL/log. Webhook wajib signature + timestamp + replay protection (lihat `05`).
- Backward compatibility: tambah field = aman; hapus/rename/ubah tipe = breaking → versi baru + migrasi consumer + pemberitahuan.

## 6. Dokumentasi API (Proporsional)

- Minimal: tabel endpoint (method, path, auth, request, response, error code) di `docs/api.md` atau OpenAPI bila ada consumer eksternal.
- Setiap contoh harus runnable (bukan `...`), mencakup happy path + 1 error (`422`/`403`).
- Dokumentasikan kode error stabil (`VALIDATION_ERROR`, `FORBIDDEN`, dll) sekali, bukan per endpoint.
- Jaga dokumentasi sinkron dengan kode. Perubahan kontrak = silent change bila tidak dilaporkan (lihat `00`) + wajib update doc + test integrasi.

```text
Contoh entri:
POST /api/v1/orders — auth: Kasir outlet sendiri
Body: { customer_id, items: [{ product_id, qty }] }
201: { success:true, data:{ id, total_cents } }
422: { success:false, code:"VALIDATION_ERROR", details:[...] }
403: { success:false, code:"FORBIDDEN" }
Idempotency-Key: didukung
Rate limit: 60/mnt per user
```

---

**Gate:** kontrak disetujui (naming + format + pagination + auth per endpoint) → implementasi di `08` → konsumsi di `07` → test integrasi di `13` → audit di `15`.
