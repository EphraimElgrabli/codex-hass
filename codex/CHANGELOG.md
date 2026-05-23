# Changelog

All notable changes to this project will be documented in this file.

## [0.2.6] - 2026-05-24

### Fixed
- Restored direct ttyd ingress serving on port 7681 so the WebSocket console works behind Home Assistant ingress
- Removed the nginx proxy and ttyd auth-header mode that caused `/ws` 502 responses and ttyd restart prompts
- Updated the healthcheck to probe ttyd directly after removing nginx from the runtime path
- Updated documentation to describe the current shared direct-ttyd ingress identity accurately

### Changed
- Start Codex automatically when the Web UI opens so unauthenticated users are taken directly into the Codex sign-in flow
- Retry Codex by default when startup exits before completion, with an explicit diagnostic shell escape

## [0.2.5] - 2026-05-24

### Fixed
- Fixed the per-user tmux launcher so ttyd opens an interactive shell instead of falling back to its restart prompt
- Added visible session startup errors in the browser terminal when the shell wrapper fails before bash starts

## [0.2.4] - 2026-05-24

### Changed
- Reduced nginx health-check log noise so Web UI proxy errors are easier to see in add-on logs

## [0.2.3] - 2026-05-24

### Fixed
- Switched the nginx upstream from a Unix socket to `127.0.0.1:7682` so ttyd no longer depends on socket permission compatibility
- Expanded the ingress allowlist from a single hard-coded Supervisor IP to private network ranges used by Home Assistant installations

## [0.2.2] - 2026-05-24

### Fixed
- Allowed AppArmor execution of `/usr/sbin/nginx` so the hardened ingress proxy can start correctly
- Moved Codex startup validation out of `/tmp` to avoid Codex helper-binary warnings during MCP config checks

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
