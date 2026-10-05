<div align="center">

# 👋 Hi, I'm [@nauraizsubhan01](https://github.com/nauraizsubhan01)

**Full-Stack Engineer · System Designer · Robotics Enthusiast**

I build production-grade systems for real businesses: multi-tenant SaaS, conversational
commerce on WhatsApp, offline-first apps, and the robotics/control foundations underneath it all.

</div>

---

## 📌 Table of Contents

- [Featured Projects](#-featured-projects)
  - [Wbns03 — WhatsApp Ordering Bot](#1-wbns03--whatsapp-ordering-bot)
  - [RestroHub — Multi-Tenant Restaurant SaaS (PHP / Laravel)](#2-restrohub--multi-tenant-restaurant-saas-php--laravel)
- [Skillset](#-skillset)
  - [System Design](#-system-design)
  - [Robotics & Control](#-robotics--control)
  - [Languages, Frameworks & Tooling](#-languages-frameworks--tooling)
- [Project Portfolio Template](#-project-portfolio-template)
- [Contact](#-contact)

---

## 🚀 Featured Projects

| Project | What it is | Stack | Status |
|---|---|---|---|
| [**Wbns03**](./projects/wbns03-whatsapp-bot.md) | WhatsApp food-ordering agent for Dera Foods, Karachi | TypeScript · Node 20 · Express · PostgreSQL · Meta Cloud API · DeepSeek | 🟢 Phase 0 live · Phase 1 (ordering) in progress |
| [**RestroHub**](./projects/restrohub.md) | Multi-tenant restaurant management SaaS — POS, ordering site, admin | PHP 8.3 · Laravel 12 · MySQL 8 · Redis · Reverb · React 18 · TypeScript | 🟢 v1.1 released |

---

### 1. Wbns03 — WhatsApp Ordering Bot

> A WhatsApp agent that lets customers browse the menu and place food orders for
> **Dera Foods** (Model Colony, Karachi), built directly on the **Meta WhatsApp Cloud API** —
> no SDK, no gateway.

**Highlights**

- 🧱 **Two-layer architecture** — a domain-agnostic `transport/` layer (HMAC signature
  verification, replay dedupe, allowlist, phone masking, delivery) and a swappable `app/`
  layer, joined by a ~40-line contract (`src/app/types.ts`). Swapping the bot's whole
  behaviour is one env var (`HANDLER=…`).
- 🔒 **Safe by construction** — the `Reply` object is bound to the inbound sender before the
  handler runs, so an application bug can never message the wrong customer.
- 🛡️ **Security-first decisions** — evaluated Evolution API and rejected it after a source
  review (no `x-hub-signature-256` check, token leakage in outbound webhooks). Going direct
  removed a network hop, an auth boundary and three services.
- 🛒 **Ordering engine (Phase 1)** — state machine, cart, price snapshots, per-customer
  serialisation, durable `inbound_events` dedupe in **PostgreSQL**, and free-text order
  parsing through a **DeepSeek** adapter behind a port with a single shared retry/timeout budget.
- ✅ **Heavily tested** — ~400 application checks that run with no network, credentials or
  env vars, plus transport smoke tests covering tampering, replay and allowlist enforcement.
- ☁️ Deployed on **Railway** as a single service.

**Tech:** `TypeScript` `Node.js 20` `Express` `PostgreSQL` `Meta WhatsApp Cloud API` `DeepSeek LLM` `Railway`

➡️ [Full case study](./projects/wbns03-whatsapp-bot.md) · 🔗 [Repository](https://github.com/nauraizsubhan01/Wbns03) *(private)*

---

### 2. RestroHub — Multi-Tenant Restaurant SaaS (PHP / Laravel)

> A micro-SaaS for small and mid-size restaurants: an **offline-first POS PWA** with
> thermal-receipt printing, a **public ordering site** with pickup/delivery, and an
> **admin dashboard** — all multi-tenant with **one MySQL database per restaurant**.

**Highlights**

- 🏢 **Database-per-tenant** multi-tenancy with `stancl/tenancy` — a central landlord DB,
  subdomain-resolved tenants, and one-command provisioning (`php artisan tenants:provision`).
- 📴 **Offline-first POS** — installable PWA using IndexedDB (Dexie) with client-generated
  UUIDs and device-prefixed order numbers (`A-0042`); an **append-only, idempotent batch sync**
  removes the need for conflict resolution.
- 🖨️ **ESC/POS printing** via RawBT for kitchen and customer receipts on Android tablets.
- ⚡ **Real-time** order board over Laravel **Reverb** websockets, with automatic degradation
  to 15 s polling.
- 💳 Karachi-specific payments flow (delivery, payment claim & verify), tenant suspension.
- 🎨 **v1.1:** CSV/JSON menu importer with dry-run preview, per-tenant content blocks,
  runtime brand theming and self-hosted font pairings — one build serves every tenant.
- 📈 NFR gates: `POST /api/orders` **p95 < 300 ms** at 10 req/s, 50-order batch sync **< 2 s**;
  Pest (real MySQL), Vitest and Playwright end-to-end tests; nightly per-tenant backups.

**Tech:** `PHP 8.3` `Laravel 12` `Sanctum` `Reverb` `MySQL 8` `Redis 7` `React 18` `TypeScript` `Vite` `Dexie` `PWA` `Pest` `Playwright` `Docker` `Nginx`

➡️ [Full case study](./projects/restrohub.md) · 🔗 [Repository](https://github.com/nauraizsubhan01/restaurant) *(private)*

---

## 🧠 Skillset

### 🏗️ System Design

| Area | What I do |
|---|---|
| **Architecture** | Modular monoliths, clear layer boundaries (ports & adapters / hexagonal), composition roots, knowing when *not* to use microservices |
| **Multi-tenancy** | Database-per-tenant isolation, landlord/tenant split, tenant provisioning, per-tenant migrations and backups |
| **Distributed data** | Idempotency keys, replay dedupe, append-only sync, offline-first clients, UUID-based identity, eventual consistency |
| **Reliability** | Retry & timeout budgets, graceful degradation (websockets → polling), queues for non-critical work, backup/restore drills |
| **Performance** | Capacity planning against design envelopes, p95 latency gates, load testing, indexing strategy |
| **Security** | HMAC webhook verification, timing-safe comparison, secret isolation, allowlists, PII masking in logs, bundle-secret scanning in CI |
| **Real-time** | WebSockets (Laravel Reverb / Echo), event-driven status pipelines |
| **Documentation** | TRDs, ADR-style locked decisions, phased implementation plans, runbooks |

### 🤖 Robotics & Control

| Area | Skills |
|---|---|
| **Control Systems** | PID tuning, state-space modelling, feedback loop design, stability analysis |
| **Kinematics & Dynamics** | Forward / inverse kinematics, robot manipulator modelling, trajectory planning |
| **Simulation** | MATLAB / Simulink, ROS / Gazebo-style simulation workflows |
| **Embedded & Hardware** | Microcontrollers (Arduino / ESP32), sensors & actuators, motor control (DC, servo, stepper) |
| **Perception** | Sensor fusion fundamentals, computer vision basics (OpenCV) |
| **Lab Work** | Control & robotics lab experiments ([Control_Robotics_Lab](https://github.com/nauraizsubhan01/Control_Robotics_Lab)) |

### 🛠️ Languages, Frameworks & Tooling

<p>
<img src="https://img.shields.io/badge/TypeScript-3178C6?style=flat&logo=typescript&logoColor=white" />
<img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black" />
<img src="https://img.shields.io/badge/PHP-777BB4?style=flat&logo=php&logoColor=white" />
<img src="https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white" />
<img src="https://img.shields.io/badge/C%2B%2B-00599C?style=flat&logo=cplusplus&logoColor=white" />
<img src="https://img.shields.io/badge/Node.js-339933?style=flat&logo=nodedotjs&logoColor=white" />
<img src="https://img.shields.io/badge/Laravel-FF2D20?style=flat&logo=laravel&logoColor=white" />
<img src="https://img.shields.io/badge/React-61DAFB?style=flat&logo=react&logoColor=black" />
<img src="https://img.shields.io/badge/Express-000000?style=flat&logo=express&logoColor=white" />
<img src="https://img.shields.io/badge/MySQL-4479A1?style=flat&logo=mysql&logoColor=white" />
<img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=flat&logo=postgresql&logoColor=white" />
<img src="https://img.shields.io/badge/Redis-DC382D?style=flat&logo=redis&logoColor=white" />
<img src="https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white" />
<img src="https://img.shields.io/badge/Railway-0B0D0E?style=flat&logo=railway&logoColor=white" />
<img src="https://img.shields.io/badge/ROS-22314E?style=flat&logo=ros&logoColor=white" />
<img src="https://img.shields.io/badge/MATLAB-0076A8?style=flat&logo=mathworks&logoColor=white" />
<img src="https://img.shields.io/badge/Arduino-00878F?style=flat&logo=arduino&logoColor=white" />
<img src="https://img.shields.io/badge/WhatsApp_API-25D366?style=flat&logo=whatsapp&logoColor=white" />
</p>

---

## 🧩 Project Portfolio Template

Adding a new project? Copy [`templates/PROJECT_TEMPLATE.md`](./templates/PROJECT_TEMPLATE.md)
into `projects/<project-name>.md`, fill it in, then add a row to the
[Featured Projects](#-featured-projects) table and a short summary section above.

---

## 📫 Contact

- GitHub: [@nauraizsubhan01](https://github.com/nauraizsubhan01)
- LinkedIn: _add link_
- Email: _add email_
