# Changelog

All notable changes to SOCKS are documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.2.1] - 2026-10-19

Routine base image refresh, with patch version bump in `node` layer. No source or behavioural changes.

### Changed

- Base image `node:26-alpine` re-pinned to a newer digest.
- Node.js 26.12.0 → 26.12.1 (from the base image)
- The README stack-version table is updated to match.

### Notes

- The applied patch for NodeJS is the currently available remediation for [CVE-2026-111497](https://www.aikido.dev/blog/node2shell-nodejs-brotli-rce-cve-2026-111497) a.k.a. Node2Shell

### Stack

| Component  | Version |
| ---------- | ------- |
| Alpine     | 3.24.2  |
| Node.js    | 26.12.0 |
| npm        | 11.20.0 |
| Express    | 5.3.0   |
| TypeScript | 7.0.2   |


## [2.2.0] - 2026-10-19

Routine base image refresh, with minor version bump in `node` layer. No source or behavioural changes.

### Changed

- Base image `node:26-alpine` re-pinned to a newer digest.
- Node.js 26.11.1 → 26.12.0 (from the base image).
- The README stack-version table is updated to match.

### Stack

| Component  | Version |
| ---------- | ------- |
| Alpine     | 3.24.2  |
| Node.js    | 26.12.0 |
| npm        | 11.20.0 |
| Express    | 5.3.0   |
| TypeScript | 7.0.2   |


## [2.1.0] - 2026-10-13

Routine dependency and base image refresh, with minor version bumps in `node` and `express` layers. No source or behavioural changes.

### Changed

- Base image `node:26-alpine` re-pinned to a newer digest.
- Node.js 26.10.0 → 26.11.1 and npm 11.19.1 → 11.20.0 (from the base image).
- `express` 5.2.1 → 5.3.0. This also moves its transitive dependencies: `body-parser` 2.3.0, `content-disposition` 2.0.1, `content-type` 2.1.0 (now a single hoisted copy instead of several), `proxy-addr` 2.0.8, `qs` 6.16.0 and `type-is` 2.1.0.
- `express-rate-limit` 8.7.0 → 8.7.1.
- `@types/node` 26.6.4 → 26.6.5.
- `ip-address` 10.7.3 → 10.7.4 (transitive).
- The README stack-version table is updated to match.

### Stack

| Component  | Version |
| ---------- | ------- |
| Alpine     | 3.24.2  |
| Node.js    | 26.11.1 |
| npm        | 11.20.0 |
| Express    | 5.3.0   |
| TypeScript | 7.0.2   |


## [2.0.1] - 2026-10-6

### Changed

- Base image `node:26-alpine` re-pinned to a newer digest.


## [2.0.0] - 2026-10-5

Moves the runtime to Node.js 26, which becomes LTS on 2026-10-28. This is a breaking change for the host-execution approach so per semver we are incrementing a major release version.

### Changed

- Base image is now `node:26-alpine`, pinned by digest.
- `engines.node` is now `>=26`. **Running from the host requires Node.js 26 or newer;** Node 24 hosts should stay on 1.0.0 or upgrade Node. Containerized operators are unaffected.
- `@types/node` is now `^26`, matching the runtime.
- The README stack-version table is updated for the new Alpine, Node.js and npm versions.

### Stack

| Component  | Version |
| ---------- | ------- |
| Alpine     | 3.24.2  |
| Node.js    | 26.10.0 |
| npm        | 11.19.1 |
| Express    | 5.2.1   |
| TypeScript | 7.0.2   |


## [1.0.0] - 2026-10-04

First public release.

### Added

- Static file server on Express 5, written in TypeScript and published as ESM. It can run directly with `node src/index.ts` (Node.js 24+ type stripping) or compiled to host or container.
- Configuration through environment variables: `PORT`, `HOST`, `STATIC_DIR`, `TRUST_PROXY`, `CACHE_MAX_AGE`, `ALLOWED_ORIGINS`.
- `GET /healthz` health check. It is registered before the static handler, so no file can shadow it.
- Security headers through Helmet:
  - Content Security Policy (CSP)
  - HSTS: 30 days, without `includeSubDomains` or `preload`
  - `Referrer-Policy: no-referrer`
  - `Cross-Origin-Resource-Policy: same-site`
  - `X-Frame-Options` / `frame-ancestors 'self'`
  - `nosniff`
- Per-IP rate limiting at 6000 requests/minute, with standard `RateLimit` headers.
- Opt-in CORS for an exact-match allowlist of origins.
- gzip compression and configurable `Cache-Control` max-age.
- One log line per request, with the path only. Query strings are never logged.
- Graceful shutdown on `SIGTERM`/`SIGINT`:
  - idle connections are closed immediately;
  - the process force-exits after 5 s if connections are still open;
  - a second signal exits immediately.
- Slow-client protection: request headers must arrive within 5 s and the full request within 10 s.
- Multi-stage Docker image on `node:24-alpine`, pinned by digest. It runs as a non-root user with no login shell and includes a built-in health check.
- Docker Compose file with a read-only root filesystem, `cap_drop: ALL`, `no-new-privileges`, `init`, memory/CPU/PID limits and log rotation. A commented-out `traefik_backend` network is included for running behind Traefik.
- `scripts/smoke-test.sh` (`npm run smoke`): 27 behavioural checks against a built image.
- `scripts/versions.sh` (`npm run versions`): prints the tracked stack versions and all dependency versions.
- Dependabot configuration for npm, Docker and GitHub Actions.

### Security

- `TRUST_PROXY=true` is **rejected at startup**, because it would let clients spoof their IP through `X-Forwarded-For` and bypass rate limiting. Use a hop count (e.g. `1`) or a list of proxy IPs/subnets.
- Dotfiles and path-traversal attempts return 404.
- Symlinks inside `STATIC_DIR` are followed, including ones that point outside it. This is intentional and documented; review the directory contents before serving them.

### Stack

| Component  | Version |
| ---------- | ------- |
| Alpine     | 3.24.2  |
| Node.js    | 24.21.0 |
| npm        | 11.19.0 |
| Express    | 5.2.1   |
| TypeScript | 7.0.2   |


[2.2.1]: https://github.com/315Concepts/SOCKS-server/releases/tag/v2.2.1
[2.2.0]: https://github.com/315Concepts/SOCKS-server/releases/tag/v2.2.0
[2.1.0]: https://github.com/315Concepts/SOCKS-server/releases/tag/v2.1.0
[2.0.1]: https://github.com/315Concepts/SOCKS-server/releases/tag/v2.0.1
[2.0.0]: https://github.com/315Concepts/SOCKS-server/releases/tag/v2.0.0
[1.0.0]: https://github.com/315Concepts/SOCKS-server/releases/tag/v1.0.0
