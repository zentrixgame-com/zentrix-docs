# Zentrix Platform Documentation

Welcome to the official **Zentrix** documentation hub. Zentrix is an open, community-driven game distribution platform — think Steam, but transparent and extensible.

---

## 📖 Table of Contents

| # | Page | Description |
|---|---|---|
| 1 | [Getting Started](Getting-Started.md) | Install the client, create an account, and browse the store |
| 2 | [Platform Overview](Platform-Overview.md) | Architecture, core services, and technical design |
| 3 | [Game Integration Guide](Game-Integration-Guide.md) | Publish your game: onboarding, build pipeline, store listing |
| 4 | [API Reference](API-Reference.md) | REST API overview, authentication, and endpoint catalogue |
| 5 | [OpenAPI Specification](openapi.yaml) | Machine-readable OpenAPI 3.0 spec (YAML) |
| 6 | [SDK Documentation](SDK.md) | Official Zentrix SDK — installation, usage, and examples |
| 7 | [FAQ](FAQ.md) | Frequently asked questions |

---

## 🗺️ Platform at a Glance

```
┌────────────────────────────────────────────┐
│                  Zentrix                   │
│         Open Game Distribution Platform   │
├──────────────┬────────────────┬────────────┤
│   Store &    │    Library &   │  Community │
│  Discovery   │   Downloads    │  & Social  │
├──────────────┼────────────────┼────────────┤
│  Dev Portal  │  Build/Deploy  │   REST API │
│  & SDK       │   Pipeline     │   & OAuth  │
└──────────────┴────────────────┴────────────┘
```

### Core Pillars

| Pillar | Description |
|---|---|
| 🛒 **Store** | Browse, search, wishlist, and purchase games |
| 📦 **Library** | Download, update, and launch your owned games |
| 🏆 **Achievements** | Cross-game achievement tracking and leaderboards |
| 👥 **Social** | Friends, chat, activity feeds, and groups |
| 🎮 **Developer Portal** | Upload builds, manage store pages, view analytics |
| 🔌 **Open API** | Full REST API so any developer can integrate with Zentrix |

---

## 🔑 Key Concepts

- **App ID** — unique identifier assigned to every game on Zentrix (e.g. `app_01HX…`)
- **Build** — a versioned, platform-specific artefact of your game uploaded via the developer portal or CLI
- **Depot** — content delivery storage bucket associated with a build
- **Achievement** — a named milestone that a game can unlock for a player at runtime
- **Entitlement** — a user's ownership record for a specific app

---

## 🚀 Quick Start (30 seconds)

```bash
# Install the Zentrix CLI
npm install -g @zentrix/cli

# Authenticate
zentrix login

# Initialise a new game project
zentrix init my-awesome-game
```

➡️ See the full [Getting Started](Getting-Started.md) guide for next steps.

---

## 📬 Support

| Channel | Link |
|---|---|
| Documentation issues | [GitHub Issues](https://github.com/zentrixgame-com/zentrix-docs/issues) |
| Developer support | developers@zentrixgame.com |
| Community forum | https://community.zentrixgame.com |
| Platform status | https://status.zentrixgame.com |
