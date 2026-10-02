# 15 — DATA SEEDER

Data dummy realistis Bahasa Indonesia, bukan John Doe.

## 1. Aturan
- Terpisah: `seed:dev` vs `seed:test`. Dilarang seed prod dengan data palsu.
- Password seed: hash dari `password123` + catat di README dev saja.
- Jumlah: 5 admin? tidak. Cukup: 1 admin, 5 user, 20-50 data utama, 100+ untuk test pagination.
- Nama ID: Budi Santoso, Siti Aminah, Jl. Merdeka No.10 Bandung, PT Maju Jaya, Rp 25.000.

## 2. Contoh Script (Prisma)
```ts
// prisma/seed.ts
import { PrismaClient } from '@prisma/client';
import bcrypt from 'bcryptjs';
const db = new PrismaClient();
async function main() {
  const hash = await bcrypt.hash('password123', 10);
  const admin = await db.user.upsert({
    where: { email: 'admin@contoh.id' },
    update: {},
    create: { email: 'admin@contoh.id', name: 'Admin Utama', password_hash: hash, role: 'admin' },
  });
  // ... buat users + data domain
  console.log('seed ok:', admin.email);
}
main().finally(() => db.$disconnect());
```

Package.json:
```json
{ "scripts": { "seed:dev": "tsx prisma/seed.ts" } }
```

## 3. Checklist
- [ ] Idempotent (upsert, bisa jalan 2x tanpa duplikat)
- [ ] Tidak ada secret asli / NIK / data pribadi real
- [ ] Ada empty-state seed? (1 user dengan 0 data untuk test empty UI)
- [ ] Dokumentasi cara jalanin di README project
