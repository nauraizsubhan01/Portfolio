# Wbns03 — WhatsApp Ordering Bot for Dera Foods

> A WhatsApp agent that lets customers of **Dera Foods** (Model Colony, Karachi) browse the
> menu, check hours and location, and place food orders, built directly on the
> **Meta WhatsApp Cloud API**.

| | |
|---|---|
| **Role** | Designer & developer |
| **Status** | 🟢 Phase 0 live on Railway · 🟡 Phase 1 (ordering) in progress |
| **Stack** | TypeScript · Node.js 20 · Express · PostgreSQL · Meta WhatsApp Cloud API · DeepSeek · Railway |
| **Links** | [Repository](https://github.com/nauraizsubhan01/Wbns03) *(private)* |

---

## 🎯 Problem

A local restaurant takes most orders over WhatsApp, by hand. Staff answer the same questions
(menu, hours, location) over and over and transcribe orders manually, which is slow and error-prone at peak times.

## 💡 Solution

A single-service bot that answers common questions instantly and turns WhatsApp chats into
structured, priced orders. Customers can order by menu buttons or free text. Free text is parsed
by an LLM that sits behind a port. Durable dedupe, per-customer serialisation and price
snapshots keep orders correct when Meta delivers webhooks more than once.

## 🏗️ Architecture

```
Meta Cloud API ──HTTPS──▶ whatsapp-bot (Railway, public)
                                │
                                ├──▶ PostgreSQL (private) — sessions, carts, orders, dedupe
                                └──▶ DeepSeek API — free-text order parsing
```

```
src/
├── transport/   WhatsApp plumbing. Domain-agnostic. Rarely changes.
│                signature · idempotency · allowlist · logsafe · ratelimit · meta client · server
├── app/         What the bot does. Swappable.
│                types (contract) · registry · derafoods · menu · hours · money · ordering/
├── persistence/ PostgreSQL adapters (pool, sessions, orders, ordering)
├── memory/      In-memory adapters used by tests
├── parsing/     DeepSeek adapter for the OrderParser port
└── index.ts     Composition root — the only file that knows both layers
```

## ✨ Key Features

- **Transport/application seam.** A ~40-line contract separates them, and swapping behaviour
  takes one env var (`HANDLER=hello | derafoods`).
- **Webhook security.** HMAC-SHA256 over raw bytes, timing-safe comparison, replay dedupe on
  `wamid`, wrong-destination and status-receipt filtering.
- **Sender-bound replies.** The handler can only reply to whoever messaged it.
- **Ordering state machine.** It handles cart, modifiers, totals with price snapshots and
  order reference sequences.
- **Free-text ordering.** The DeepSeek adapter returns structured JSON. It is the only module that
  can read the API key, and all its retries share one deadline.
- **PII-safe logging.** Phone numbers are masked, and a test suite checks that logs stay safe.
- **Compliance.** Explicit `/privacy` and `/terms` routes, which Meta requires before Live mode.
  There is deliberately no `express.static` mount.

## ⚖️ Design Decisions & Trade-offs

| Decision | Choice | Why |
|---|---|---|
| WhatsApp access | Direct Meta Cloud API, no SDK | Official Node SDK archived in 2023 and pinned to an old Graph API |
| Gateway | **Rejected Evolution API** | Source review found no signature verification, routing by attacker-supplied IDs, and access-token leakage |
| Topology | One service | Fewer hops, fewer auth boundaries, simpler ops |
| Persistence | PostgreSQL, before any ordering logic | Durable `inbound_events` dedupe is the foundation of correct orders |
| LLM | Behind an `OrderParser` port | Swappable and testable with fakes, and the key stays in one module |

## 🧪 Testing & Quality

- About 400 application checks (`npm run test:app`). They need no network, credentials or env vars.
- Transport smoke tests cover missing, malformed and wrong-key signatures, tampering, replay,
  non-text payloads and the allowlist. They pass the same way under every handler.
- Persistence tests run against real PostgreSQL. There are adversarial, portability and parser
  tests, and `tsc` typechecks the project.

## 🗺️ Roadmap

- [x] **0a** Transport: signatures, dedupe, allowlist, send
- [x] **0b** First application: greeting, categories, hours, location
- [x] Deployed to Railway
- [ ] **1** Ordering: Postgres, state machine, cart, orders *(in progress)*
- [ ] **2** Menu import via JSON upload
- [ ] **3** Dashboard with revenue and order totals
- [ ] **4** Launch: monitoring, backup drill, load test, template messages
