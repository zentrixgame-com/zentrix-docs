# SDK Documentation

The **Zentrix SDK** provides idiomatic access to platform features (achievements, leaderboards, cloud saves, overlay, friends) from within your game, without needing to manage HTTP requests or tokens manually.

---

## Supported Engines & Languages

| Engine / Runtime | Language | Package |
|---|---|---|
| Unity | C# | `com.zentrix.sdk` (Unity Package Manager) |
| Unreal Engine 5 | C++ | Zentrix UE5 Plugin (Marketplace) |
| Godot 4 | GDScript / C# | `addons/zentrix-sdk` |
| Native Windows | C++ | `ZentrixSDK.dll` |
| Web / Electron | JavaScript / TypeScript | `npm install @zentrix/sdk` |
| Server-side | Node.js | `npm install @zentrix/server-sdk` |
| Server-side | Python | `pip install zentrix-sdk` |

---

## Installation

### Unity

1. Open **Window → Package Manager → + → Add package from git URL**:
   ```
   https://github.com/zentrixgame-com/zentrix-unity-sdk.git
   ```
2. The `ZentrixSDK` namespace is now available in your scripts.

### Unreal Engine 5

1. Download `ZentrixPlugin_UE5_x.y.z.zip` from the [Developer Portal](https://zentrixgame.com/dev/sdk).
2. Extract to `Plugins/Zentrix/` inside your project.
3. Enable the plugin in **Edit → Plugins → Zentrix**.
4. Add `"Zentrix"` to your module's `PublicDependencyModuleNames`.

### JavaScript / TypeScript

```bash
npm install @zentrix/sdk
# or
yarn add @zentrix/sdk
```

### Python (server-side)

```bash
pip install zentrix-sdk
```

---

## Initialisation

All SDK calls require the SDK to be initialised with your **App ID** before use. Call `Init` as early as possible in your game's startup sequence.

### Unity (C#)

```csharp
using Zentrix;

public class GameManager : MonoBehaviour
{
    void Awake()
    {
        ZentrixSDK.Init("app_01HX9KQPW3");
    }
}
```

### Unreal Engine (C++)

```cpp
#include "ZentrixSDK.h"

void UMyGameInstance::Init()
{
    Super::Init();
    FZentrixSDK::Init(TEXT("app_01HX9KQPW3"));
}
```

### JavaScript

```js
import { ZentrixSDK } from '@zentrix/sdk';

await ZentrixSDK.init('app_01HX9KQPW3');
```

### Python (server-side)

```python
from zentrix import ZentrixSDK

sdk = ZentrixSDK(api_key="zxk_your_key", app_id="app_01HX9KQPW3")
```

---

## Authentication

The SDK handles token management automatically when running inside the Zentrix client. If running outside the client (e.g. a standalone web build), you must supply an access token:

```js
await ZentrixSDK.init('app_01HX9KQPW3', { accessToken: 'eyJhbGci…' });
```

---

## Achievements

### Unlock an achievement

```csharp
// Unity — async/await
await ZentrixSDK.Achievements.UnlockAsync("first_blood");

// Unity — callback
ZentrixSDK.Achievements.Unlock("first_blood", (result) =>
{
    if (result.IsSuccess) Debug.Log("Achievement unlocked!");
    else Debug.LogError(result.Error);
});
```

```cpp
// Unreal
ZentrixSDK->GetAchievements()->UnlockAchievement(
    TEXT("first_blood"),
    FOnAchievementUnlocked::CreateUObject(this, &AMyActor::OnAchievementUnlocked)
);
```

```js
// JavaScript
await zentrix.achievements.unlock('first_blood');
```

### List unlocked achievements

```csharp
var achievements = await ZentrixSDK.Achievements.GetUnlockedAsync();
foreach (var a in achievements)
    Debug.Log($"{a.Name} — unlocked at {a.UnlockedAt}");
```

### Check a specific achievement

```csharp
bool isUnlocked = await ZentrixSDK.Achievements.IsUnlockedAsync("first_blood");
```

---

## Leaderboards

### Submit a score

```csharp
await ZentrixSDK.Leaderboards.SubmitScoreAsync("speedrun_any_pct", 183420);
```

```js
await zentrix.leaderboards.submitScore('speedrun_any_pct', 183420);
```

### Fetch top scores

```csharp
// Global top 10
var entries = await ZentrixSDK.Leaderboards.GetTopScoresAsync("speedrun_any_pct", count: 10);

// Friends only
var friendEntries = await ZentrixSDK.Leaderboards.GetFriendsScoresAsync("speedrun_any_pct");

foreach (var e in entries)
    Debug.Log($"#{e.Rank} {e.Username}: {e.Score}");
```

### Fetch score around the current user

```csharp
var nearby = await ZentrixSDK.Leaderboards.GetScoresAroundUserAsync("speedrun_any_pct", range: 5);
```

---

## Cloud Saves

Cloud saves are per-user, per-slot. Each slot can store up to **10 MB** of binary data.

### Save

```csharp
byte[] data = MyGame.Serialise();
await ZentrixSDK.CloudSave.UploadAsync("slot_1", data);
```

### Load

```csharp
byte[] data = await ZentrixSDK.CloudSave.DownloadAsync("slot_1");
if (data != null)
    MyGame.Deserialise(data);
```

### List slots

```csharp
var slots = await ZentrixSDK.CloudSave.ListSlotsAsync();
foreach (var s in slots)
    Debug.Log($"{s.SlotName}: {s.SizeBytes} bytes, saved at {s.UpdatedAt}");
```

### Delete a slot

```csharp
await ZentrixSDK.CloudSave.DeleteAsync("slot_1");
```

---

## Overlay

The Zentrix overlay provides in-game UI for achievements, friends, and the store.

```csharp
// Open overlay on a specific page
ZentrixSDK.Overlay.Open(OverlayPage.Achievements);
ZentrixSDK.Overlay.Open(OverlayPage.Friends);
ZentrixSDK.Overlay.Open(OverlayPage.Store);

// Check if overlay is visible
bool visible = ZentrixSDK.Overlay.IsVisible;

// Listen for overlay events
ZentrixSDK.Overlay.OnOpened += () => PauseGame();
ZentrixSDK.Overlay.OnClosed += () => ResumeGame();
```

The default hotkey to toggle the overlay is **Shift + Tab**. Players can change it in the Zentrix client settings.

---

## Friends & Presence

```csharp
// List friends
var friends = await ZentrixSDK.Social.GetFriendsAsync();

// Get presence for a friend
var presence = await ZentrixSDK.Social.GetPresenceAsync(friendUserId);
Debug.Log($"{presence.Username} is {presence.Status}");

// Listen for presence changes
ZentrixSDK.Social.OnPresenceChanged += (p) =>
    Debug.Log($"{p.Username} changed status to {p.Status}");
```

---

## Error Handling

All SDK methods throw (or invoke callbacks with) a `ZentrixException` on failure:

```csharp
try
{
    await ZentrixSDK.Achievements.UnlockAsync("first_blood");
}
catch (ZentrixException ex)
{
    Debug.LogError($"[Zentrix] {ex.ErrorCode}: {ex.Message}");
}
```

Common error codes:

| Code | Meaning |
|---|---|
| `not_initialised` | `ZentrixSDK.Init()` was not called |
| `not_authenticated` | No valid user session |
| `achievement_not_found` | The achievement ID does not exist for this app |
| `already_unlocked` | Achievement was already unlocked (not an error in most flows) |
| `rate_limited` | Too many requests; back off and retry |
| `network_error` | Could not reach the Zentrix API |

---

## SDK Changelog

See the platform [CHANGELOG](../CHANGELOG.md) for SDK version history.

---

## Source Code & Issues

- Unity SDK: https://github.com/zentrixgame-com/zentrix-unity-sdk
- JS/TS SDK: https://github.com/zentrixgame-com/zentrix-js-sdk
- Report bugs: https://github.com/zentrixgame-com/zentrix-docs/issues
