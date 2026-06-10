# Changelog

All notable changes to the Bright Data GitHub Copilot custom agents in this repository.

Format based on [Keep a Changelog](https://keepachangelog.com/).

---

## [1.0.0] — 2026-06-10

### Added

- **brightdata-rapid-agent** — Rapid mode (free tier): search, discover, scrape via Bright Data MCP.
- **brightdata-pro-agent** — Pro mode: 60+ tools including browser automation and `web_data_*` APIs.
- Documentation:
  - README.md — overview and quick start
  - SETUP_GUIDE.md — cloud agent and CLI setup
  - AGENT_REFERENCE.md — agent configuration and tools
  - TEAM_DEPLOYMENT.md — enterprise, organization, and repository rollout
  - SECURITY.md — credentials and governance
  - TROUBLESHOOTING.md — common issues

### Configuration

- MCP via hosted Bright Data endpoint (`https://mcp.brightdata.com/mcp`).
- Agents secret: `COPILOT_MCP_BRIGHTDATA_TOKEN`.
- Pro agent URL includes `&pro=1`.

---

## Upcoming

Document changes here when agent behavior or configuration is updated.
