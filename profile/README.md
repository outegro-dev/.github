<div align="center">

<a href="https://outegro.dev"><img src="https://outegro.dev/og" alt="outegro.dev" width="100%"></a>

# outegro.dev

**A real production platform, designed, built and operated end-to-end by one engineer — from the first pixel to the cluster.**

[![Live](https://img.shields.io/badge/live-outegro.dev-cf3528?style=for-the-badge)](https://outegro.dev)
[![Play Battleship](https://img.shields.io/badge/play-battleship-2360b5?style=for-the-badge)](https://battleship.outegro.dev)
[![Read on Edu](https://img.shields.io/badge/read-edu-f5c518?style=for-the-badge&labelColor=1b1915)](https://edu.outegro.dev)
[![Stack](https://img.shields.io/badge/see-the%20stack-1b1915?style=for-the-badge)](https://outegro.dev/stack)

</div>

---

## What this is

outegro.dev is my portfolio — not as a set of screenshots, but as a running system. Six web apps share one account, one payment flow, one notification pipeline and one operator console, all deployed through GitOps to a Kubernetes cluster I set up and maintain myself.

Everything here is live. You can sign in, play, buy, read — and every step goes through the same services described below.

## Live apps

| | App | What it does |
|---|---|---|
| 🗞️ | **[outegro.dev](https://outegro.dev)** | Landing in a hand-made zine style, EN/RU, strict CSP, CLS ≈ 0 |
| 🔑 | **[id.outegro.dev](https://id.outegro.dev)** | Single sign-on for the whole platform: email code, passkeys (WebAuthn), Google, sessions and security log |
| ⚓ | **[battleship.outegro.dev](https://battleship.outegro.dev)** | Real-time multiplayer Battleship: matchmaking by Elo, private rooms, 4 bot levels, leaderboard, shop |
| 📚 | **[edu.outegro.dev](https://edu.outegro.dev)** | Interactive books on Node.js and SQL internals: quizzes, an event-loop simulator, an in-browser SQL sandbox, AI helper |
| 💳 | **[pay.outegro.dev](https://pay.outegro.dev)** | Checkout, subscriptions, receipts and refunds for every app on the platform |
| 🛠️ | **admin.outegro.dev** | Operator console: users, roles, grants, audit log, payments, content, Grafana via SSO *(private)* |

## Architecture

```mermaid
flowchart LR
  U([Browser]) --> CF[Cloudflare] --> T[Traefik on K3s]
  T --> WEB["<b>6 Next.js apps + BFF</b><br/>landing · id · battleship<br/>pay · edu · admin"]
  WEB --> SVC["<b>5 NestJS services</b><br/>auth · notifications · payments<br/>game server · edu"]
  SVC <--> MQ[(RabbitMQ<br/>outbox / inbox)]
  SVC --> PG[(PostgreSQL 18<br/>one DB per service)]
  SVC --> VK[(Valkey)]
  PG -. daily backups .-> R2[(Cloudflare R2)]
```

## Engineering highlights

- **Identity done properly.** OAuth 2.0 SSO with PKCE across subdomains, ES256 JWTs with key rotation and JWKS, a backend-for-frontend so tokens never reach the browser, passkeys, Google with account linking, CSRF checks, rate limits backed by Valkey.
- **Services that own their data.** Each NestJS service has its own PostgreSQL database and role. Services talk through RabbitMQ events with a transactional outbox and an idempotent inbox, retries via TTL queues and dead-letter queues.
- **Payments you can trust.** Checkout, webhooks, reconciliation, subscriptions, refunds and entitlement grants; duplicate purchases are detected and flagged, revoking a grant cancels renewal at the provider.
- **A server-authoritative game.** Battleship runs on WebSockets with the rules engine shared between client and server, turn timers, forfeits that survive a restart, and a probability-map bot at the top level.
- **Ship in minutes, roll back with `git revert`.** CI checks and rebuilds only affected apps, pushes images to GHCR and commits tags to [gitops](https://github.com/outegro-dev/gitops); Argo CD runs migrations and rolls out. A one-service change reaches production in about 6 minutes.
- **Operated, not just deployed.** Prometheus, Loki and Grafana; structured logs with correlation IDs; a watchdog that sends alerts to Telegram; Sealed Secrets in git; a tested restore from backup in 81 seconds.
- **Tested end to end.** 1,600+ automated tests: unit, integration on Testcontainers against real PostgreSQL, Valkey and RabbitMQ, and Playwright across Chromium, Firefox and WebKit with axe accessibility checks.

## By the numbers

<table>
  <tr>
    <td align="center"><b>6</b><br/><sub>web apps</sub></td>
    <td align="center"><b>5</b><br/><sub>backend services</sub></td>
    <td align="center"><b>9</b><br/><sub>shared packages</sub></td>
    <td align="center"><b>1,600+</b><br/><sub>automated tests</sub></td>
    <td align="center"><b>~6 min</b><br/><sub>merge → production</sub></td>
    <td align="center"><b>81 s</b><br/><sub>verified restore</sub></td>
  </tr>
</table>

## Stack

**Frontend** — Next.js 16 · React 19 · TypeScript 6 · Tailwind CSS 4 · Radix UI · MobX · next-intl · Three.js / R3F<br/>
**Backend** — Node.js 24 · NestJS 12 · Zod 4 · Drizzle ORM · WebSockets · jose · SimpleWebAuthn<br/>
**Data & messaging** — PostgreSQL 18 (CloudNativePG) · Valkey 9 · RabbitMQ 4 · Cloudflare R2<br/>
**Infrastructure** — K3s · Traefik · cert-manager · Argo CD · Sealed Secrets · GitHub Actions · Docker Buildx Bake · Cloudflare<br/>
**Observability** — Prometheus · Grafana · Loki + Alloy · Pino<br/>
**Quality** — Vitest · Testcontainers · Playwright · axe-core · Biome · pnpm + Turborepo

The reasoning behind every choice is on [outegro.dev/stack](https://outegro.dev/stack).

## Repositories

| Repository | |
|---|---|
| **[platform](https://github.com/outegro-dev/platform)** | Monorepo with all apps, services and shared packages, CI, specs and ADRs |
| **[gitops](https://github.com/outegro-dev/gitops)** | Desired state of the production cluster: Argo CD apps, platform components, node bootstrap |

## Built by

**Nick Lukashik** — senior full-stack engineer, Tbilisi.

[![GitHub](https://img.shields.io/badge/GitHub-coping--barrel-1b1915?logo=github)](https://github.com/coping-barrel)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Nick%20Lukashik-0A66C2?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/nick-lukashick/)
[![Telegram](https://img.shields.io/badge/Telegram-@coping__barrel-26A5E4?logo=telegram&logoColor=white)](https://t.me/coping_barrel)
[![Email](https://img.shields.io/badge/Email-coping.barrel@gmail.com-cf3528?logo=gmail&logoColor=white)](mailto:coping.barrel@gmail.com)
[![Book a call](https://img.shields.io/badge/Book-a%2030--min%20call-f5c518?labelColor=1b1915)](https://calendly.com/nick-lukashik/30min)
