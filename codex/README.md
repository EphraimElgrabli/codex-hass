# Codex for Home Assistant

Run [OpenAI Codex](https://developers.openai.com/codex/cli), OpenAI's coding agent, directly in your Home Assistant sidebar with access to your Home Assistant configuration and optional Home Assistant MCP integration.

## Requirements

- Home Assistant OS or Supervised installation
- A ChatGPT account with Codex access or an OpenAI API key

## Features

- **Web Terminal**: Access Codex through a browser-based terminal
- **Config Access**: Read and write Home Assistant configuration files
- **Home Assistant MCP**: Optional direct access to entities and services via `hass-mcp`
- **Per-User Codex Homes**: Each Home Assistant user gets a separate Codex home under `/data/codex-home/users/<ha-user-id>/.codex`
- **Per-User Runtime Accounts**: Each ingress user is mapped to a dedicated Unix user before the shell starts
- **Per-User Session Persistence**: Optional tmux integration preserves sessions per Home Assistant user instead of sharing one global shell
- **Hardened Ingress**: nginx only accepts ingress traffic from Home Assistant and proxies ttyd over a local Unix socket
- **Customizable Theme**: Choose between dark and light terminal themes
- **Default Model Setting**: Set a persistent default model while still allowing `/model` changes in-session
- **Initial Architecture Scope**: Supports amd64 and aarch64 in this first release

## Setup

### 1. Install the Add-on

1. Add the repository to Home Assistant
2. Install the "Codex" add-on
3. Start the add-on
4. Open the Web UI from the sidebar

### 2. Start Codex

On first launch:

1. Open the terminal from the Home Assistant sidebar
2. Type `codex`
3. Follow the Codex sign-in flow in the terminal
4. After authentication, start working from `/homeassistant`

The add-on does not store OpenAI credentials in Home Assistant add-on options. Authentication is handled by Codex itself.

## Usage

### Typical Flow

```bash
codex
```

Then ask Codex to help with tasks such as:

- Listing or reviewing automations
- Editing Home Assistant YAML files
- Debugging configuration issues
- Inspecting logs and unavailable entities
- Managing smart-home behavior through Home Assistant MCP

### Model Selection

- Set `default_model` in the add-on configuration to persist a default Codex model
- Leave `default_model` blank to use Codex's built-in default
- Use `/model` inside Codex to switch models during the current session

### Shell Helpers

| Command | Purpose |
|---------|---------|
| `codex` | Start Codex |
| `ha-config` | Change to the Home Assistant config directory |
| `ha-logs` | Read the Home Assistant log file if present |

## Configuration Options

| Option | Description | Default |
|--------|-------------|---------|
| `enable_mcp` | Enable the Home Assistant MCP server | true |
| `terminal_font_size` | Font size (10-24) | 14 |
| `terminal_theme` | dark or light | dark |
| `working_directory` | Start directory | /homeassistant |
| `session_persistence` | Use tmux for persistent sessions | true |
| `default_model` | Optional persistent default Codex model | blank |

## File Locations

| Path | Description | Access |
|------|-------------|--------|
| `/homeassistant` | Home Assistant configuration directory | read-write |
| `/homeassistant/.codex/config.toml` | Optional project-level Codex overrides | read-write |
| `/homeassistant/AGENTS.md` | Optional project-level Codex instructions | read-write |
| `/data/codex-home/users/<ha-user-id>/.codex` | Per-user persistent Codex home directory | read-write |
| `/data/codex-home/legacy-global-home` | Backup of legacy shared Codex state from older add-on versions | read-write |
| `/share` | Shared folder | read-write |
| `/media` | Media folder | read-write |
| `/ssl` | SSL certificates | read-only |
| `/backup` | Backups | read-only |

The add-on merges add-on-managed defaults into each user's `~/.codex/config.toml` without removing unrelated user settings. Put project-specific Codex configuration in `/homeassistant/.codex/config.toml`, and project instructions in `/homeassistant/AGENTS.md` or nested `AGENTS.md` files.

Codex only loads project-level `.codex/config.toml` files for trusted projects. If `/homeassistant` is not trusted yet, Codex will ignore `/homeassistant/.codex/config.toml` until the project is trusted.

## Session Persistence

When `session_persistence` is enabled, the add-on uses tmux to maintain a separate terminal session for each Home Assistant user. This means:

- Your session survives browser refreshes
- You reconnect to your own session instead of a shared global shell
- Long-running Codex work can continue in the background

### tmux Commands

| Key | Action |
|-----|--------|
| `Ctrl+b d` | Detach from the session and keep it running |
| `Ctrl+b [` | Enter scroll/copy mode |
| Mouse wheel | Scroll terminal history |
| `q` | Exit scroll/copy mode |

### Copy and Paste

When tmux is enabled, mouse handling changes:

| Action | How to do it |
|--------|--------------|
| Copy | Hold `Ctrl+Shift` while selecting text |
| Paste | `Shift+Insert`, middle-click, or `Ctrl+Shift+V` |

## Home Assistant MCP

When `enable_mcp` is enabled, the add-on generates a Codex MCP configuration for the `homeassistant` server using `hass-mcp`. The Supervisor token is not stored in Home Assistant add-on options or persisted in `~/.codex/config.toml`; it is kept root-readable only and exposed to Codex through a privileged helper that launches `hass-mcp`.

## Migration Notes

If you used an older Codex add-on build that stored shared state in `/homeassistant/.codex-home`, the new runtime copies that directory once to `/data/codex-home/legacy-global-home` as a backup. The backup is not automatically assigned to any user; new sessions use per-user homes under `/data/codex-home/users/<ha-user-id>/.codex`.

## Troubleshooting

### Codex sign-in issues

1. Run `codex` manually in the terminal
2. Follow the prompts for ChatGPT sign-in or API key authentication
3. Confirm that Codex state persists under your per-user directory in `/data/codex-home/users/`

### Home Assistant MCP not working

1. Verify `enable_mcp` is set to `true`
2. Restart the add-on after configuration changes
3. Check the add-on logs for `hass-mcp` startup errors

### Terminal not loading

1. Check that the add-on is running
2. Refresh the page
3. Review add-on logs for ttyd startup errors

### Session not persisting

1. Ensure `session_persistence` is set to `true`
2. Sessions are keyed by your Home Assistant ingress user id
3. Restart the add-on completely after changing session settings

## Support

- [GitHub Issues](https://github.com/robsonfelix/robsonfelix-hass-addons/issues)
- [Home Assistant Community](https://community.home-assistant.io/)
