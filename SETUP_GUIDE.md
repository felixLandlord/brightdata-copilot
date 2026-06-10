# Setup Guide — Bright Data Custom Agents

This guide walks through enabling and using the Bright Data agents on **GitHub.com (cloud agent)** and **Copilot CLI**.

---

## Before you start

| Requirement | Details |
|-------------|---------|
| Copilot plan | Paid plan required: Pro, Pro+, Business, or Enterprise. See [Copilot plans](https://docs.github.com/en/copilot/get-started/plans). |
| Bright Data token | Create at [brightdata.com/ai/mcp-server](https://brightdata.com/ai/mcp-server). Free tier includes 5,000 MCP requests/month. |
| Admin access | Business/Enterprise: an admin must enable cloud agent and MCP policies first ([TEAM_DEPLOYMENT.md](TEAM_DEPLOYMENT.md)). |
| Repository access | Write access to add agent files and secrets; admin access for MCP repository settings (optional). |

---

## Part 1 — Cloud agent on GitHub.com

Cloud agent runs on GitHub's servers. MCP and secrets are configured centrally—individual users do not set up MCP themselves.

### Step 1: Add the agent files

Ensure these files exist on the **default branch**:

```
.github/agents/brightdata-rapid-agent.agent.md
.github/agents/brightdata-pro-agent.agent.md
```

You can use this repository as-is, or copy only the `.github/agents/` folder into another repository.

**Filename rules** (from GitHub): only `.`, `-`, `_`, `a-z`, `A-Z`, `0-9`.

### Step 2: Enable access (admins)

If your organization uses **Copilot Business** or **Copilot Enterprise**, cloud agent and third-party MCP are **disabled by default**.

**Enterprise owners:**

1. Enterprise → **AI controls** → **Agents** → enable **Copilot Cloud Agent** for the right organizations.
2. **AI controls** → **MCP** → enable **MCP servers in Copilot**.

See [Enabling cloud agent in your enterprise](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/enable-copilot-cloud-agent).

**Organization owners:**

1. Organization **Settings** → **Copilot** → **Policies**.
2. Enable **Copilot cloud agent**.
3. Enable **MCP servers on GitHub.com**.

See [Adding cloud agent to your organization](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent).

### Step 3: Add your Bright Data API token

The agents reference a secret named `COPILOT_MCP_BRIGHTDATA_TOKEN`. Only Agents secrets prefixed with `COPILOT_MCP_` are passed to MCP servers.

**Repository level** (single repo):

1. Repository → **Settings** → **Secrets and variables** → **Agents**.
2. **New repository secret**
3. Name: `COPILOT_MCP_BRIGHTDATA_TOKEN`
4. Value: your Bright Data API token

**Organization level** (shared across repos):

1. Organization → **Settings** → **Secrets and variables** → **Agents**.
2. **New organization secret** with the same name.
3. Choose which repositories can access it.

See [Configure secrets for Copilot cloud agent](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables).

### Step 4: Verify the agent appears

1. Go to [github.com/copilot/agents](https://github.com/copilot/agents).
2. Select your repository from the dropdown.
3. Click the **agent icon** in the prompt box.
4. Confirm **Bright Data Rapid** and/or **Bright Data Pro** are listed.

If agents are missing, refresh the page and confirm files are on the default branch.

### Step 5: Start a session

1. Select the repository and **choose the Bright Data agent** from the dropdown (required—do not rely on the default cloud agent for web tasks).
2. Enter a prompt, for example:
   - *Search the web for the latest React release notes and summarize them.*
   - *Scrape https://example.com/docs and list the API endpoints.*
3. Click **Start task** or press Enter.

**Other ways to start:**

- Assign an issue to **Copilot** and select the Bright Data agent in the dialog.
- Use the **Agents** tab in a repository or `/task` in Copilot Chat.

See [Using Copilot cloud agent on GitHub](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github).

### Step 6: Validate MCP (optional)

1. Assign a small issue to Copilot with the Bright Data agent selected.
2. Open the session log → **Copilot** → **Start MCP Servers**.
3. Confirm Bright Data tools are listed.

---

## Part 2 — Copilot CLI

CLI runs on your local machine. **MCP is configured separately** from GitHub repository settings.

### Install Copilot CLI

Choose one method ([official install guide](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli)):

```bash
# npm (Node.js 22+)
npm install -g @github/copilot

# macOS/Linux
brew install copilot-cli
```

### Authenticate

```bash
copilot
/login
```

Follow the browser prompt. See [Authenticating Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/authenticate-copilot-cli).

### Copilot CLI setup

#### Option A — Interactive (`/mcp add`)

1. Start `copilot` in a directory that contains this repository (or a clone).
2. Run `/mcp add`.
3. Fill in:
   - **Server name:** `brightdata`
   - **Server type:** HTTP
   - **URL:** `https://mcp.brightdata.com/mcp?token=YOUR_API_TOKEN`  
     For Pro tools, add `&pro=1` to the URL.
   - **HTTP headers:** `{"Authorization": "Bearer YOUR_API_TOKEN"}`
   - **Tools:** `*`
4. Press **Ctrl+S** to save.

Config is stored in `~/.copilot/mcp-config.json` by default.

#### Option B — Edit config file

Add to `~/.copilot/mcp-config.json`:

```json
{
  "mcpServers": {
    "brightdata": {
      "type": "http",
      "url": "https://mcp.brightdata.com/mcp?token=YOUR_API_TOKEN",
      "headers": {
        "Authorization": "Bearer YOUR_API_TOKEN"
      },
      "tools": ["*"]
    }
  }
}
```

Use `&pro=1` in the URL for Pro mode. **Do not commit this file**—it contains your token.

Verify with `/mcp show` and `/mcp show brightdata`.

See [Adding MCP servers for Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers).

### Use the Bright Data agent in CLI

The agent profiles in `.github/agents/` are picked up from the repository automatically.

```bash
# Interactive — select from list
/agent

# Command line
copilot --agent=brightdata-rapid-agent --prompt "Search for the latest express npm version"

# In a prompt
Use the brightdata-pro-agent to scrape the pricing page at example.com
```

See [Invoking custom agents](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/invoke-custom-agents).

---

## Cloud agent vs CLI — what differs

| | Cloud agent (GitHub.com) | Copilot CLI |
|--|--------------------------|-------------|
| Where MCP is configured | Agent file + optional repo settings; secrets on GitHub | `~/.copilot/mcp-config.json` on each machine |
| Per-user MCP setup | No (centralized) | Yes (each user) |
| Bright Data token storage | Agents secret `COPILOT_MCP_BRIGHTDATA_TOKEN` | Local config or env (user-managed) |
| Agent selection | Dropdown on github.com/copilot/agents | `/agent` or `--agent=` |

Repository MCP configuration does **not** automatically apply to CLI. This is expected per [GitHub's MCP documentation](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent).

---

## Example prompts

| Task | Example prompt |
|------|----------------|
| Web search | *Search the web for current Node.js LTS version and summarize release notes.* |
| Scrape a page | *Scrape https://example.com/api and list documented endpoints.* |
| Package lookup (Pro) | *Look up the latest express version on npm and its main dependencies.* |
| Research issue | *Research how competitors price their API—use live web data and cite sources.* |

You do not need to know Bright Data tool names; the agent selects tools based on your request.
