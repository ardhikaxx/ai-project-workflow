# 17 — PERFORMANCE & SEO (Cepat Secukupnya, SEO Seperlunya)

> File ini mengatur budget dan teknik. Implementasi frontend di `07`, backend di `08`, query di `04`/`16`, audit UI di `14`, rilis di `20`.
> Prinsip: ukur dulu, perbaiki 3 penghambat terbesar, jangan optimasi prematur di luar budget.

## 1. Performance Budget (Tetapkan di `01`, Tegakkan di Sini)

Tetapkan angka konkret di `[EDIT: TARGET PERFORMANCE]`, contoh default yang wajar:

```text
LCP < 2.5s (4G), CLS < 0.1, INP < 200ms
Bundle JS awal < 250KB gzip (landing) / < 400KB (admin kompleks)
List API p95 < 300ms (filter sederhana), laporan berat < 2s + job bila lebih
Query list utama: N+1 = 0, tanpa sequential scan tabel besar
Gambar: hero < 200KB, thumb < 80KB, format modern (WebP/AVIF) + dimensi tepat
```

Setiap klaim optimasi wajib menyertakan **sebelum/sesudah + alat ukur** (Lighthouse, WebPageTest, `EXPLAIN`, APM, log query count). Tanpa angka = belum optimasi.

## 2. Frontend Performance (Rujuk `07`)

- **Network:** code splitting per route, lazy untuk chart/editor/modal berat, impor ikon selektif, prefetch hanya untuk navigasi yang sangat mungkin, batasi font (subset + `display=swap`, maks 2 family).
- **Render:** hindari waterfall fetch berantai (paralelkan independen), streaming/Suspense bila didukung, virtualisasi untuk list ribuan baris bila pagination tidak cukup, memo hanya untuk yang terbukti berat.
- **Image/asset:** `srcset`/`sizes` atau komponen image framework, lazy below-fold, placeholder, kompresi build-time. Dilarang video autoplay 10MB sebagai "pemanis".
- **Caching:** header cache untuk aset statis (hash filename, long max-age) + revalidasi tepat untuk HTML/API. Jangan cache data per-user tanpa key scoping.

## 3. Backend & API Performance (Rujuk `08`/`09`)

- Pagination + `per_page` maks, select minimal, eager loading, agregat di DB.
- Rate limiting + timeout untuk endpoint mahal; export/laporan besar via job + file, bukan response raksasa yang menahan worker.
- Cache proporsional: hanya data idempoten + TTL + invalidasi jelas (contoh: master kategori 5 mnt; dilarang cache saldo/stok realtime tanpa strategi invalidation yang teruji).
- N+1 = High minimal. Query di loop = Critical bila di endpoint publik/sering.

## 4. Database Performance (Rujuk `04`/`16`)

- Index untuk query utama (bukan semua kolom). Verifikasi `EXPLAIN` sebelum menambah index "harapan".
- Pisahkan OLTP vs laporan: laporan berat memakai agregat/materialized/job, bukan membebani tabel transaksional saat jam sibuk.
- Batasi `sort` pada kolom terindex (whitelist di `09`). Batasi `search` liar (`%...%` di kolom tak terindex pada tabel jutaan row).

## 5. SEO Proporsional (Jangan Berlebihan untuk Dashboard Privat)

**Untuk public-facing (landing, katalog, artikel, company profile):**

- Metadata unik per halaman: title (±50–60 char), description (±140–160 char), canonical, Open Graph/Twitter, favicon.
- Semantic HTML + satu `h1` + hierarki heading + alt bermakna + URL bersih (`/produk/sewa-excavator-jakarta` bukan `/p?id=123`).
- Structured data (JSON-LD) bila relevan: Product, Article, Event, LocalBusiness, FAQ. Sitemap.xml + robots.txt + status 404/redirect yang benar.
- Internal linking + kecepatan (Core Web Vitals) sebagai fondasi ranking. Konten harus spesifik produk (rujuk `10` anti content-slop).

**Untuk private/dashboard (`/admin/*`, `/app/*`):**

- `noindex, nofollow` + auth wajib. Jangan buang waktu membuat sitemap untuk halaman privat.
- SEO di sini = findability internal: judul halaman jelas, breadcrumb, search/filter yang cepat — bukan meta keyword.

Contoh header:

```html
<title>[EDIT: NAMA PROYEK] — Sewa Excavator Jakarta, Termasuk Operator</title>
<meta name="description" content="[EDIT: ...] 150 karakter spesifik, bukan generik." />
<link rel="canonical" href="https://[EDIT: DOMAIN]/sewa-excavator-jakarta" />
<meta name="robots" content="index,follow" />
<!-- untuk /admin: --><meta name="robots" content="noindex,nofollow" />
```

## 6. Kapan TIDAK Perlu (Anti-Overengineering)

- Tidak perlu CDN global + edge cache + sharding untuk 100 user internal.
- Tidak perlu full-text engine terpisah untuk 5.000 produk (cukup trigram/full-text DB).
- Tidak perlu structured data untuk dashboard internal.
- Tidak perlu micro-frontend / ISR kompleks untuk company profile 10 halaman.

Setiap teknik di atas butuh pemicu terukur (angka beban/lambat) + persetujuan + biaya. Jika tidak ada, catat `DEFERRED:`.

---

**Gate:** budget terpenuhi di staging (Lighthouse + p95 API + EXPLAIN utama) atau deviasi dicatat + diterima pemilik sebelum `20`.
