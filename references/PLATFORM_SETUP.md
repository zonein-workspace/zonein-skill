# Platform Setup Guide

ZoneIn supports multiple AI agent platforms. Choose the setup method for your platform.

## Prerequisites (All Platforms)

1. **Python 3** installed
2. **ZoneIn API Key** — Go to [app.zonein.xyz](https://app.zonein.xyz) → Log in → Click **"Get API Key"** (starts with `zn_`)

---

## Claude.ai (Web) — Remote MCP Connector

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

> **How it works:** Claude uses Dynamic Client Registration (OAuth 2.1) to automatically register itself. When you first use a ZoneIn tool, you'll be asked to enter your API key once. After that, Claude uses bearer tokens automatically.

---

## Claude Desktop — MCP Server (stdio)

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

3. Replace `/absolute/path/to/zonein-mcp-server/` with the actual path to the [zonein-mcp-server](https://github.com/zonein-workspace/zonein-mcp-server) project.

4. **Restart Claude Desktop.** ZoneIn tools will appear automatically in the tools menu (🔧).

> **Note:** This requires the `zonein-mcp-server` project with MongoDB access. For most users, the **Claude.ai web** method is simpler.

---

## Claude Code — Agent Skills

Claude Code auto-discovers skills from `.claude/skills/` directories.

**Steps:**

1. Copy the skill folder into your project:
   ```bash
   cp -r zonein-skill .claude/skills/hyperliquid-trading-agent
   ```
   Or for global access (all projects):
   ```bash
   cp -r zonein-skill ~/.claude/skills/hyperliquid-trading-agent
   ```

2. Set your API key:
   ```bash
   export ZONEIN_API_KEY="zn_your_key_here"
   ```

3. Start Claude Code — the skill loads automatically. Ask about trading signals, create agents, etc.

---

## Cursor

### Option A: Agent Skills (recommended)

1. Copy the skill folder:
   ```bash
   cp -r zonein-skill .cursor/skills/hyperliquid-trading-agent
   ```

2. Set `ZONEIN_API_KEY` in your terminal/environment.

3. Cursor will auto-discover the skill.

### Option B: MCP Server

1. Open Cursor → **Settings** → **MCP** → **Add Server**
2. Fill in:

   | Field | Value |
   |:---|:---|
   | **Name** | `zonein` |
   | **Command** | `python3` |
   | **Args** | `/path/to/zonein-mcp-server/mcp_server.py` |
   | **Env** | `MONGO_URI=your_mongodb_uri` |

3. Save and restart.

---

## Windsurf

### Option A: Agent Skills

1. Copy the skill folder:
   ```bash
   cp -r zonein-skill /your/workspace/skills/hyperliquid-trading-agent
   ```

2. Set `ZONEIN_API_KEY` in your environment.

### Option B: MCP Server

1. Open Windsurf → **Settings** → **MCP**
2. Add server:

   | Field | Value |
   |:---|:---|
   | **Command** | `python3 /path/to/zonein-mcp-server/mcp_server.py` |
   | **Env** | `MONGO_URI=your_mongodb_uri` |

---

## Manus — Import from GitHub

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
6. Use it: type `/` in chat → select **hyperliquid-trading-agent**, or just ask about trading naturally.

---

## GPT Custom Actions (OpenAI) — OpenAPI

GPT Custom Actions connect to external APIs via OpenAPI specs.

**Steps:**

1. Go to [chat.openai.com](https://chat.openai.com) → **Explore GPTs** → **Create a GPT** (or edit existing)
2. Click the **Configure** tab → scroll down to **Actions** → **Create new action**
3. Click **Import from URL** and paste:
   ```
   https://mcp.zonein.xyz/docs/openapi.json
   ```
   Or click **Import Schema** and paste the contents of `openapi.yaml` from this skill.

4. Set **Authentication:**

   | Field | Value |
   |:---|:---|
   | **Authentication type** | API Key |
   | **API Key** | `zn_your_key_here` |
   | **Auth Type** | Custom |
   | **Custom Header Name** | `X-API-Key` |

5. Click **Save** → **Test** any action to verify.

6. In your GPT instructions, add:
   ```
   You have access to ZoneIn Trading Intelligence API. Use it when users ask about crypto trading, whale activity, market analysis, or trading signals.
   ```

> **Available actions:** All read-only endpoints (signals, leaderboard, dashboard, TA, derivatives). Financial endpoints (open/close positions) are NOT included for safety.

---

## OpenClaw

1. Open **Gateway Dashboard** → navigate to `/skills`
2. Enable **hyperliquid-trading-agent**
3. Paste your API key (`zn_...`)

Alternative methods:
```bash
# Via environment variable
export ZONEIN_API_KEY="zn_your_key_here"

# Via config file
# ~/.openclaw/openclaw.json → skills.entries.zonein.apiKey
```

---

## Gemini / Antigravity

Same as Claude Code — Agent Skills:

1. Place the skill folder in your workspace skills directory
2. Set `ZONEIN_API_KEY` environment variable
3. The agent will auto-discover the skill from `SKILL.md`

---

## Generic Setup (Any Platform)

If your platform supports running Python scripts:

```bash
# 1. Set API key
export ZONEIN_API_KEY="zn_your_key_here"

# 2. Verify connection
python3 scripts/zonein.py status

# 3. Get trading signals
python3 scripts/zonein.py perp-signals --limit 5

# 4. View AI dashboard
python3 scripts/zonein.py dashboard
```

All commands: see [Commands Reference](COMMANDS.md) or run `python3 scripts/zonein.py --help`.
