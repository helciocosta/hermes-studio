# Docker Compose Guide
This repository ships an environment-variable driven Docker Compose setup.
## Quick Start
### Pull pre-built image (Recommended)
```bash
WEBUI_IMAGE=ekkoye8888/hermes-web-ui docker compose up -d
docker compose logs -f hermes-webui
```
Open: `http://localhost:6060`
### Build from source
```bash
docker compose up -d --build
docker compose logs -f hermes-webui
```
## Services
This compose file runs a single service:
- `hermes-webui` — Web UI dashboard with integrated Hermes Agent runtime (pre-built image or built from source)
The Web UI container is built on the `nousresearch/hermes-agent` base image and uses the Hermes CLI / agent bridge runtime.
## Environment Variables
All key runtime settings are configured from compose variables.
| Variable | Default | Description |
|---|---|---|
| `PORT` | `6060` | Web UI listen port |
| `PREVIEW_FRONTEND_PORT` | `8651` | Frontend preview server port (Vite dev server for live reload) |
| `XAI_OAUTH_PORT` | `56121` | XAI OAuth callback port for authentication flow |
| `BIND_HOST` | `0.0.0.0` | Optional Web UI bind host. Defaults to IPv4 for stable WSL/Windows access. Set `::` explicitly for IPv6. |
| `CORS_ORIGINS` | same host only | Comma- or space-separated cross-origin allowlist for HTTP, Socket.IO, and WebSocket. |
| `HERMES_BIN` | `/opt/hermes/.venv/bin/hermes` | Path to Hermes CLI binary |
| `HERMES_AGENT_IMAGE` | `nousresearch/hermes-agent:latest` | Hermes Agent base image (used only during build) |
| `WEBUI_IMAGE` | `hermes-web-ui-local:latest` | Web UI image (set to `ekkoye8888/hermes-web-ui` to use pre-built) |
| `HERMES_DATA_DIR` | `./hermes_data` | Hermes runtime data directory |
| `AUTH_TOKEN` | auto-generated | Fixed auth token for Web UI access (overrides auto-generation) |
| `AUTH_JWT_SECRET` | same as AUTH_TOKEN | JWT secret for username/password authentication |
| `PROFILE` | `default` | Default Hermes profile on startup |
| `LOG_LEVEL` | `info` | Log level (debug, info, warn, error) |
| `HERMES_WEB_UI_DISABLE_GATEWAY_AUTOSTART` | `0` | Set to `1` to disable managed gateway auto-start |
| `HERMES_WEB_UI_DISABLE_MCP_AUTOINJECT` | `0` | Set to `1` to disable MCP auto-injection |
| `HERMES_WEB_UI_DISABLE_SKILL_INJECTION` | `0` | Set to `1` to disable bundled skill injection |
| `OPENROUTER_API_KEY` | — | OpenRouter API key for 200+ models (many free models available) |
| `OPENCODE_ZEN_API_KEY` | — | OpenCode Zen API key for curated models (includes free models like `big-pickle`) |
Override variables directly from shell:
```bash
PORT=16060 docker compose up -d
```
Or create a `.env` file in the project root:
```
WEBUI_IMAGE=ekkoye8888/hermes-web-ui
PORT=6060
AUTH_TOKEN=seu-token-seguro-aqui
AUTH_JWT_SECRET=seu-jwt-secret-aqui

# LLM Providers
OPENROUTER_API_KEY=sk-or-v1-YOUR_KEY_HERE
OPENCODE_ZEN_API_KEY=sk-YOUR_KEY_HERE
```
## Data Persistence
| Path | Description |
|---|---|
| `${HERMES_DATA_DIR}` (`./hermes_data`) | Hermes runtime data (sessions, config, profiles) |
| `${HERMES_DATA_DIR}/hermes-web-ui` | Web UI data (auth token, etc.) |
- Hermes data persists in `./hermes_data`, mapped to `/home/agent/.hermes` in the container.
- Web UI data persists in `./hermes_data/hermes-web-ui/`, mapped to `/home/agent/.hermes-web-ui` in the container.
- The auth token is auto-generated on first run and printed to container logs.
- Deleting the token file and restarting will generate a new one.
## Port Mapping
| Port | Description |
|---|---|
| `${PORT}` (6060) | Web UI dashboard |
| `${PREVIEW_FRONTEND_PORT}` (8651) | Frontend preview server (Vite dev) |
| `${XAI_OAUTH_PORT}` (56121) | XAI OAuth callback endpoint |
No Hermes gateway ports are exposed by this compose setup.
## Code Runtime Behavior
- Hermes CLI binary comes from `HERMES_BIN` env (`packages/server/src/services/hermes-cli.ts`).
- If `HERMES_BIN` is not provided, code falls back to `hermes` in `PATH`.
- Profile-specific chat runs are handled through the Hermes agent bridge. The selected/requested profile is authorized …
- Docker is a managed gateway runtime: Web UI checks profile gateways on startup, but it does not run a periodic gateway watcher.
## Common Operations
Recreate:
```bash
docker compose up -d --force-recreate
```
View auth token:
```bash
docker compose logs hermes-webui | grep token
# or
cat ./hermes_data/hermes-web-ui/.token
```
Stop:
```bash
docker compose down
```