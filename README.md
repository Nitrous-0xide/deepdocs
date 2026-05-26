# deepdocs

> A collection of documentation for undocumented, reverse-engineered, and internal APIs.

## About

`deepdocs` is a repository focused on documenting APIs, protocols, headers, endpoints, and behaviors that are:

- undocumented
- partially documented
- internal/private
- reverse engineered
- browser-client specific
- experimentally discovered

The goal is to create high quality technical references for systems that normally lack public documentation.

This repository covers:

- Internal web APIs
- Hidden endpoints
- Gateway/WebSocket protocols
- Proprietary headers
- Mobile app APIs
- Browser telemetry systems
- Experimentation/feature flag systems
- Internal RPC mechanisms
- Legacy endpoints
- Reverse engineered protocols

---

# Philosophy

deepdocs aims to:

- preserve technical knowledge
- improve interoperability
- aid researchers and developers
- document protocol behavior
- explain undocumented systems clearly

This repository is intended for:

- education
- research
- debugging
- interoperability
- archival purposes

---

# Structure

```text
/docs
  /discord
  /spotify
  /steam
  /reddit
  /cloudflare
  /misc
```

Each service contains:

- endpoint documentation
- request/response examples
- protocol notes
- observed headers
- websocket behavior
- authentication formats
- rate limiting details
- reverse engineering notes

---

# Documentation Standards

Each document should include:

- Overview
- Base URLs
- Authentication
- Headers
- Endpoints
- Request examples
- Response examples
- Error formats
- Rate limits
- Protocol behavior
- Known quirks
- Legal/safety notes

Markdown is preferred.

---

# Example Topics

## APIs

- Discord Internal API
- Steam Client API
- Reddit Mobile API
- Spotify Partner API
- YouTube Internal API

## Protocols

- Gateway protocols
- WebSocket message formats
- RPC protocols
- Push notification systems

## Infrastructure

- Cloudflare challenge flow
- Experimentation systems
- Telemetry payloads
- Client fingerprinting

---

# Contributions

Contributions are welcome.

Useful contributions include:

- endpoint discoveries
- corrected payloads
- protocol analysis
- request captures
- websocket event mappings
- rate limit observations
- deobfuscated client logic

Please verify findings before opening pull requests.

---

# Rules

## Do not include

- leaked credentials
- private tokens
- personally identifiable information
- exploits intended for abuse
- malware
- credential theft tools

## Keep contributions focused on

- interoperability
- education
- documentation
- protocol analysis
- research

---

# Legal

All trademarks belong to their respective owners.

This repository is unofficial and not affiliated with any company documented here.

Some documented APIs may be unsupported, unstable, or prohibited for automated use under platform Terms of Service.

Use responsibly.

---

# Disclaimer

Information in this repository may become outdated quickly.

Internal APIs can change without notice.

Nothing here guarantees stability or long-term functionality.

---

# License

MIT
