# Platform Overview

This page describes the high-level architecture of the Zentrix platform, its core services, and the design principles behind them.

---

## Architecture Overview

Zentrix follows a **microservices** architecture, with each domain exposed as an independent service behind a unified API gateway.

```
                        ┌───────────────────┐
                        │   API Gateway     │  api.zentrixgame.com/v1
                        │  (Auth, Rate-Lmt) │
                        └────────┬──────────┘
              ┌─────────────────┼───────────────────┐
              │                 │                   │
     ┌────────▼───────┐ ┌──────▼──────┐ ┌──────────▼────────┐
     │  Identity &    │ │   Catalog   │ │   Library /        │
     │  Auth Service  │ │   Service   │ │   Entitlement Svc  │
     └────────────────┘ └─────────────┘ └────────────────────┘
              │                 │                   │
     ┌────────▼───────┐ ┌──────▼──────┐ ┌──────────▼────────┐
     │  Social &      │ │  Build /    │ │   Achievements &   │
     │  Friends Svc   │ │  Depot Svc  │ │   Leaderboards Svc │
     └────────────────┘ └─────────────┘ └────────────────────┘
```

All services communicate internally over gRPC. External consumers (clients, games, third-party apps) only ever interact with the REST API gateway.

---

## Core Services

### Identity & Auth Service

Handles user registration, login, and session management.

- **OAuth 2.0 / OpenID Connect** for third-party logins (GitHub, Google)
- **JWT access tokens** (15 min expiry) + **refresh tokens** (30-day rolling)
- Two-factor authentication (TOTP and WebAuthn/passkeys)
- Developer API keys for server-to-server authentication

### Catalog Service

Manages all game listings, store pages, and metadata.

- Game search (full-text + faceted filters)
- Tags, genres, categories, age ratings
- Pricing, discounts, regional pricing
- Media assets: screenshots, trailers, capsule images

### Library & Entitlement Service

Records what each user owns and handles install/uninstall events.

- Entitlement grants on purchase
- Cloud save sync (per-game, per-user)
- Play time tracking
- Family sharing

### Build & Depot Service

Manages game build artefacts and content delivery.

- Builds uploaded via CLI or REST API
- Depot-based content chunking (delta updates)
- CDN-backed download at the edge
- Version pinning (players can rollback to older builds)

### Achievements & Leaderboards Service

Provides a cross-game achievement and ranking system.

- Games call the REST API (or use the SDK) to unlock achievements at runtime
- Global and friend-filtered leaderboards per score type
- Achievement badges shown on user profiles

### Social & Friends Service

Powers the community layer.

- Friend requests and friend lists
- Presence (online/in-game/offline)
- Chat (1-to-1 and group)
- Activity feed (achievements, recent play, reviews)

---

## Data Flow: Purchasing a Game

```
Player        Client          API Gateway       Catalog Svc      Entitlement Svc
  │             │                   │                │                  │
  │  Click Buy  │                   │                │                  │
  │────────────►│                   │                │                  │
  │             │  POST /purchases  │                │                  │
  │             │──────────────────►│                │                  │
  │             │                   │ validate price │                  │
  │             │                   │───────────────►│                  │
  │             │                   │◄───────────────│                  │
  │             │                   │         grant entitlement         │
  │             │                   │──────────────────────────────────►│
  │             │                   │◄──────────────────────────────────│
  │             │◄──────────────────│                                   │
  │  Game added │                   │                                   │
  │◄────────────│                   │                                   │
```

---

## Technology Stack

| Layer | Technology |
|---|---|
| API Gateway | Kong |
| Backend services | Go (primary), Node.js (tooling) |
| Database | PostgreSQL (transactional), Redis (cache/sessions) |
| Search | Elasticsearch |
| Object storage | S3-compatible (builds/depots) |
| CDN | Cloudflare |
| Message bus | NATS JetStream |
| Container orchestration | Kubernetes |

---

## Environments

| Environment | Base URL | Notes |
|---|---|---|
| Production | `https://api.zentrixgame.com/v1` | Live data |
| Sandbox | `https://sandbox.api.zentrixgame.com/v1` | Free test environment — no real purchases |
| Local (dev) | `http://localhost:3000/v1` | Docker Compose stack |

---

## Rate Limiting

| Token type | Requests / minute |
|---|---|
| Unauthenticated | 30 |
| User JWT | 300 |
| Developer API key | 1 000 |
| Partner (whitelisted) | 10 000 |

Rate-limit headers returned on every response:

```
X-RateLimit-Limit: 300
X-RateLimit-Remaining: 297
X-RateLimit-Reset: 1714000060
```

---

## Security

- All traffic is TLS 1.2+ (TLS 1.3 preferred)
- API keys are stored as bcrypt hashes
- PII is encrypted at rest (AES-256-GCM)
- Vulnerability disclosures: **security@zentrixgame.com**
