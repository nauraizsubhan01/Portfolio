# RestroHub — Multi-Tenant Restaurant Management SaaS

> A micro-SaaS for small and mid-size restaurants. It combines an **offline-first POS PWA**, a
> **public ordering site** with pickup and delivery, and an **admin dashboard**. Each
> restaurant gets its own MySQL database.

| | |
|---|---|
| **Role** | Architect & full-stack developer |
| **Status** | 🟢 v1.1 released (menu importer, tenant content, theming) |
| **Stack** | PHP 8.3 · Laravel 12 · stancl/tenancy · Sanctum · Reverb · MySQL 8 · Redis 7 · React 18 · TypeScript · Vite · Dexie |
| **Links** | [Repository](https://github.com/nauraizsubhan01/restaurant) *(private)* |

---

## 🎯 Problem

RestroHub merges two single-tenant systems that were already delivered. One was a tablet POS
and the other was a plain PHP/PDO ordering site. Both stored critical data in fragile places, such as
POS orders in `localStorage` and client-side email keys. Neither could serve more than one restaurant.

## 💡 Solution

A single Laravel modular monolith that serves many restaurants with strong isolation. It has
three React front-ends that share typed packages. The POS keeps working fully offline and
syncs when the network returns. One frontend build serves every tenant, and each restaurant's
branding is resolved at runtime.

## 🏗️ Architecture

```
             ┌──────────── apps/pos (PWA, Dexie/IndexedDB, RawBT printing)
             ├──────────── apps/ordering (public site, per-tenant subdomain)
             ├──────────── apps/admin (back office, live order board)
             │      shared: packages/shared-types · packages/api-client
             ▼
   Laravel 12 modular monolith (Orders · Menu · Tenancy · Billing · Notifications · Devices)
      │            │                 │                     │
  Landlord DB   Tenant DBs (1/restaurant)   Redis (queues, cache)   Reverb (websockets)
```

## ✨ Key Features

- **Tenancy.** One database per tenant through `stancl/tenancy`, resolved by subdomain.
  `tenants:provision` creates the database, migrates and seeds it, and emails an owner invitation.
- **Offline-first POS.** It stores orders in IndexedDB with client UUIDv4 IDs and device-prefixed
  display numbers. Batch upserts are append-only and idempotent.
- **Printing.** Kitchen and customer receipts print over ESC/POS through RawBT on Android tablets.
- **Order pipeline.** Orders move through received → preparing → ready → picked_up / completed and
  update live over Reverb, falling back to 15 s polling.
- **Auth.** Sanctum SPA cookies for staff and long-lived, revocable device tokens for the POS.
- **Payments.** A Karachi-specific checkout supports delivery and payment claim & verify. Tenants
  can be suspended.
- **v1.1.**
  - CSV/JSON menu import with a dry-run preview.
  - Content blocks: hero, hours, map, socials, WhatsApp call bar.
  - Runtime brand palette and four self-hosted font pairings, with no third-party font CDN.

## ⚖️ Design Decisions & Trade-offs

| Decision | Choice | Trade-off accepted |
|---|---|---|
| Backend shape | Modular monolith | Microservices would only add cost at this scale |
| Isolation | Database-per-tenant | Migrations run N times, and connection pools grow with tenants |
| Offline store | IndexedDB via Dexie | More complex than `localStorage`, but transactional and reliable |
| Sync | Append-only idempotent upserts | POS never edits orders after sync, so no conflict resolution is needed |
| Real-time | Self-hosted Reverb | One more long-running process, with automatic polling fallback |
| Email | Server-side queued mail | Removes spoofable client-side keys |

## 🧪 Testing & Quality

- **Pest** backend tests run against real MySQL, never SQLite, and guards stop tests from writing
  to non-test databases.
- **Vitest** has one suite per app. **Playwright** covers the cross-app path: place an order, see it
  on the admin board, then on the tracking page.
- NFR gates: `POST /api/orders` stays under **300 ms at p95** at 10 req/s sustained, and a
  50-order batch sync finishes in **under 2 s**.
- CI fails the build if a known secret shows up in a shipped JS bundle.

## 🚀 Operations

- Runs on one Ubuntu VPS: nginx + php-fpm, MySQL 8, Redis, Reverb, and two queue workers under supervisor.
- `deploy.sh` runs a repeatable deploy, including landlord and all tenant migrations.
- `ops/backup.sh` runs a nightly mysqldump of every tenant, keeps 30 days, and runs independently of the app.

## 🗺️ Roadmap

- [x] Phases 0–9: scaffold → tenancy → schema → auth → orders → POS sync → POS PWA → admin & ordering → payments → hardening
- [x] v1.1: menu importer, tenant content, theming
- [ ] v2: online card payments, multi-location per tenant
