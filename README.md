# Deployxa Agent Plugin for OpenAI Codex (`deployxa_codex`)

An agentic integration enabling **OpenAI Codex** (CLI and IDE harness) to autonomously trigger deployments, stream build and runtime logs, run platform diagnostics on failures, and manage cloud infrastructure on the **Deployxa** platform.

---

## Architecture & MCP Integration

Like Claude Code and ZCode, OpenAI Codex leverages the **Model Context Protocol (MCP)**:

* **Protocol Layer**: Exposes Deployxa tools (`deployxa_create_deployment`, `deployxa_get_deployment_logs`, `deployxa_diagnose_deployment`, etc.) via stdio or streamable HTTP.
* **Context & Instructions**: Directs Codex through [`AGENTS.md`](./AGENTS.md) and domain skills in [`skills/`](./skills).
* **Safe Mutations**: Write operations (such as creating deployments and updating config) execute directly, while destructive calls (project deletion, domain deletion) require explicit confirmation.

---

## Directory Structure

```text
├── plugin.json               # Codex Agent Plugin manifest
├── AGENTS.md                 # OpenAI Codex agent instruction directives
├── .mcp.json                 # MCP server definitions
├── codex-config.toml         # Configuration template for ~/.codex/config.toml
├── package.json              # Plugin package metadata
├── README.md                 # Setup & usage guide
└── skills/                   # Domain skill instructions
    ├── deployxa-deployment/
    │   └── SKILL.md          # Autonomous build & deployment orchestration
    ├── deployxa-diagnose/
    │   └── SKILL.md          # Failure diagnostics & rollbacks
    └── deployxa-environment/
        └── SKILL.md          # Environment & domain management
```

---

## Installation & Setup

### 1. Set Environment Variables
```bash
export DEPLOYXA_TOKEN="dxa_your_api_token"
# Optional: only needed if overriding your default organization
# export DEPLOYXA_ORGANIZATION_ID="org_your_org_id"
```

### 2. Configure Codex CLI

Add the Deployxa MCP server to Codex via command-line:

```bash
# Connect using the hosted Streamable HTTP endpoint (OAuth 2.1 supported):
codex mcp add deployxa --url https://mcp.deployxa.com/mcp
```

Or append the contents of [`codex-config.toml`](./codex-config.toml) to your `~/.codex/config.toml`:

```toml
[mcp.servers.deployxa]
url = "https://mcp.deployxa.com/mcp"
```

### 3. Add `AGENTS.md` to Your Workspace

Copy or include [`AGENTS.md`](./AGENTS.md) in your project root so that the Codex agent harness automatically picks up Deployxa instructions:

```bash
curl -o AGENTS.md https://raw.githubusercontent.com/deployxa/codex-plugin/main/AGENTS.md
```

---

## Example Usage with Codex

Prompt the Codex agent directly:
* *"Deploy this project to Deployxa on the current branch and verify the build status"*
* *"Our last deployment failed. Run diagnostics with Deployxa and tell me what broke"*
* *"Add domain staging.example.com to my Deployxa project and show required DNS records"*
* *"Roll back to deployment d_abc123"*
