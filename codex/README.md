# Codex Home Assistant Add-on

Run [OpenAI Codex](https://developers.openai.com/codex/cli) directly inside the Home Assistant sidebar. This add-on gives Home Assistant a persistent Codex terminal, access to the Home Assistant configuration folder, and optional Home Assistant MCP integration through `hass-mcp`.

## What This Add-on Does

The add-on starts a browser-based terminal in Home Assistant and launches a persistent shell behind it. From that shell you can run `codex`, inspect your configuration, edit YAML, review logs, and work against your Home Assistant project without leaving the Home Assistant UI.

When MCP is enabled, the add-on also registers a `homeassistant` MCP server for Codex. That lets Codex inspect entity state, call services, and reason about your installation with direct Home Assistant context.

## How It Works

1. Home Assistant opens the add-on through ingress.
2. `ttyd` serves the Home Assistant ingress terminal on port `7681`.
3. The shell starts in the configured working directory, usually `/homeassistant`.
4. Codex state is stored under `/data/codex-home/users/anonymous/.codex` in the current direct-ttyd ingress mode.
5. If session persistence is enabled, the runtime reconnects to the existing tmux session.
6. If MCP is enabled, Codex receives a managed `homeassistant` server entry that launches `hass-mcp` through a privileged helper rather than exposing the Supervisor token in the interactive shell.

## Requirements

- Home Assistant OS or Home Assistant Supervised
- A ChatGPT account with Codex access, or an OpenAI API key for Codex CLI use

## Installation

1. Add `https://github.com/kecksdigital/codex-hass` as a Home Assistant add-on repository.
2. Install the `Codex` add-on.
3. Review the add-on options before starting it.
4. Start the add-on.
5. Open the sidebar panel.

## First Run

1. Open the add-on from the Home Assistant sidebar.
2. Codex starts automatically in the terminal.
3. Complete the Codex sign-in flow if this is the first launch.
4. Trust `/homeassistant` inside Codex if you want project-level `.codex/config.toml` settings in that directory to load.
5. Start working from `/homeassistant`.

The add-on does not ask for OpenAI credentials in Home Assistant add-on options. Authentication stays inside the Codex CLI flow.

## Everyday Usage

Typical tasks:

- Review or edit Home Assistant YAML files
- Investigate broken automations or entities
- Search logs and configuration files
- Refactor scripts stored in `/homeassistant`, `/share`, or `/media`
- Use Home Assistant MCP to inspect state or call services from Codex

Useful commands:

| Command | Purpose |
|---------|---------|
| `codex` | Reopen Codex after exiting to the shell |
| `ha-config` | Jump to `/homeassistant` |
| `ha-logs` | Print `home-assistant.log` if it exists |

## Model Selection

The add-on lets you choose a default model without locking the session to that model:

- Set `default_model` in the add-on config to write a persistent default into the managed Codex config.
- Leave `default_model` blank to use Codex defaults.
- Use `/model` inside Codex any time you want to switch models for the current session.

## Add-on Options

| Option | What it controls | Default |
|--------|------------------|---------|
| `enable_mcp` | Registers the managed `homeassistant` MCP server for Codex | `true` |
| `terminal_font_size` | Terminal font size in the sidebar UI | `14` |
| `terminal_theme` | Sidebar terminal color theme | `dark` |
| `working_directory` | Initial directory for the shell | `/homeassistant` |
| `session_persistence` | Reattach to the existing tmux session | `true` |
| `default_model` | Optional persistent startup model for Codex | blank |

## Files, Persistence, and Overrides

| Path | Purpose |
|------|---------|
| `/homeassistant` | Your Home Assistant configuration directory |
| `/homeassistant/.codex/config.toml` | Optional project-level Codex overrides |
| `/homeassistant/AGENTS.md` | Optional project-level Codex instructions |
| `/data/codex-home/users/anonymous/.codex` | Persistent Codex home used by the direct-ttyd ingress mode |
| `/data/codex-home/legacy-global-home` | Backup of older shared Codex state |
| `/share` | Shared Home Assistant folder |
| `/media` | Media folder |
| `/ssl` | SSL files, mounted read-only |
| `/backup` | Backups, mounted read-only |

The add-on manages user-level Codex defaults in the managed `~/.codex/config.toml`. Project-specific behavior should live in `/homeassistant/.codex/config.toml` and `/homeassistant/AGENTS.md`.

## Home Assistant MCP

When `enable_mcp` is on, the add-on writes a managed `homeassistant` MCP server entry into the managed Codex config. The server is launched through `hass-mcp`, with Home Assistant Supervisor credentials passed only to the helper that starts the MCP server. They are not stored in the add-on options and are not exported into the user's interactive shell.

## Session Persistence

When `session_persistence` is enabled:

- Your browser can disconnect and reconnect without losing the session
- The terminal reconnects to the existing tmux session
- Long-running Codex tasks can keep running in the background

Useful tmux keys:

| Key | Action |
|-----|--------|
| `Ctrl+b d` | Detach and leave the session running |
| `Ctrl+b [` | Enter scroll/copy mode |
| `q` | Exit scroll/copy mode |

To copy text while tmux is active, hold `Ctrl+Shift` while selecting text in the terminal.

## Troubleshooting

### Codex will not start

1. Exit to the shell if needed, then run `codex` manually from the terminal.
2. Follow the sign-in or API key prompts shown by the CLI.
3. Check the add-on logs for startup errors.

### My project-level Codex config is ignored

1. Confirm the file is at `/homeassistant/.codex/config.toml`.
2. Trust the `/homeassistant` project inside Codex.
3. Restart Codex after changing project-level configuration if needed.

### MCP is missing or not working

1. Make sure `enable_mcp` is `true`.
2. Restart the add-on after changing the option.
3. Check add-on logs for `hass-mcp` startup failures.

### My session does not come back

1. Confirm `session_persistence` is `true`.
2. Reopen the add-on as the same Home Assistant user.
3. Restart the add-on completely if tmux state looks stale.

## Support

- [Repository](https://github.com/kecksdigital/codex-hass)
- [Issues](https://github.com/kecksdigital/codex-hass/issues)
- [Home Assistant Community](https://community.home-assistant.io/)
