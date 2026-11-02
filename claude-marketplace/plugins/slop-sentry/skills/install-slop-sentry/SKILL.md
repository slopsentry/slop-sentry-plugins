---
name: install-slop-sentry
description: Connect the Slop Sentry MCP server to the AI apps on this machine. Use when someone asks to install, connect, add, or set up Slop Sentry in Claude, Claude Code, Cowork, ChatGPT, Codex, Cursor, Gemini, Antigravity, VS Code, or Suits.ai.
---

# Install Slop Sentry

Connect `https://mcp.slopsentry.ai` (the site root, never a `/mcp` suffix). Interactive apps sign in with the browser. Do not invent a credential, and do not write a credential into a plugin package or into `dist/plugins`.

An account key is only for the CLI, CI, or an app that still has no browser sign-in. Create that key on https://slopsentry.ai/account. Codex stores a pasted key under `http_headers`, not `headers`.

## Before you edit config

1. Ask the user to sign up or sign in at https://slopsentry.ai if they have no account.
2. Detect which apps are on this machine. Check both the binary and the config directory. On macOS also look for the desktop apps under `/Applications`.

| App | What to look for |
| --- | --- |
| Claude Code | `claude` on `PATH` |
| Claude Desktop | `~/Library/Application Support/Claude` or `%APPDATA%\Claude` |
| Claude Cowork | Claude desktop app, Cowork enabled |
| ChatGPT desktop | `ChatGPT` app, or `~/.codex/config.toml` |
| Codex | `codex` on `PATH`, or `~/.codex` |
| Cursor | `cursor` on `PATH`, `~/.cursor`, or the Cursor app |
| Gemini CLI | `gemini` on `PATH`, or `~/.gemini` |
| Antigravity | `agy` on `PATH`, Antigravity app, or `~/.gemini/antigravity` |
| VS Code | `code` on `PATH` |
| Suits.ai | No local binary. Give the user the form fields below. |

3. For each app you find, connect it, then verify. Leave apps that are not installed off the change list. When you merge a JSON config, keep every server that is already there and add or replace only the `slop-sentry` entry.

## Connect

### Claude Code

```bash
claude mcp add --scope user --transport http slop-sentry https://mcp.slopsentry.ai
claude mcp list
```

`--transport http` is required. Without it, Claude Code treats the URL as a local command.

If a built Claude marketplace exists at `dist/plugins/claude-marketplace` in a checkout of this repo, you can also install the plugin:

```bash
claude plugin marketplace add dist/plugins/claude-marketplace
claude plugin install slop-sentry@slop-sentry --scope user
```

### Codex and the ChatGPT desktop app

They share `~/.codex/config.toml` (Windows: `%USERPROFILE%\.codex\config.toml`).

```bash
codex mcp add slop-sentry --url https://mcp.slopsentry.ai
```

Complete the browser sign-in. If Codex asks for a registration method, run:

```bash
codex mcp login slop-sentry --oauth-client-registration dcr
```

In the ChatGPT desktop app, add a server under Plugins, Settings, Plugins, MCPs. Choose Add Server, then Streamable HTTP. Name `slop-sentry`. URL `https://mcp.slopsentry.ai`. Do not leave the transport on STDIO.

To load the local package before a public listing exists, from a checkout of this repo:

```bash
python3 scripts/build_plugins.py
codex plugin marketplace add dist/plugins/codex-marketplace
```

Restart the ChatGPT desktop app and install Slop Sentry from that marketplace.

### Cursor

Prefer the install link printed in `dist/plugins/snippets/cursor-install-url.txt` after `python3 scripts/build_plugins.py`. Otherwise merge this into `~/.cursor/mcp.json` (project scope: `.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "slop-sentry": {
      "url": "https://mcp.slopsentry.ai"
    }
  }
}
```

Cursor should open the browser for sign-in. The plugin folder for the Cursor marketplace is `dist/plugins/cursor/slop-sentry`.

### Claude Desktop

If the desktop app offers a remote connector, add `https://mcp.slopsentry.ai` there and approve the browser prompt.

Otherwise merge this into the desktop config. macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`. Windows: `%APPDATA%\Claude\claude_desktop_config.json`.

```json
{
  "mcpServers": {
    "slop-sentry": {
      "type": "http",
      "url": "https://mcp.slopsentry.ai"
    }
  }
}
```

Quit and reopen Claude Desktop.

### Claude Cowork

If the user can install plugins, install `dist/plugins/slop-sentry-claude.zip` or the directory listing after it is approved. Workspace admins who cannot install a plugin use Workspace Settings, Connectors, Add Custom MCP, and paste `https://mcp.slopsentry.ai`.

### Gemini CLI

Merge this into `~/.gemini/settings.json`, then restart the CLI:

```json
{
  "mcpServers": {
    "slop-sentry": {
      "url": "https://mcp.slopsentry.ai"
    }
  }
}
```

### Antigravity

If a built Antigravity package exists at `dist/plugins/antigravity/slop-sentry` in a checkout of this repo:

```bash
agy plugin validate --strict dist/plugins/antigravity/slop-sentry
agy plugin install dist/plugins/antigravity/slop-sentry
agy plugin list
```

Otherwise install from the public plugins repo once published, or merge the same remote URL into the Antigravity / Gemini MCP settings:

```json
{
  "mcpServers": {
    "slop-sentry": {
      "url": "https://mcp.slopsentry.ai"
    }
  }
}
```

Complete browser sign-in when the MCP server connects. In the Antigravity App, plugins also appear under Customizations → Installed / Marketplace.

### VS Code

In the command palette, run MCP: Open User Configuration. Or write a workspace `.vscode/mcp.json`. Use the `servers` key:

```json
{
  "servers": {
    "slop-sentry": {
      "type": "http",
      "url": "https://mcp.slopsentry.ai"
    }
  }
}
```

### Suits.ai

In Settings, Integrations, create an MCP connection:

- Title: `Slop Sentry`
- MCP endpoint URL: `https://mcp.slopsentry.ai`
- Prefer browser sign-in when Suits offers it
- Authorize

## Verify

After the browser approval, call `slop_sentry_whoami` from the app you just connected. A 401 means sign-in did not finish. Send the user back to the browser prompt. Do not create a key to get past a 401.

If you configured a different app than the one you are running in, ask the user to open that app and request `slop_sentry_whoami`.

## Report

Reply with one line per app you checked:

- connected
- needs a browser approval
- not installed on this machine

Name the config file or command you changed. Do not claim an app is connected until `slop_sentry_whoami` succeeds or the user confirms the browser approval.
