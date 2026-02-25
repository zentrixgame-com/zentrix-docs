# Getting Started

This guide walks you through setting up Zentrix as a **player** or a **game developer** in under 10 minutes.

---

## For Players

### 1. Create an Account

Visit [https://zentrixgame.com/signup](https://zentrixgame.com/signup) and register with your email address, or sign in with GitHub / Google OAuth.

### 2. Download the Desktop Client

| Platform | Download |
|---|---|
| Windows (64-bit) | [ZentrixSetup-x64.exe](https://zentrixgame.com/download/win64) |
| macOS (universal) | [Zentrix.dmg](https://zentrixgame.com/download/macos) |
| Linux (AppImage) | [Zentrix.AppImage](https://zentrixgame.com/download/linux) |
| Web Client | [play.zentrixgame.com](https://play.zentrixgame.com) |

### 3. Browse the Store

After logging in, the **Store** tab shows featured, trending, and free games. Use the search bar or filter by genre, tags, and price.

### 4. Install and Play

Click **Add to Library**, then **Install** in your Library tab. The client handles downloads, patches, and launching.

---

## For Game Developers

### Prerequisites

- Node.js ≥ 18 or Python ≥ 3.10 (CLI depends on one of these)
- A free [Zentrix Developer Account](https://zentrixgame.com/dev/signup)

### 1. Install the Zentrix CLI

```bash
# via npm
npm install -g @zentrix/cli

# via pip (alternative)
pip install zentrix-cli
```

Verify the installation:

```bash
zentrix --version
# zentrix-cli v1.0.0
```

### 2. Authenticate

```bash
zentrix login
# Opens a browser window — complete the OAuth flow, then return to the terminal
```

### 3. Initialise Your Game Project

```bash
zentrix init my-awesome-game
cd my-awesome-game
```

This creates the following scaffold:

```
my-awesome-game/
├── zentrix.json          # App manifest (App ID, name, depots, …)
├── builds/               # Build artefacts go here
│   └── .gitkeep
└── README.md
```

### 4. Configure `zentrix.json`

```json
{
  "appId": "app_YOUR_APP_ID",
  "name": "My Awesome Game",
  "shortDescription": "An incredible adventure.",
  "platforms": ["windows", "macos", "linux"],
  "depots": [
    {
      "name": "base",
      "platforms": ["windows", "macos", "linux"],
      "path": "builds/base"
    }
  ]
}
```

### 5. Upload a Build

```bash
zentrix build upload --depot base --path ./builds/base
# Uploading… ████████████████████ 100%
# Build uploaded. Build ID: bld_01HX…
```

### 6. Publish to the Store

```bash
zentrix store publish --draft
# Draft saved. Review at https://zentrixgame.com/dev/apps/app_YOUR_APP_ID/store
```

Review the store page in the Developer Portal and click **Submit for Review** when ready.

---

## Next Steps

| I want to… | Go to… |
|---|---|
| Understand the full platform | [Platform Overview](Platform-Overview.md) |
| Add achievements to my game | [Game Integration Guide](Game-Integration-Guide.md) |
| Call the API directly | [API Reference](API-Reference.md) |
| Use the SDK in my game engine | [SDK Documentation](SDK.md) |
