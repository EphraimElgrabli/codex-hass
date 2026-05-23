# Changelog

All notable changes to this project will be documented in this file.

## [0.2.1] - 2026-05-24

### Changed
- Rewrote the Codex documentation to explain installation, per-user sessions, MCP wiring, persistence, and everyday usage more clearly
- Replaced upstream repository links and metadata with the `kecksdigital/codex-hass` repository
- Updated the add-on branding, description, and panel icon so the Codex add-on no longer ships with Claude-oriented presentation

## [0.2.0] - 2026-05-23

### Added
- Per-user Codex homes under `/data/codex-home/users/<ha-user-id>/.codex`
- Dedicated Unix users for each Home Assistant ingress user before the shell starts
- Per-user tmux session isolation keyed by the Home Assistant ingress user id
- Reverse-proxied ingress terminal with nginx in front of ttyd and ttyd bound to a local Unix socket
- `hass-mcp-wrapper` and a privileged root helper so the Supervisor token stays out of the interactive shell
- Non-destructive merging of add-on-managed Codex defaults into each user's `~/.codex/config.toml`
- One-time backup of legacy shared Codex state from `/homeassistant/.codex-home` without auto-assigning it to a user

### Changed
- Moved persistent Codex state out of `/homeassistant` and into `/data/codex-home`
- Removed the shared global ttyd/tmux session model in favor of per-user sessions
- Added startup validation for the generated per-user Codex MCP configuration
- Removed inherited `docker_api` and `full_access` privileges from the Codex add-on

## [0.1.0] - 2026-05-23

### Added
- Initial Codex add-on release alongside the existing Claude Code add-on
- OpenAI Codex CLI installed during image build with `codex --version` validation
- Home Assistant ingress terminal powered by ttyd on port 7681
- Persistent Codex home directory at `/homeassistant/.codex-home`
- Generated `~/.codex/AGENTS.md` with Home Assistant path mapping, log guidance, and MCP notes
- Generated `~/.codex/config.toml` with optional default model selection and Home Assistant MCP wiring
- Session persistence via tmux with the `codex` session name
- Initial architecture support for amd64 and aarch64

### Changed
- Removed Playwright MCP and Claude-specific runtime behavior from the new Codex add-on
- Removed inherited Modbus/serial tooling and UART access from the Codex v1 scope
