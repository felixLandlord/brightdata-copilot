# Security — Bright Data Custom Agents

Security practices for API credentials, MCP access, and web data usage.

---

## API credentials

### Do

- Store the Bright Data API token as a **GitHub Agents secret** named `COPILOT_MCP_BRIGHTDATA_TOKEN`.
- Use **organization-level** secrets when the same token serves multiple repositories.
- Rotate tokens through the [Bright Data dashboard](https://brightdata.com/) if compromised.
- Limit org secret access to **selected repositories** when possible.

### Do not

- Commit API tokens to git (including in agent files—the agents use secret references only).
- Store tokens in repository Actions, Codespaces, or Dependabot secrets for MCP use—cloud agent only reads **Agents** secrets.
- Share tokens in chat, issues, or pull request comments.

Reference: [Configure secrets for Copilot cloud agent](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables)

---

## MCP access scope

### Cloud agent

- MCP tools in the agent profile are limited to `brightdata/*` plus standard Copilot tools (`read`, `edit`, `search`, `execute`).
- Copilot uses MCP tools **autonomously without asking for approval** once configured ([configure MCP servers](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers)).
- Built-in GitHub and Playwright MCP servers are also available by default; Bright Data agents are instructed to use Bright Data only for web data.

### Copilot CLI

- MCP config lives in `~/.copilot/mcp-config.json` on the user's machine.
- Users are responsible for protecting local config files that contain tokens.
- CLI may prompt before running shell commands; MCP tool use follows CLI permission settings.

---

## Network and data flow

| Component | Connection |
|-----------|------------|
| Cloud agent → Bright Data | HTTPS to `https://mcp.brightdata.com` |
| Target websites | Requested by Bright Data infrastructure, not the developer's machine |
| CLI → Bright Data | HTTPS from the user's machine to `mcp.brightdata.com` |

Cloud agent session logs **mask** secret values ([secrets documentation](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables)).

---

## Authentication limitations

Copilot cloud agent and Copilot code review ([MCP and cloud agent](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent)):

- Support **MCP tools** only (not MCP resources or prompts).
- Do **not** support remote MCP servers that use **OAuth** for authentication.

Bright Data's hosted MCP uses token-based auth (API token in URL/header), which is compatible with cloud agent.

---

## Usage monitoring

- **Bright Data:** Monitor usage and billing in the Bright Data dashboard. Rapid mode includes 5,000 free requests/month; Pro mode is pay-as-you-go.
- **GitHub:** Copilot usage and AI credits follow your Copilot plan. See [Copilot plans](https://docs.github.com/en/copilot/get-started/plans).

---

## Agent behavior safeguards

The agent prompts instruct Copilot to:

- Use only Bright Data MCP for live web content.
- Respect robots.txt, terms of service, and privacy.
- Not bypass paywalls or authentication.
- Avoid redundant MCP calls (especially important in Pro mode).

---

## Enterprise governance

Enterprise and organization administrators can:

- Disable cloud agent or MCP for members ([enterprise AI controls](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/enable-copilot-cloud-agent)).
- Restrict cloud agent to specific repositories ([org cloud agent settings](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent)).
- Configure outbound firewall allowlists for cloud agent.
- Apply rulesets on `.github-private` to control who can edit organization/enterprise agents.

---

## Reporting issues

- **Bright Data MCP:** [GitHub Issues](https://github.com/brightdata/brightdata-mcp/issues) or support@brightdata.com
- **GitHub Copilot:** `/feedback` in Copilot CLI or GitHub Support

---

## Official references

- [Configure MCP servers for your repository](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers)
- [Bright Data MCP security overview](https://docs.brightdata.com/mcp-server/overview)
- [Custom agents configuration](https://docs.github.com/en/copilot/reference/custom-agents-configuration)
