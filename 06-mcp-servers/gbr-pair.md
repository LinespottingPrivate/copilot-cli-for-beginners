<!--
---
id: CopilotCLI-06-GBR-Pair
title: !translate Pair a phone with Build Remote Agent
description: !translate Optional: attach GitHub Copilot CLI to gbr-mcp / 127.0.0.1:8788 so a phone can spectate the desktop session. Protocol gbr/1.
audience: Developers / Students / Terminal users
slug: pair-a-phone-with-build-remote-agent
weight: 62
---
-->

# Pair a phone with Build Remote Agent

> ⚠️ **This content is optional.** Copilot CLI does not require a phone. Use this only if you want a **spectator** on iOS/Android while Copilot keeps orchestrating on the desktop.
>
> **[← Back to Chapter 06: MCP Servers](README.md)**

---

**Build Remote Agent** is an independent product (Linespotting AB). Not affiliated with xAI, SpaceX, or GitHub. Protocol stays `gbr/1` — this page does not add a second pair flow.

Phone is spectator + veto, not the orchestrator. Attach surface is only:

| How | Where |
|-----|--------|
| Bot API | `http://127.0.0.1:8788` after `gbr-agent run` |
| MCP stdio | `gbr-mcp` (same JSON as the Bot API) |

Do not put mailbox keys, `X-GBR-Key`, or `device.json` in git or in `mcp-config.json`.

## Install the agent and pair

On the **same machine** as Copilot CLI (need **v0.6.0+**):

```bash
# macOS / Linux
curl -fsSL https://grokbuildremote.com/install.sh | bash
gbr-agent version
gbr-agent pair          # browser QR and printed 8-char code
gbr-agent run           # leave running
```

```powershell
# Windows
irm https://grokbuildremote.com/install.ps1 | iex
gbr-agent version
gbr-agent pair
gbr-agent run
```

Phone: open [Build Remote Agent](https://grokbuildremote.com/) → scan the QR **or** type the 8-char code. Unpair in the app before moving to another PC. Force-close is not enough.

## Attach Copilot CLI (MCP)

Clone the MIT MCP helper once, then point Copilot at it:

```bash
git clone https://github.com/LinespottingOrg/GrokBuildRemote-Agents.git
cd GrokBuildRemote-Agents/mcp/gbr-mcp && npm install
node bin/gbr-mcp.js --diagnose
```

Add a `gbr` server to `~/.copilot/mcp-config.json` (or copy [samples/mcp-configs/gbr-mcp-config.json](../samples/mcp-configs/gbr-mcp-config.json) and fix the path):

```json
{
  "mcpServers": {
    "gbr": {
      "type": "local",
      "command": "node",
      "args": [
        "/ABS/PATH/GrokBuildRemote-Agents/mcp/gbr-mcp/bin/gbr-mcp.js"
      ],
      "tools": ["*"]
    }
  }
}
```

Restart Copilot CLI, then:

```text
/mcp show
```

You should see `gbr`. Copilot stays in the terminal; the phone only spectates (and can inject/veto through the Bot API).

## Attach without MCP (Bot API)

After `gbr-agent run`:

```bash
curl -sS http://127.0.0.1:8788/health
curl -sS http://127.0.0.1:8788/v1/sessions
```

Loop: diagnose → open/attach → lock → inject → wait idle → harvest excerpt → iterate or close.

Docs: https://github.com/LinespottingOrg/GrokBuildRemote-Agents/blob/main/docs/BOT-API.md

---

**[← Back to Chapter 06](README.md)** | **[Custom MCP server (optional) →](mcp-custom-server.md)**
