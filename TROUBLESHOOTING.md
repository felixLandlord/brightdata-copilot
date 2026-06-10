# Troubleshooting — Bright Data Custom Agents

Common issues and how to fix them. Steps are based on [GitHub documentation](https://docs.github.com/en/copilot) and Bright Data MCP guidance.

---

## Quick diagnosis

| Symptom | Likely cause | See section |
|---------|--------------|-------------|
| Agent not in dropdown | Files not on default branch, or no Copilot license | [Agent not visible](#agent-not-visible-in-dropdown) |
| MCP tools fail on GitHub.com | Missing secret, MCP policy disabled, or invalid token | [Cloud agent MCP](#cloud-agent-mcp-not-working) |
| Works on GitHub.com but not CLI | CLI has separate MCP config | [Cloud vs CLI](#works-in-cloud-agent-but-not-in-cli) |
| Web answers without live data | Wrong agent selected (default cloud agent) | [Wrong agent](#wrong-agent-selected) |
| 401 / auth errors | Invalid or missing Bright Data token | [Authentication](#authentication-errors) |
| Timeouts on scrape | Slow site or low timeout | [Timeouts](#timeouts) |

---

## Agent not visible in dropdown

**Check:**

1. Agent files exist at `.github/agents/brightdata-rapid-agent.agent.md` (and/or pro) on the **default branch**.
2. You have a **paid Copilot plan** (not Copilot Free).
3. You selected the correct **repository** in the prompt box at [github.com/copilot/agents](https://github.com/copilot/agents).
4. Refresh the page after merging agent files.

For org/enterprise agents in `.github-private`, files must be in **`agents/`** (not `.github/agents/`) for wide availability. See [TEAM_DEPLOYMENT.md](TEAM_DEPLOYMENT.md).

Reference: [Creating custom agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents)

---

## Cloud agent MCP not working

**Admin checklist (Business/Enterprise):**

1. User has a Copilot license.
2. **Copilot cloud agent** policy is enabled (enterprise and/or org).
3. **MCP servers in Copilot** / **MCP servers on GitHub.com** policy is enabled.
4. Target organization is included in enterprise cloud agent policy (if applicable).

**Repository checklist:**

1. Agents secret `COPILOT_MCP_BRIGHTDATA_TOKEN` exists (repo or org level).
2. Secret name is exact (case-insensitive but must start with `COPILOT_MCP_`).
3. Bright Data token is valid.

**Validate:**

1. Assign a test issue to Copilot with the Bright Data agent selected.
2. Session log → **Copilot** → **Start MCP Servers** → confirm Bright Data tools appear.

Reference: [Configure MCP servers](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers)

---

## Works in cloud agent but not in CLI

This is **expected** if CLI MCP was not configured locally.

| Surface | MCP configuration location |
|---------|---------------------------|
| Cloud agent | Agent file + Agents secrets on GitHub |
| Copilot CLI | `~/.copilot/mcp-config.json` on each machine |

**Fix for CLI:**

1. Run `/mcp show` — is `brightdata` listed?
2. If missing, run `/mcp add` (see [SETUP_GUIDE.md](SETUP_GUIDE.md)).
3. If listed but no tools, ensure `"tools": ["*"]` (empty array disables all tools).
4. Verify URL and Authorization header match your token.
5. Check enterprise/org MCP allowlist policies that may block CLI servers.

Reference: [Adding MCP servers for Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers)

---

## Wrong agent selected

Bright Data tools are only available when **Bright Data Rapid** or **Bright Data Pro** is selected from the agent dropdown.

The default Copilot cloud agent does not include Bright Data MCP. If web tasks return stale or generic answers, confirm the correct agent is selected before starting the session or assigning an issue.

---

## Authentication errors

**Symptoms:** 401, 403, or "authentication" in MCP logs.

**Fix:**

1. Confirm token at [Bright Data](https://brightdata.com/ai/mcp-server).
2. Cloud agent: re-save `COPILOT_MCP_BRIGHTDATA_TOKEN` Agents secret.
3. CLI: update token in `/mcp edit brightdata` or `mcp-config.json`.
4. Pro agent: URL must include `&pro=1` and account must have Pro eligibility.

---

## Timeouts

**Symptoms:** Scrape or browser tools fail on complex or slow sites.

**Fix:**

1. Retry with `scrape_batch` or an alternate URL.
2. Pro browser tools: allow **180s+** before treating as failed (agent instructions).
3. Bright Data `POLLING_TIMEOUT` and `BASE_TIMEOUT` env vars apply to local/npm MCP only—not the hosted URL used by these agents.

Reference: [Bright Data MCP configuration](https://github.com/mcp/brightdata/brightdata-mcp#-configuration)

---

## MCP works but tool not called

Copilot may not invoke MCP tools unless the prompt clearly requires live web data.

**Try explicit prompts:**

- *Use Bright Data to search the web for…*
- *Scrape this URL and quote the page content: https://…*

---

## CLI: organization MCP policy blocks server

If your enterprise configured an MCP registry allowlist, only permitted servers run in CLI—even when cloud agent works with repository-configured MCP.

**Fix:** Ask your enterprise admin to allow the Bright Data MCP endpoint, or use cloud agent for centralized MCP access.

Reference: [Adding MCP servers for Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers)

---

## CLI: `spawn npx ENOENT` (local MCP only)

If running `@brightdata/mcp` locally instead of the hosted URL, Node.js must be installed.

**Fix:** Use the hosted server (`https://mcp.brightdata.com/mcp?token=...`) or install Node.js.

Reference: [Bright Data MCP troubleshooting](https://github.com/mcp/brightdata/brightdata-mcp#-common-issues--solutions)

---

## Copilot CLI not available

Copilot CLI requires a paid Copilot subscription. Organization/enterprise admins can disable CLI in policies.

Reference: [Installing Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli)

---

## OAuth-based MCP not supported

Cloud agent does not support remote MCP servers that use OAuth. Bright Data hosted MCP uses API tokens, which is supported.

Reference: [MCP and cloud agent](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent)

---

## Still stuck?

Gather this information before contacting support:

1. Surface: cloud agent, CLI, or IDE.
2. Agent selected: rapid or pro.
3. Cloud: session log excerpt from **Start MCP Servers** (redact secrets).
4. CLI: output of `/mcp show brightdata` (redact token).
5. Plan type: Business, Enterprise, Pro, etc.

**References:**

- [Troubleshoot Copilot cloud agent](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/troubleshoot-cloud-agent)
- [Bright Data MCP support](https://github.com/mcp/brightdata/brightdata-mcp)
