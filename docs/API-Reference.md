# API Reference

The Zentrix Platform exposes a versioned **REST API** that third-party developers can use to integrate their games, build companion apps, or extend the platform.

---

## Base URLs

| Environment | Base URL |
|---|---|
| Production | `https://api.zentrixgame.com/v1` |
| Sandbox | `https://sandbox.api.zentrixgame.com/v1` |

All requests must be made over **HTTPS**. HTTP requests are redirected with `301`.

---

## Machine-Readable Specification

The complete API is described as an **OpenAPI 3.0** document:

📄 **[openapi.yaml](openapi.yaml)** — download and import into any OpenAPI-compatible tool (Swagger UI, Postman, Insomnia, Stoplight, etc.)

```bash
# Preview locally with Swagger UI via Docker
docker run -p 8080:8080 \
  -e SWAGGER_JSON=/spec/openapi.yaml \
  -v $(pwd)/docs:/spec \
  swaggerapi/swagger-ui
# then open http://localhost:8080
```

---

## Authentication

Zentrix supports two authentication schemes:

### 1. User Bearer Token (OAuth 2.0)

For actions on behalf of a user (e.g. unlocking achievements, reading library).

**Step 1 — Redirect the user:**

```
GET https://zentrixgame.com/oauth/authorize
  ?client_id=YOUR_CLIENT_ID
  &redirect_uri=https://yourapp.com/callback
  &response_type=code
  &scope=profile library achievements
  &state=RANDOM_STATE
```

**Step 2 — Exchange the code:**

```http
POST https://api.zentrixgame.com/v1/oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&code=AUTH_CODE
&redirect_uri=https://yourapp.com/callback
&client_id=YOUR_CLIENT_ID
&client_secret=YOUR_CLIENT_SECRET
```

**Response:**

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiJ9…",
  "token_type": "Bearer",
  "expires_in": 900,
  "refresh_token": "zxrt_01HX…",
  "scope": "profile library achievements"
}
```

Use the token in the `Authorization` header:

```
Authorization: Bearer eyJhbGciOiJSUzI1NiJ9…
```

### 2. Developer API Key (Server-to-Server)

For server-side calls that don't act on behalf of a user (e.g. uploading builds, publishing store pages).

Generate a key in **Developer Portal → Settings → API Keys**.

```
Authorization: ApiKey zxk_01HX9KQPW3…
```

---

## Common HTTP Status Codes

| Code | Meaning |
|---|---|
| `200 OK` | Successful GET / PATCH |
| `201 Created` | Successful POST that created a resource |
| `204 No Content` | Successful DELETE |
| `400 Bad Request` | Validation error — check the `errors` array in the response body |
| `401 Unauthorized` | Missing or invalid token / API key |
| `403 Forbidden` | Token valid but insufficient scope or ownership |
| `404 Not Found` | Resource does not exist |
| `409 Conflict` | Resource already exists (e.g. duplicate achievement unlock) |
| `422 Unprocessable Entity` | Semantic validation failure |
| `429 Too Many Requests` | Rate limit exceeded |
| `500 Internal Server Error` | Platform error — open a support ticket if persistent |

**Error body format:**

```json
{
  "error": "validation_error",
  "message": "appId is required",
  "errors": [
    { "field": "appId", "message": "This field is required." }
  ],
  "requestId": "req_01HX…"
}
```

---

## Endpoint Groups

### 🔐 Auth & Identity

| Method | Path | Description |
|---|---|---|
| `GET` | `/oauth/authorize` | Begin OAuth 2.0 authorisation flow |
| `POST` | `/oauth/token` | Exchange code or refresh token |
| `POST` | `/oauth/revoke` | Revoke a token |
| `GET` | `/users/me` | Get the authenticated user's profile |

### 📋 Catalog

| Method | Path | Description |
|---|---|---|
| `GET` | `/apps` | List/search games in the catalog |
| `GET` | `/apps/{appId}` | Get a specific game |
| `POST` | `/apps` | Create a new app (Developer API key required) |
| `PATCH` | `/apps/{appId}` | Update app metadata |
| `DELETE` | `/apps/{appId}` | Delete (unpublish) an app |

### 📚 Library & Entitlements

| Method | Path | Description |
|---|---|---|
| `GET` | `/library` | List games in the authenticated user's library |
| `GET` | `/library/{appId}` | Check ownership of a specific app |
| `POST` | `/library` | Add a free game to the user's library |

### 🏗️ Builds & Depots

| Method | Path | Description |
|---|---|---|
| `GET` | `/apps/{appId}/builds` | List builds for an app |
| `POST` | `/apps/{appId}/builds` | Upload a new build |
| `GET` | `/apps/{appId}/builds/{buildId}` | Get build details |
| `POST` | `/apps/{appId}/builds/{buildId}/promote` | Promote a build to a branch |

### 🏆 Achievements

| Method | Path | Description |
|---|---|---|
| `GET` | `/apps/{appId}/achievements` | List all achievements for an app |
| `POST` | `/apps/{appId}/achievements` | Create an achievement (Developer only) |
| `POST` | `/achievements/unlock` | Unlock an achievement for the current user |
| `GET` | `/users/{userId}/achievements` | Get a user's unlocked achievements |

### 📊 Leaderboards

| Method | Path | Description |
|---|---|---|
| `GET` | `/apps/{appId}/leaderboards` | List leaderboards for an app |
| `POST` | `/apps/{appId}/leaderboards` | Create a leaderboard (Developer only) |
| `POST` | `/leaderboards/{leaderboardId}/scores` | Submit a score |
| `GET` | `/leaderboards/{leaderboardId}/scores` | Fetch scores (global or friends) |

### 👥 Social

| Method | Path | Description |
|---|---|---|
| `GET` | `/users/me/friends` | List friends |
| `POST` | `/users/me/friends` | Send a friend request |
| `DELETE` | `/users/me/friends/{userId}` | Remove a friend |
| `GET` | `/users/{userId}/presence` | Get a user's online presence |

---

## Pagination

List endpoints return paginated results using **cursor-based pagination**:

```
GET /v1/apps?limit=20&cursor=eyJpZCI6IjAxSFg5S1FQVzMifQ==
```

Response:

```json
{
  "data": [ … ],
  "pagination": {
    "limit": 20,
    "nextCursor": "eyJpZCI6IjAxSFk3…",
    "hasMore": true
  }
}
```

---

## Versioning

The API version is part of the URL path (`/v1/`). Breaking changes are introduced in a new major version. We announce deprecations at least **6 months** in advance via the [changelog](../CHANGELOG.md) and via email to registered developers.

---

## SDK & Code Samples

Rather than calling the REST API directly from inside your game, consider using the **[Zentrix SDK](SDK.md)** which wraps the API and handles token management, retries, and platform-specific features.
