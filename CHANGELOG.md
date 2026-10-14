# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- **Preview frontend port (8651)**: Exposes Vite dev server for live-reload frontend development in Docker
- **XAI OAuth port (56121)**: Enables XAI authentication callback flow
- **Authentication environment variables**: `AUTH_TOKEN`, `AUTH_JWT_SECRET` for explicit auth configuration
- **Profile configuration**: `PROFILE` variable for default Hermes profile selection
- **Log level control**: `LOG_LEVEL` variable (debug, info, warn, error)
- **Feature flags**:
  - `HERMES_WEB_UI_DISABLE_GATEWAY_AUTOSTART`
  - `HERMES_WEB_UI_DISABLE_MCP_AUTOINJECT`
  - `HERMES_WEB_UI_DISABLE_SKILL_INJECTION`
- **Holographic Memory**: Local memory provider activated by default (no external API required)

### Changed
- **Merged `hermes-studio` configuration into `hermes-studio-full`**: Consolidated single source of truth
- **Docker Compose now exposes all required ports**: 6060 (UI), 8651 (preview), 56121 (XAI OAuth)
- **Environment variables documented in `.env.example` pattern** within docker-compose.yml
- **Updated `docs/docker.md`** with complete port mapping and environment variable reference
- **Updated `README.md`** with Docker Compose quick start, port table, and environment variable tables

### Removed
- **Legacy `hermes-studio` directory**: Archived as `hermes-studio.legacy.2026-07-30`

### Security
- Auth token and JWT secret now configurable via environment variables
- Default bootstrap credentials documented with recommendation to change on first login

## [2026-07-30] - Initial Consolidation

### Added
- Complete Docker-based deployment with local build
- Holographic memory provider (local, no external dependencies)
- Git version control with GitHub remote (EKKOLearnAI/hermes-studio)
- Persistent data volume (`hermes_data`) with config, sessions, skills, memory