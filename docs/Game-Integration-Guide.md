# Game Integration Guide

This guide explains everything a game developer needs to publish a game on Zentrix, integrate platform features (achievements, leaderboards, cloud saves, overlay), and go live on the store.

---

## Table of Contents

1. [Developer Account Setup](#1-developer-account-setup)
2. [Creating an App](#2-creating-an-app)
3. [Uploading Builds](#3-uploading-builds)
4. [Integrating the SDK](#4-integrating-the-sdk)
   - [Achievements](#achievements)
   - [Leaderboards](#leaderboards)
   - [Cloud Saves](#cloud-saves)
   - [Overlay](#overlay)
5. [Store Page](#5-store-page)
6. [Review & Launch](#6-review--launch)
7. [Post-Launch](#7-post-launch)

---

## 1. Developer Account Setup

1. Sign up at [https://zentrixgame.com/dev/signup](https://zentrixgame.com/dev/signup).
2. Complete identity verification (email + optional studio verification for partners).
3. Agree to the [Zentrix Developer Agreement](https://zentrixgame.com/legal/developer-agreement).
4. Set up a payout method in **Settings → Payouts** (required before your game can be sold; free games can skip this).

---

## 2. Creating an App

In the **Developer Portal → Apps → New App**:

| Field | Description |
|---|---|
| App name | Display name for your game (2–128 chars) |
| Short description | One-line pitch (≤ 160 chars) |
| Platforms | `windows`, `macos`, `linux` (select all that apply) |
| Release type | `early_access`, `full_release` |
| Price | Numeric (USD base price); 0 for free |
| Tags | Up to 20 tags from the approved taxonomy |

After saving you receive an **App ID** (e.g. `app_01HX9KQPW3…`). Store this; it is required by the SDK and CLI.

---

## 3. Uploading Builds

### Via CLI (recommended)

```bash
# Install
npm install -g @zentrix/cli

# Login
zentrix login

# Upload a Windows build
zentrix build upload \
  --app app_01HX9KQPW3 \
  --depot windows-base \
  --path ./dist/windows \
  --version 1.0.0
```

### Via REST API

```http
POST /v1/apps/{appId}/builds
Authorization: ApiKey zxk_…
Content-Type: multipart/form-data

depot=windows-base&version=1.0.0&file=@dist/windows.zip
```

### Branch Management

| Branch | Purpose |
|---|---|
| `default` | What players receive by default |
| `beta` | Opt-in beta branch |
| `staging` | Internal testing only |

```bash
zentrix build promote \
  --app app_01HX9KQPW3 \
  --build bld_01HX… \
  --branch default
```

---

## 4. Integrating the SDK

The Zentrix SDK provides access to platform features from within your game. See the full [SDK Documentation](SDK.md) for all languages/engines.

### Initialise the SDK

All SDK calls require initialisation at startup with your **App ID**:

```csharp
// C# / Unity example
ZentrixSDK.Init("app_01HX9KQPW3");
```

```cpp
// C++ / Unreal example
FZentrixSDK::Init(TEXT("app_01HX9KQPW3"));
```

### Achievements

#### Define achievements in the Developer Portal

Go to **Apps → Your App → Achievements → Add Achievement**:

| Field | Example |
|---|---|
| Achievement ID | `first_blood` |
| Name | "First Blood" |
| Description | "Kill your first enemy." |
| Hidden | false |
| Icon (locked) | 256×256 PNG |
| Icon (unlocked) | 256×256 PNG |

#### Unlock at runtime

```csharp
// C#
await ZentrixSDK.Achievements.UnlockAsync("first_blood");
```

```cpp
// C++
ZentrixSDK->GetAchievements()->UnlockAchievement(TEXT("first_blood"), OnComplete);
```

```js
// JavaScript (Electron / web games)
await zentrix.achievements.unlock('first_blood');
```

#### REST API fallback

```http
POST /v1/achievements/unlock
Authorization: Bearer <user_access_token>
Content-Type: application/json

{
  "appId": "app_01HX9KQPW3",
  "achievementId": "first_blood"
}
```

### Leaderboards

```csharp
// Submit a score
await ZentrixSDK.Leaderboards.SubmitScoreAsync("speedrun_any_pct", 183420);

// Fetch top 10
var entries = await ZentrixSDK.Leaderboards.GetTopScoresAsync("speedrun_any_pct", count: 10);
foreach (var e in entries)
    Console.WriteLine($"{e.Rank}. {e.Username} — {e.Score}");
```

### Cloud Saves

```csharp
// Save
byte[] saveData = SerializeSaveGame();
await ZentrixSDK.CloudSave.UploadAsync("slot_1", saveData);

// Load
byte[] data = await ZentrixSDK.CloudSave.DownloadAsync("slot_1");
```

### Overlay

The Zentrix overlay provides in-game access to friends, achievements, and the store.

```csharp
// Trigger overlay programmatically
ZentrixSDK.Overlay.Open(OverlayPage.Achievements);
```

Players can also open the overlay with the default hotkey **Shift + Tab** (configurable).

---

## 5. Store Page

A compelling store page increases conversion. Required assets:

| Asset | Size / Format | Notes |
|---|---|---|
| Capsule image (small) | 231×87 px PNG | Used in search results |
| Capsule image (large) | 460×215 px PNG | Used on the homepage |
| Header image | 1920×620 px JPG/PNG | Top of store page |
| Screenshots | 1280×720 or 1920×1080 | Min 4, max 20 |
| Trailer (optional) | MP4 h.264, max 500 MB | Autoplay muted |

Fill in:
- **Full description** (Markdown supported)
- **System requirements** (min / recommended per platform)
- **Content ratings** (fill in the questionnaire for automatic PEGI/ESRB mapping)
- **Tags** and **categories**

---

## 6. Review & Launch

1. Click **Submit for Review** in the Developer Portal.
2. The Zentrix review team responds within **2–5 business days**.
3. If approved, set your **release date** or click **Release Now**.
4. Players who wishlisted the game receive a launch notification automatically.

---

## 7. Post-Launch

- **Analytics** — real-time installs, active users, and revenue in the Developer Portal under **Analytics**.
- **Updates** — upload a new build, promote it to the `default` branch, and the client patches all players automatically.
- **Pricing** — set discounts via **Apps → Your App → Pricing → Discounts**.
- **Community Hub** — automatically created; players can post reviews, guides, and artwork.
