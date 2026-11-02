# Slop Sentry Plugins

Public repository hosting the Claude plugin packages, marketplace catalog, and distribution artifacts for [Slop Sentry](https://slopsentry.ai).

Slop Sentry checks writing drafts for thin claims, try-hard or hokey language, and AI-generated writing tells, then delivers polished, human-sounding revisions. This repository provides the public packaging and marketplace manifests required for Claude Code, Claude Cowork, and MCP-compatible environments without bundling proprietary backend engine sources.

## Repository Structure

```
.
├── claude-marketplace/
│   ├── .claude-plugin/
│   │   └── marketplace.json               # Marketplace manifest registering slop-sentry
│   └── plugins/
│       └── slop-sentry/
│           ├── .claude-plugin/
│           │   ├── icon.png               # 512x512 plugin listing icon
│           │   └── plugin.json            # Plugin manifest and metadata
│           ├── .mcp.json                  # Remote MCP server configuration (https://mcp.slopsentry.ai)
│           └── skills/
│               ├── slop-sentry/
│               │   └── SKILL.md           # Instructions for invoking Slop Sentry editing and audit tools
│               └── install-slop-sentry/
│                   └── SKILL.md           # Instructions for configuring Slop Sentry across AI clients
├── dist/
│   ├── slop-sentry-claude.zip             # Claude / Cowork plugin archive
│   ├── slop-sentry-codex.zip              # Codex / ChatGPT desktop plugin archive
│   └── slop-sentry-antigravity.zip        # Antigravity plugin archive
└── README.md
```

### What Each Directory Contains

- **`claude-marketplace/`**: The Claude marketplace catalog. It defines the marketplace structure and points to the `slop-sentry` plugin inside `plugins/slop-sentry`.
- **`claude-marketplace/plugins/slop-sentry/`**: The plugin source directory submitted to the Claude plugin directory. It includes:
  - `.claude-plugin/icon.png`: 512×512 square PNG icon for the Claude plugin directory.
  - `.claude-plugin/plugin.json`: Plugin description, version, privacy policy URL, and author metadata.
  - `.mcp.json`: Configures the remote MCP endpoint (`https://mcp.slopsentry.ai`).
  - `skills/slop-sentry`: The primary workflow skill for drafting, editing, quality checks, substance checks, hokiness checks, and visual diff reviews.
  - `skills/install-slop-sentry`: Setup instructions for connecting the MCP server to supported local and desktop clients.
- **`dist/`**: Pre-built distribution archives for Claude/Cowork (`slop-sentry-claude.zip`), Codex/ChatGPT desktop (`slop-sentry-codex.zip`), and Antigravity (`slop-sentry-antigravity.zip`). Upload the matching archive when a client cannot use the marketplace catalog.

## Installation

### Claude Code

To add the local marketplace and install the plugin in Claude Code:

```bash
# Add the marketplace catalog
claude plugin marketplace add claude-marketplace

# Install the Slop Sentry plugin
claude plugin install slop-sentry@slop-sentry --scope user
```

To validate the plugin directory prior to submission or installation:

```bash
claude plugin validate claude-marketplace/plugins/slop-sentry
```

### Direct MCP Connection (Claude Code)

If you prefer connecting directly to the remote MCP server without installing the plugin package:

```bash
claude mcp add --scope user --transport http slop-sentry https://mcp.slopsentry.ai
```

> **Note:** The `--transport http` flag is required. Without it, Claude Code interprets the URL as a local executable command.

### Claude Cowork

1. **Direct Upload:** Upload `dist/slop-sentry-claude.zip` directly through the Cowork plugin interface.
2. **Directory Listing:** Once published in the public directory, install Slop Sentry from the directory catalog.
3. **Custom MCP Connector:** If plugin installations are restricted by your workspace administrator, configure a custom connector under **Workspace Settings > Connectors > Add Custom MCP** with the URL `https://mcp.slopsentry.ai`.

## Authentication & Privacy

Slop Sentry uses browser-based authentication. When invoking tools for the first time, your client will prompt you to authenticate with your Slop Sentry account at [slopsentry.ai](https://slopsentry.ai). No API keys, credentials, or secrets are stored in this repository or in the plugin manifests.

- **Website:** [https://slopsentry.ai](https://slopsentry.ai)
- **Privacy Policy:** [https://slopsentry.ai/privacy](https://slopsentry.ai/privacy)
- **Terms of Service:** [https://slopsentry.ai/privacy](https://slopsentry.ai/privacy)
- **Remote MCP Endpoint:** `https://mcp.slopsentry.ai`
