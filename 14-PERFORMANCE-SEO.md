# 14 — PERFORMANCE & SEO

## 1. Performance Budget
- LCP < 2.5s, INP < 200ms, CLS < 0.1 (mobile 4G)
- JS per route < 200KB gzip, image < 200KB, font ≤2 family.
- Lighthouse Performance ≥90, Accessibility ≥90, Best Practices ≥90.

## 2. Checklist Frontend Perf
- [ ] Image: `next/image` + width/height + lazy + AVIF/WebP
- [ ] Font: `next/font` + display swap, max 2 weight
- [ ] Dynamic import untuk modal/chart/editor berat
- [ ] Cache: fetch `revalidate` / stale-while-revalidate tepat, bukan `no-store` semua
- [ ] Debounce search 300ms, pagination/virtualize list panjang

## 3. Backend Perf
- [ ] Pagination semua list, max 100
- [ ] Index + EXPLAIN untuk query lambat
- [ ] Cache read-heavy (Redis / memo) + invalidasi jelas
- [ ] Timeout eksternal API + fallback

## 4. SEO Dasar
- [ ] Tiap route: `<title>` unik + meta description ID 120-160 char
- [ ] OG image 1200x630, canonical, robots.txt + sitemap.xml
- [ ] Semantic HTML: 1 h1, heading berurutan, alt bermakna
- [ ] JSON-LD untuk artikel/produk jika relevan

## 5. Perintah
```bash
npx lighthouse http://localhost:3000 --view
npx next build && npx next start
```
