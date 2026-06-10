# Agent Reference — Bright Data Custom Agents

Reference for the two Bright Data agent profiles in this repository.

---

## Agent profiles

| Property | Bright Data Rapid | Bright Data Pro |
|----------|-------------------|-----------------|
| **File** | `.github/agents/brightdata-rapid-agent.agent.md` | `.github/agents/brightdata-pro-agent.agent.md` |
| **Name** | `brightdata-rapid-agent` | `brightdata-pro-agent` |
| **Bright Data mode** | Rapid (free tier) | Pro (pay-as-you-go) |
| **MCP URL** | `https://mcp.brightdata.com/mcp?token=...` | Same + `&pro=1` |
| **Monthly allowance** | 5,000 requests free | Usage-based billing |

### When to use which

- **Rapid** — Web search, discover, and scrape. No browser automation or `web_data_*` platform APIs.
- **Pro** — Everything in Rapid plus 60+ tools: browser automation, structured extractors (Amazon, LinkedIn, npm, etc.), and `extract`.

Pricing details: [Bright Data MCP pricing](https://github.com/mcp/brightdata/brightdata-mcp#-pricing--modes).

---

## YAML configuration

Both agents use the [GitHub custom agent format](https://docs.github.com/en/copilot/reference/custom-agents-configuration):

```yaml
---
name: brightdata-rapid-agent
description: Real-time web research via Bright Data MCP (free tier)
tools: ['execute', 'read', 'edit', 'search', 'brightdata/*']
mcp-servers:
  brightdata:
    type: 'http'
    url: 'https://mcp.brightdata.com/mcp?token=${{ secrets.COPILOT_MCP_BRIGHTDATA_TOKEN }}'
    headers: {"Authorization": "Bearer ${{ secrets.COPILOT_MCP_BRIGHTDATA_TOKEN }}"}
    tools: ["*"]
---
```

Pro agent adds `&pro=1` to the URL.

### Key fields

| Field | Purpose |
|-------|---------|
| `description` | **Required.** Shown in the agent dropdown. |
| `tools` | Limits Copilot tools. `brightdata/*` enables all Bright Data MCP tools. |
| `mcp-servers` | Connects to Bright Data. **Used by cloud agent only** (not VS Code IDE agents per GitHub docs). |
| `target` | Optional: `vscode` or `github-copilot`. Omitted = both environments. |

### Secret reference

| Secret name | Used by |
|-------------|---------|
| `COPILOT_MCP_BRIGHTDATA_TOKEN` | Cloud agent MCP connection |

Syntax options in agent YAML ([GitHub docs](https://docs.github.com/en/copilot/reference/custom-agents-configuration)):

- `${{ secrets.COPILOT_MCP_BRIGHTDATA_TOKEN }}`
- `$COPILOT_MCP_BRIGHTDATA_TOKEN`
- `${COPILOT_MCP_BRIGHTDATA_TOKEN}`

---

## Tools by agent

### Rapid agent tools

| Tool | Use |
|------|-----|
| `discover` | Broad, intent-ranked web search |
| `search_engine` | Targeted search queries |
| `search_engine_batch` | Multiple searches at once |
| `scrape_as_markdown` | Fetch a URL as markdown |
| `scrape_batch` | Multiple URLs at once |

### Pro agent — additional capabilities

| Category | Examples |
|----------|----------|
| Browser automation | `scraping_browser_snapshot`, `scraping_browser_click_ref`, `scraping_browser_screenshot` |
| Platform data | `web_data_amazon_product`, `web_data_linkedin_posts`, `web_data_npm_package`, etc. |
| Extraction | `extract` for structured fields from pages |

Full tool list and groups: [Bright Data MCP repository](https://github.com/mcp/brightdata/brightdata-mcp#-advanced-tool-selection).

---

## Agent behavior (summary)

Both agents instruct Copilot to:

1. Use **only** Bright Data MCP for live web data—not built-in web fetch or model knowledge.
2. Use repo `read` / `search` only for code in the current repository.
3. Cite sources (title + URL) in responses.
4. Retry once on failure, then report errors clearly.

Pro agent adds browser workflow rules (snapshot before click, re-snapshot after navigation).

---

## Where agents are available

Per [GitHub about custom agents](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-custom-agents):

| Surface | How to select |
|---------|---------------|
| [github.com/copilot/agents](https://github.com/copilot/agents) | Agent dropdown in prompt box |
| Issue assignment | Agent dropdown when assigning to Copilot |
| Copilot Chat `/task` | Agent dropdown |
| Copilot CLI | `/agent`, `--agent=name`, or mention in prompt |

**Users must select the Bright Data agent** from the dropdown when they want web data tools. The default cloud agent does not include Bright Data MCP.

To prevent automatic agent selection by Copilot, set `disable-model-invocation: true` in the agent YAML ([configuration reference](https://docs.github.com/en/copilot/reference/custom-agents-configuration)).

---

## MCP processing order (cloud agent)

When multiple MCP sources exist, GitHub processes them in this order ([custom agents configuration](https://docs.github.com/en/copilot/reference/custom-agents-configuration)):

1. Built-in servers (GitHub, Playwright)
2. **Custom agent MCP** (Bright Data, configured in the agent file)
3. Repository-level MCP settings

Agent-level Bright Data config in this repo is sufficient for most setups. Repository-level MCP is optional if you want the same server available to the default cloud agent or Copilot code review.

---

## Updating agents

1. Edit the `.agent.md` file in `.github/agents/`.
2. Merge to the default branch.
3. Changes apply on the next session (versioning follows git commit SHA per GitHub).

No redeployment or marketplace publish step is required.

---

## Official references

- [Custom agents configuration](https://docs.github.com/en/copilot/reference/custom-agents-configuration)
- [MCP and Copilot cloud agent](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent)
- [Bright Data MCP docs](https://docs.brightdata.com/mcp-server/overview)
- [Bright Data MCP on GitHub](https://github.com/mcp/brightdata/brightdata-mcp)
