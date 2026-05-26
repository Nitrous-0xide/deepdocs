# Discord Internal API Documentation

> [!WARNING]
> This documentation is unofficial and based on observed behavior of the Discord web client.
>
> Endpoints, headers, payloads, and behaviors described here may change at any time without notice.
>
> Automating user accounts ("selfbots") violates Discord's Terms of Service.

---

# Overview

Discord's web application communicates with several internal APIs in addition to the officially documented bot/developer API.

These APIs are used by:

- The browser client
- Desktop applications
- Mobile applications
- Experimentation systems
- Telemetry systems
- Gateway services

This repository documents observed behavior of these systems for research and educational purposes.

---

# Base URLs

## REST API

```text
https://discord.com/api/v10
```

Older clients may still use:

```text
https://discord.com/api/v9
```

---

## CDN

```text
https://cdn.discordapp.com
```

Used for:

- Attachments
- Avatars
- Banners
- Emojis
- Stickers

---

## Gateway

```text
wss://gateway.discord.gg
```

Used for realtime communication.

---

# Authentication

## Bot Tokens

Official bot authentication format:

```http
Authorization: Bot TOKEN
```

Example:

```http
Authorization: Bot MTAxMjM0NTY3ODkw...
```

---

## User Tokens

The web client authenticates using:

```http
Authorization: USER_TOKEN
```

No `Bot` prefix is used.

> [!CAUTION]
> Using user tokens programmatically violates Discord Terms of Service.

---

# Common Headers

## x-super-properties

Base64 encoded JSON blob identifying the client.

Example decoded payload:

```json
{
  "os": "Linux",
  "browser": "Chrome",
  "device": "",
  "system_locale": "en-GB",
  "browser_user_agent": "Mozilla/5.0 ...",
  "browser_version": "138.0.0.0",
  "os_version": "",
  "release_channel": "stable",
  "client_build_number": 550218
}
```

### Purpose

Used for:

- Analytics
- Experiment assignment
- Client identification
- Anti-abuse systems
- Feature rollouts

---

## x-context-properties

Base64 encoded context metadata.

Example decoded payload:

```json
{
  "location": "chat_input"
}
```

### Known Locations

| Value | Meaning |
|---|---|
| `chat_input` | Sending message |
| `friends_list` | Friends page |
| `user_profile` | Viewing profile |
| `guild_header` | Guild interaction |

---

## x-debug-options

Observed values:

```text
bugReporterEnabled
```

Likely internal debugging configuration.

---

## x-discord-locale

Example:

```http
x-discord-locale: en-GB
```

Controls localization.

---

## x-discord-timezone

Example:

```http
x-discord-timezone: Europe/London
```

Used for scheduling and events.

---

# Cookies

Common observed cookies:

| Cookie | Purpose |
|---|---|
| `__dcfduid` | Session tracking |
| `__sdcfduid` | Security tracking |
| `_cfuvid` | Cloudflare |
| `cf_clearance` | Cloudflare challenge |

These are generally unnecessary for standard bot API usage.

---

# Channels

# Fetch Messages

```http
GET /channels/{channel_id}/messages
```

## Query Parameters

| Parameter | Description |
|---|---|
| `limit` | Max results (1-100) |
| `before` | Messages before ID |
| `after` | Messages after ID |
| `around` | Messages around ID |

## Example

```bash
curl \
  -H "Authorization: Bot TOKEN" \
  "https://discord.com/api/v10/channels/123/messages?limit=50"
```

---

# Send Message

```http
POST /channels/{channel_id}/messages
```

## Request Body

```json
{
  "content": "hello world"
}
```

## Extended Example

```json
{
  "content": "test",
  "nonce": "1508890385336762368",
  "tts": false,
  "flags": 0
}
```

## Example Request

```bash
curl \
  -X POST \
  -H "Authorization: Bot TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"content":"hello"}' \
  "https://discord.com/api/v10/channels/123/messages"
```

---

# Typing Indicator

```http
POST /channels/{channel_id}/typing
```

No body required.

## Example

```bash
curl \
  -X POST \
  -H "Authorization: Bot TOKEN" \
  "https://discord.com/api/v10/channels/123/typing"
```

---

# Attachments

Discord uses a multi-stage upload system.

## Step 1 — Request Upload Slot

```http
POST /channels/{channel_id}/attachments
```

## Example Request

```json
{
  "files": [
    {
      "filename": "image.png",
      "file_size": 12345,
      "id": "0"
    }
  ]
}
```

## Step 2 — Upload File

Upload directly to returned storage URL.

## Step 3 — Send Message

Reference uploaded attachment in message payload.

---

# Gateway

# Get Gateway URL

```http
GET /gateway
```

Bot version:

```http
GET /gateway/bot
```

---

# Gateway Lifecycle

## 1. Connect

Connect to:

```text
wss://gateway.discord.gg/?v=10&encoding=json
```

---

## 2. Hello

Server sends opcode `10`.

Example:

```json
{
  "op": 10,
  "d": {
    "heartbeat_interval": 41250
  }
}
```

---

## 3. Identify

Client sends opcode `2`.

Example:

```json
{
  "op": 2,
  "d": {
    "token": "TOKEN",
    "properties": {
      "os": "linux",
      "browser": "chrome",
      "device": "pc"
    }
  }
}
```

---

## 4. Heartbeats

Client periodically sends opcode `1`.

---

# Gateway Opcodes

| Opcode | Name |
|---|---|
| 0 | Dispatch |
| 1 | Heartbeat |
| 2 | Identify |
| 6 | Resume |
| 7 | Reconnect |
| 9 | Invalid Session |
| 10 | Hello |
| 11 | Heartbeat ACK |

---

# Rate Limits

Discord uses bucketed rate limiting.

## Headers

| Header | Description |
|---|---|
| `X-RateLimit-Limit` | Max requests |
| `X-RateLimit-Remaining` | Remaining requests |
| `X-RateLimit-Reset` | Reset timestamp |
| `Retry-After` | Retry delay |
| `X-RateLimit-Bucket` | Bucket identifier |

---

## 429 Response

```json
{
  "message": "You are being rate limited.",
  "retry_after": 2.5,
  "global": false
}
```

---

# Experiments API

Observed endpoint:

```http
GET /experiments
```

Returns:

- Feature flags
- Rollout buckets
- Experiment assignments

Often tied to:

- Build number
- Account state
- Fingerprinting data
- Client telemetry

---

# Presence

Presence updates are sent over Gateway.

Example:

```json
{
  "op": 3,
  "d": {
    "since": 0,
    "activities": [],
    "status": "online",
    "afk": false
  }
}
```

---

# Anti-Abuse Systems

Discord appears to evaluate:

- TLS fingerprints
- Browser consistency
- Request timing
- Gateway behavior
- Cookie integrity
- Invalid request counts
- Client telemetry

This is why replayed browser requests often fail outside real browsers.

---

# Client Build Numbers

Discord frequently updates:

- Client build numbers
- Fingerprint formats
- Telemetry schemas
- Experiment payloads

Hardcoded browser metadata may stop functioning after updates.

---

# Recommended Usage

For stability and compliance:

- Use bot accounts
- Use the documented API
- Use official libraries
- Avoid replaying browser requests

Official resources:

- https://discord.com/developers/docs/intro
- https://discord.com/developers/docs/reference

---

# Legal

Discord is a trademark of Discord Inc.

This repository is unofficial and intended solely for educational and interoperability research purposes.
