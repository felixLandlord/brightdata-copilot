# Bright Data Custom Agents for GitHub Copilot

This repository provides **GitHub Copilot custom agents** that connect Copilot to [Bright Data's Web MCP server](https://brightdata.com/ai/mcp-server) for live web search, scraping, and (in Pro mode) browser automation and structured data extraction.

There is no separate install or marketplace. The agents are Markdown files in `.github/agents/`. Once this repository is on your GitHub account and access is configured, users **select the agent manually** from the Copilot agent dropdown when they want Bright Data web tools.

## What's included

| Agent | File | Mode | Best for |
|-------|------|------|----------|
| **Bright Data Rapid** | `brightdata-rapid-agent.agent.md` | Free tier (5,000 requests/month) | Search, discover, scrape |
| **Bright Data Pro** | `brightdata-pro-agent.agent.md` | Pay-as-you-go | 60+ tools including browser automation and `web_data_*` APIs |

Both agents route all live web work through Bright Data MCP only—not built-in web fetch or training data.

## Quick start

### Prerequisites

- A **paid GitHub Copilot plan** (Pro, Pro+, Business, or Enterprise). Custom agents are not available on Copilot Free.
- A **Bright Data API token** ([sign up free](https://brightdata.com/ai/mcp-server)).
- For **Copilot cloud agent on GitHub.com**: an administrator must enable cloud agent and MCP policies (Business/Enterprise are disabled by default).
- For **Copilot CLI**: each user installs the CLI and configures MCP locally (see [SETUP_GUIDE.md](SETUP_GUIDE.md)).

### Cloud agent (GitHub.com) — 5 steps

1. Add this repository (or copy the agent files into your repo's `.github/agents/`).
2. Add an **Agents secret** named `COPILOT_MCP_BRIGHTDATA_TOKEN` with your Bright Data API token ([how to add secrets](SETUP_GUIDE.md#step-3-add-your-bright-data-api-token)).
3. Ensure **Copilot cloud agent** and **MCP servers** are enabled at enterprise/org level if applicable ([TEAM_DEPLOYMENT.md](TEAM_DEPLOYMENT.md)).
4. Merge agent files to the repository's **default branch**.
5. Open [github.com/copilot/agents](https://github.com/copilot/agents), select your repository, choose **Bright Data Rapid** or **Bright Data Pro** from the agent dropdown, and start a task.

### Copilot CLI — 4 steps

1. [Install Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli).
2. Run `copilot` and authenticate with `/login`.
3. Add the Bright Data MCP server locally with `/mcp add` ([SETUP_GUIDE.md](SETUP_GUIDE.md#copilot-cli-setup)).
4. Run `/agent` and select **brightdata-rapid-agent** or **brightdata-pro-agent**, or use `copilot --agent=brightdata-rapid-agent`.

> **Important:** Repository MCP settings used by cloud agent on GitHub.com do **not** carry over to Copilot CLI. CLI users must configure MCP on their own machine.

## Documentation

| Guide | Purpose |
|-------|---------|
| [SETUP_GUIDE.md](SETUP_GUIDE.md) | Step-by-step setup for cloud agent and CLI |
| [AGENT_REFERENCE.md](AGENT_REFERENCE.md) | Agent files, tools, and configuration |
| [TEAM_DEPLOYMENT.md](TEAM_DEPLOYMENT.md) | Deploy at enterprise, organization, or repository level |
| [SECURITY.md](SECURITY.md) | API tokens, secrets, and data handling |
| [TROUBLESHOOTING.md](TROUBLESHOOTING.md) | Common problems and fixes |
| [CHANGELOG.md](CHANGELOG.md) | Version history |

## Official references

- [GitHub: Creating custom agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/create-custom-agents)
- [GitHub: Custom agents configuration](https://docs.github.com/en/copilot/reference/custom-agents-configuration)
- [GitHub: Using Copilot cloud agent on GitHub](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)
- [GitHub: Copilot CLI getting started](https://docs.github.com/en/copilot/how-tos/copilot-cli/cli-getting-started)
- [Bright Data MCP server](https://github.com/mcp/brightdata/brightdata-mcp)
- [Bright Data Web MCP](https://brightdata.com/ai/mcp-server)

## Repository ownership

After transfer to your GitHub account, you own this repository and the agents. To update an agent, edit the `.agent.md` file and merge to the default branch—changes apply to everyone using agents from that repository. Copies of the repo do not receive updates automatically.
