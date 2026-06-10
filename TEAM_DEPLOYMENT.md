# Team Deployment — Enterprise, Organization, and Repository

How to roll out Bright Data custom agents across your company. All policy steps below reference [official GitHub documentation](https://docs.github.com/en/copilot).

---

## Deployment levels at a glance

| Level | Agent file location | Who configures | Who can use |
|-------|---------------------|----------------|-------------|
| **Repository** | `.github/agents/*.agent.md` in each repo | Repo maintainers | Anyone with Copilot + repo access |
| **Organization** | `agents/*.agent.md` in org `.github-private` repo | Org owners | All org members (when released) |
| **Enterprise** | `agents/*.agent.md` in enterprise `.github-private` repo | Enterprise owners | All enterprise members (when released) |
| **CLI (per user)** | `~/.copilot/agents` (optional) | Each developer | That developer only |

**Name conflicts:** A repository-level agent overrides an organization-level agent with the same name, which overrides an enterprise-level agent ([custom agents configuration](https://docs.github.com/en/copilot/reference/custom-agents-configuration)).

---

## Access checklist (admins)

Use this order for Business/Enterprise deployments:

1. **Assign Copilot licenses** to users who need the agents.
2. **Enterprise** (if applicable): Enable **Copilot Cloud Agent** and **MCP servers in Copilot** under AI controls.
3. **Organization**: Enable **Copilot cloud agent** and **MCP servers on GitHub.com** in Copilot policies.
4. **Secrets**: Add `COPILOT_MCP_BRIGHTDATA_TOKEN` at org or repo level.
5. **Agent files**: Deploy agent profiles (see deployment options below).
6. **Validate**: Start a cloud agent session and check MCP tools in session logs.

Third-party MCP and cloud agent are **disabled by default** for Business/Enterprise ([enterprise enablement guide](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/enable-copilot-cloud-agent)).

---

## Enterprise setup

### Enable cloud agent and MCP

1. Navigate to your **enterprise** on GitHub.com.
2. **AI controls** → **Agents** → **Copilot Cloud Agent** → choose policy (e.g. enabled for selected organizations).
3. **AI controls** → **MCP** → enable **MCP servers in Copilot**.

> **Note:** "MCP Registry URL" and "Restrict MCP access to registry servers" policies do **not** apply to Copilot cloud agent.

Reference: [Enabling cloud agent in your enterprise](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/enable-copilot-cloud-agent)

### Enterprise-wide custom agents (optional)

To make Bright Data agents available in **all repositories** without copying files into each repo:

1. Choose an organization in your enterprise to host agents.
2. Create a repository named **`.github-private`** using [GitHub's custom agents template](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents).
3. Set visibility to **Internal** (all members) or **Private** (manual access).
4. Place agent files in **`agents/`** (root), not `.github/agents/`:
   ```
   agents/brightdata-rapid-agent.agent.md
   agents/brightdata-pro-agent.agent.md
   ```
5. Enterprise → **AI controls** → **Custom agents** → select the hosting organization.

Reference: [Preparing custom agents in your enterprise](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-enterprise/manage-agents/prepare-for-custom-agents)

### Testing before wide release

1. Put test agents in `.github/agents/` inside `.github-private` (visible only when working in that repo).
2. Test at [github.com/copilot/agents](https://github.com/copilot/agents) with `.github-private` selected.
3. Move agents to `agents/` when ready for organization/enterprise-wide availability.

Reference: [Testing and releasing custom agents](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/test-custom-agents)

---

## Organization setup

### Enable policies

1. Organization **Settings** → **Copilot** → **Policies**.
2. Enable **Copilot cloud agent**.
3. Enable **MCP servers on GitHub.com**.

Reference: [Adding cloud agent to your organization](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent)

### Control which repositories can use cloud agent

Organization **Settings** → **Copilot** → **Cloud agent** → **Repository access**.

By default, cloud agent is available in all repos. You can block specific repositories.

### Organization-wide agents (optional)

Same as enterprise, but scoped to one organization:

1. Create **`.github-private`** in the organization ([prepare for custom agents](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/prepare-for-custom-agents)).
2. Add agents to `agents/` directory.
3. Add org-level Agents secret `COPILOT_MCP_BRIGHTDATA_TOKEN` with access to required repositories.

---

## Repository setup (single project)

Best when Bright Data is needed in one or a few repositories:

1. Copy `.github/agents/brightdata-*.agent.md` into the target repo.
2. Add repository (or org) Agents secret `COPILOT_MCP_BRIGHTDATA_TOKEN`.
3. Merge to default branch.
4. Tell developers to select the Bright Data agent from the dropdown.

No `.github-private` repository required.

---

## Secrets at scale

| Scope | When to use | Path |
|-------|-------------|------|
| **Repository** | One repo, one token | Settings → Secrets and variables → Agents |
| **Organization** | Shared token across many repos | Org Settings → Secrets and variables → Agents |

Rules ([GitHub docs](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables)):

- MCP secrets must start with `COPILOT_MCP_`.
- Repository-level secrets override organization-level secrets with the same name.
- Cloud agent does **not** access Actions, Codespaces, or Dependabot secrets—only **Agents** secrets.

---

## Copilot CLI rollout

CLI MCP is **per machine**, not inherited from GitHub:

1. Install [Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/set-up-copilot-cli/install-copilot-cli).
2. Each user adds Bright Data MCP via `/mcp add` or `~/.copilot/mcp-config.json`.
3. Users clone a repo containing agent files, or copy agents to `~/.copilot/agents`.
4. Enterprise/org MCP registry allowlists may restrict which MCP servers CLI can use ([CLI MCP docs](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers)).

Provide developers a copy of [SETUP_GUIDE.md — Copilot CLI](SETUP_GUIDE.md#part-2--copilot-cli).

---

## Firewall considerations

Cloud agent runs in a GitHub-managed environment. If your organization restricts outbound network access, ensure **Bright Data MCP** is reachable:

- Hosted endpoint: `https://mcp.brightdata.com`

Organization owners can configure allowlists under **Settings** → **Copilot** → **Cloud agent** → firewall settings.

Reference: [Customizing the cloud agent firewall](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)

---

## Plan requirements

| Plan | Custom agents | Cloud agent default | MCP |
|------|---------------|---------------------|-----|
| Copilot Free | No | — | — |
| Copilot Pro / Pro+ / Max | Yes | Enabled | Yes |
| Copilot Business | Yes | Disabled (admin must enable) | Disabled (admin must enable) |
| Copilot Enterprise | Yes | Disabled (admin must enable) | Disabled (admin must enable) |

See [Copilot plans](https://docs.github.com/en/copilot/get-started/plans).

---

## Official references

- [Setting up Copilot for your organization](https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/enable-copilot/set-up-for-organization)
- [Setting up Copilot for your enterprise](https://docs.github.com/en/copilot/how-tos/copilot-on-github/set-up-copilot/enable-copilot/set-up-for-enterprise)
- [Managing access to cloud agent](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/access-management)
- [Configure MCP servers for your repository](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers)
