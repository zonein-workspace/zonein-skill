# Platform Setup Guide

ZoneIn supports multiple AI agent platforms. Choose the setup method for your platform.

## Prerequisites (All Platforms)

1. **Python 3** installed
2. **ZoneIn API Key** — Go to [app.zonein.xyz](https://app.zonein.xyz) → Log in → Click **"Get API Key"** (starts with `zn_`)

---

## 🚀 Fast Install (Recommended)

For most modern AI coding environments and agents (such as **Claude Code**, **Windsurf**, **Cursor**, **Copilot**, and generic CLI agents), you can automatically install the ZoneIn skill with a single command:

```bash
npx skills add https://github.com/zonein-workspace/zonein-skill --skill zonein
```

After installation, simply set your API key in your environment and launch your AI assistant:

```bash
export ZONEIN_API_KEY="zn_your_key_here"
```

*Your agent will auto-discover the skill and is instantly ready to fetch market data and manage your ZoneIn trades.*

---

## Manual Setup for Specialized Platforms

For platforms requiring specific cloud integrations, UI setups, or dedicated MCP servers not supported by the 1-click script above, follow the specialized instructions below.

### Claude.ai (Web) — Remote MCP Connector

> **Requires:** Claude Pro, Max, Team, or Enterprise plan.

Claude.ai web connects to remote MCP servers via Custom Connectors with OAuth 2.1.

**Steps:**

1. Open [claude.ai](https://claude.ai) → click your avatar (bottom-left) → **Settings**
2. Go to **Connectors** → **Add Custom Connector**
3. Fill in the form:

   | Field | Value |
   |:---|:---|
   | **Name** | `ZoneIn Trading` |
   | **Remote MCP server URL** | `https://mcp.zonein.xyz/mcp` |
   | **OAuth Client ID** | _(leave empty — auto-registered)_ |
   | **OAuth Client Secret** | _(leave empty — auto-registered)_ |

4. Click **Add**
5. Claude will redirect you to ZoneIn's authorization page
6. Enter your **ZoneIn API Key** (`zn_...`) and click **Authorize**
7. Done! ZoneIn tools will appear in your chats automatically.

> **How it works:** Claude uses Dynamic Client Registration (OAuth 2.1) to automatically register itself. When you first use a ZoneIn tool, you'll be asked to authorize. After that, Claude uses token authentication autonomously.

---

### Claude Desktop — MCP Server (stdio)

Claude Desktop runs local MCP servers via stdio protocol.

**Steps:**

1. Open Claude Desktop config file:
   - **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
   - **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

2. Add the following (create the file if it doesn't exist):
   ```json
   {
     "mcpServers": {
       "zonein": {
         "command": "python3",
         "args": ["/absolute/path/to/zonein-mcp-server/mcp_server.py"],
         "env": {
           "MONGO_URI": "your_mongodb_connection_string"
         }
       }
     }
   }
   ```

3. Replace `/absolute/path/to/zonein-mcp-server/` with the actual path to the cloned `zonein-mcp-server` repository.
4. **Restart Claude Desktop.** ZoneIn tools will appear automatically in the tools menu.

---

### Manus — Import from GitHub

Manus supports importing skills directly from GitHub.

**Steps:**

1. In Manus interface → **Skills** tab (left sidebar) → **+ Add**
2. Select **Import from GitHub**
3. Paste the repository URL:
   ```
   https://github.com/zonein-workspace/zonein-skill
   ```
4. Manus will clone the repo, detect `SKILL.md`, and register the skill.
5. Set API key when prompted, or via Manus environment variables:
   ```
   ZONEIN_API_KEY=zn_your_key_here
   ```

---

### GPT Custom Actions (OpenAI) — OpenAPI

GPT Custom Actions connect to external APIs via OpenAPI specs.

**Steps:**

1. Go to [chat.openai.com](https://chat.openai.com) → **Explore GPTs** → **Create a GPT**
2. Click the **Configure** tab → scroll down to **Actions** → **Create new action**
3. Click **Import from URL** and paste:
   ```
   https://mcp.zonein.xyz/docs/openapi.json
   ```
4. Set **Authentication:**

   | Field | Value |
   |:---|:---|
   | **Authentication type** | API Key |
   | **API Key** | `zn_your_key_here` |
   | **Auth Type** | Custom |
   | **Custom Header Name** | `X-API-Key` |

5. Click **Save** → **Test** any action to verify.
6. In your GPT instructions, add: `You have access to ZoneIn Trading Intelligence API. Use it when users ask about crypto trading, whale activity, market analysis, or trading signals.`

---

### OpenClaw

1. Open **Gateway Dashboard** → navigate to `/skills`
2. Enable **hyperliquid-trading-agent**
3. Paste your API key (`zn_...`)

*Alternative methods:*
```bash
# Via environment variable
export ZONEIN_API_KEY="zn_your_key_here"

# Via config file
# ~/.openclaw/openclaw.json → skills.entries.zonein.apiKey
```

---

### Gemini / Antigravity

1. Run the `npx skills add` script at the root of your project workspace.
2. Set the `ZONEIN_API_KEY` environment variable.
3. The agent will auto-discover the skill from `SKILL.md`.

---

## Manual / Python Setup

If you prefer to run the scripts manually without an agent:

```bash
export ZONEIN_API_KEY="zn_your_key_here"

# Verify connection
python3 scripts/zonein.py status

# Get trading signals
python3 scripts/zonein.py perp-signals --limit 5
```

All commands: see [Commands Reference](COMMANDS.md).
