# Frequently Asked Questions

---

## General

### What is Zentrix?

Zentrix is an open game distribution platform — a community-driven alternative to proprietary game stores. It lets players discover, buy, download, and play games, while giving developers a transparent and flexible publishing pipeline.

### Is Zentrix free to use for players?

Yes. Creating an account and downloading the Zentrix client is completely free. Some games may be paid.

### Is Zentrix free for game developers?

Creating a developer account is free. Zentrix takes a revenue share on paid game sales (see the [Developer Agreement](https://zentrixgame.com/legal/developer-agreement) for current rates). Free games have no platform fees.

### What platforms does the Zentrix client support?

Windows (64-bit), macOS (Intel & Apple Silicon), and Linux (x86_64). A web client is also available at [play.zentrixgame.com](https://play.zentrixgame.com).

---

## For Game Developers

### How do I publish my game?

1. Sign up for a [developer account](https://zentrixgame.com/dev/signup).
2. Create an App in the [Developer Portal](https://zentrixgame.com/dev).
3. Upload your build via the [Zentrix CLI](Getting-Started.md#for-game-developers) or REST API.
4. Fill in your store page and submit for review.

See the full [Game Integration Guide](Game-Integration-Guide.md).

### How long does the review process take?

Typically **2–5 business days**. Complex submissions or those requiring content rating clarification may take longer.

### What game engines are supported?

Any engine. Zentrix does not restrict which engine you use. The SDK has first-class support for **Unity**, **Unreal Engine 5**, and **Godot 4**. For other engines, you can call the REST API directly or use the C++ or JavaScript SDK.

### Can I release a game in Early Access?

Yes. Set `releaseType: early_access` when creating the app. Early Access games are clearly labelled in the store.

### Can I offer the game for free?

Yes, set `price: 0`. Free games are also subject to the review process.

### How does the build update system work?

When you upload a new build and promote it to the `default` branch, the Zentrix client automatically downloads a **delta patch** for all players who have the game installed. Players do not need to re-download the full game.

### How do I add achievements?

1. Define achievements in **Developer Portal → Apps → Your App → Achievements**.
2. Call `ZentrixSDK.Achievements.UnlockAsync(achievementId)` (or the REST endpoint) from within your game when the player earns the achievement.

See the [Game Integration Guide — Achievements](Game-Integration-Guide.md#achievements) and [SDK Documentation](SDK.md#achievements).

### Is there an analytics dashboard?

Yes. **Developer Portal → Analytics** shows real-time installs, daily/monthly active users, average session length, revenue, and refund rate.

---

## API & Integration

### Where can I find the API documentation?

See the [API Reference](API-Reference.md) page for a human-readable overview. Download the full [OpenAPI 3.0 specification](openapi.yaml) for machine-readable details you can import into Postman, Insomnia, or Swagger UI.

### What authentication method should I use?

| Use case | Method |
|---|---|
| Acting on behalf of a user (in-game SDK calls) | OAuth 2.0 Bearer Token |
| Server-to-server (CI/CD, build upload, store management) | Developer API Key |

### Is there a sandbox environment for testing?

Yes. Use `https://sandbox.api.zentrixgame.com/v1` as the base URL. No real purchases are made in the sandbox. Sandbox accounts and production accounts are separate.

### What is the rate limit?

| Token type | Requests / minute |
|---|---|
| Unauthenticated | 30 |
| User Bearer Token | 300 |
| Developer API Key | 1 000 |

See [Platform Overview — Rate Limiting](Platform-Overview.md#rate-limiting) for response headers.

### Can I use the API to check if a user owns my game (DRM)?

Yes. Call `GET /library/{appId}` with the user's Bearer Token. A `200` response means they own the game; a `404` means they do not. This requires the `library` OAuth scope.

---

## Cloud Saves

### How much cloud save storage does each player get?

Each save slot supports up to **10 MB** per game. The number of slots per game is unlimited (subject to fair use).

### Are cloud saves automatic?

No. Your game must explicitly call `ZentrixSDK.CloudSave.UploadAsync(slot, data)` to save, and `DownloadAsync(slot)` to load. This gives you full control over when syncs occur.

---

## Troubleshooting

### The Zentrix client won't start

- Check [https://status.zentrixgame.com](https://status.zentrixgame.com) for platform outages.
- Try running the client as administrator (Windows) or granting execute permission (Linux: `chmod +x Zentrix.AppImage`).
- Reinstall the client from [zentrixgame.com/download](https://zentrixgame.com/download).

### A game fails to download or update

1. Check your internet connection.
2. Verify there is enough disk space.
3. In the client, right-click the game → **Verify Integrity**.
4. If the issue persists, open a support ticket at [zentrixgame.com/support](https://zentrixgame.com/support).

### My API key isn't working

- Ensure you are passing it as `Authorization: ApiKey zxk_yourkey` (note the `ApiKey` prefix).
- Check the key has not been revoked in **Developer Portal → Settings → API Keys**.
- Verify you are hitting the correct environment (production vs sandbox).

---

## Contact & Support

| Type | Contact |
|---|---|
| General support | https://zentrixgame.com/support |
| Developer support | developers@zentrixgame.com |
| Security issues | security@zentrixgame.com |
| Community forum | https://community.zentrixgame.com |
| Documentation bugs | [GitHub Issues](https://github.com/zentrixgame-com/zentrix-docs/issues) |
