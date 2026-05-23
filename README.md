# Kecks Digital Home Assistant Add-ons

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Home Assistant add-ons for AI-assisted configuration work, browser automation, and utility services.

The main add-on in this repository is `Codex`, which runs OpenAI Codex inside the Home Assistant sidebar with a persistent terminal and optional Home Assistant MCP access.
`Auto-Monocle` and `Playwright Browser` are separate optional add-ons. Codex does not depend on them.

## Add-ons

| Add-on | Description |
|--------|-------------|
| [Codex](codex/) | OpenAI Codex in the Home Assistant sidebar with a persistent terminal and optional Home Assistant MCP access |
| [Auto-Monocle](auto-monocle/) | Auto-discover HA cameras and expose to Alexa via Monocle Gateway |
| [Playwright Browser](playwright-browser/) | Headless Chromium with CDP endpoint for browser automation |

## Install This Repository

[![Add Repository](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Fkecksdigital%2Fcodex-hass)

Or manually: **Settings** → **Add-ons** → **Add-on Store** → **⋮** → **Repositories** → Add `https://github.com/kecksdigital/codex-hass`

## Getting Started With Codex

1. Add this repository to Home Assistant.
2. Install the `Codex` add-on.
3. Set the add-on options you want, especially `default_model`, `codex_permissions`, `enable_mcp`, and `session_persistence`.
4. Start the add-on and open the sidebar panel.
5. Complete the Codex sign-in flow when the terminal opens Codex.

The Codex add-on starts in `/homeassistant`, uses `gpt-5.4` as its starter model, mounts the standard Home Assistant folders, stores persistent Codex state under `/data/codex-home/`, and can expose a `homeassistant` MCP server when enabled. Set `codex_permissions` to `full_access` when you want Codex to use `sandbox_mode = "danger-full-access"` inside the add-on container.

## Support

- [Codex add-on guide](codex/README.md)
- [Issues](https://github.com/kecksdigital/codex-hass/issues)

## License

MIT License
