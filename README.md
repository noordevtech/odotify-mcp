<img src="logo.svg" width="64" alt="Odotify">

# Odotify MCP server

Connect Claude, ChatGPT, Cursor or any MCP client to [Odotify](https://odotify.com), managed Odoo hosting on any Linux server.

Your assistant can look at your servers, Odoo instances, backups, deployments, jobs and audit log, restart an instance, start or verify a backup, and diagnose a slow Odoo database (slow queries, query plans, index health). It cannot delete anything, and every database change it proposes waits for a person to approve it in the Odotify dashboard.

- **Server URL:** `https://mcp.odotify.com/mcp` (remote, Streamable HTTP)
- **Sign-in:** OAuth 2.1 with automatic client registration, or an Odotify API key as a Bearer token
- **Registry name:** `com.odotify/mcp` in the [official MCP Registry](https://registry.modelcontextprotocol.io)
- **Plans:** AI assistant access is included in Starter, Pro and Agency ([pricing](https://odotify.com/pricing/))

## Setup

### Claude (claude.ai, Claude Desktop)

Settings → Connectors → Add custom connector → URL `https://mcp.odotify.com/mcp`. Claude opens Odotify's sign-in; pick the organization and whether the assistant may propose changes or only read.

### Claude Code

```bash
claude mcp add --transport http odotify https://mcp.odotify.com/mcp
```

### ChatGPT

Settings → Apps & Connectors → Advanced → Developer mode on → Create, with the URL `https://mcp.odotify.com/mcp` and OAuth authentication.

### Cursor

`~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "odotify": { "url": "https://mcp.odotify.com/mcp" }
  }
}
```

### VS Code (GitHub Copilot)

```json
{
  "servers": {
    "odotify": { "type": "http", "url": "https://mcp.odotify.com/mcp" }
  }
}
```

### Headless clients and scripts

Create an API key in the dashboard under **API keys**, with the role the client should have, and send it as `Authorization: Bearer odk_...`. Keys can propose changes but never approve them.

## Tools

| Class | Tools |
|---|---|
| Read | `list_servers`, `get_server_status`, `get_postgres_config`, `list_instances`, `get_instance_status`, `list_backups`, `list_backup_schedules`, `list_deployments`, `get_job`, `get_audit_logs`, `get_slow_queries`, `get_running_statements`, `get_query_plan`, `get_index_health`, `get_index_status`, `refresh_insights`, `list_db_changes`, `list_restore_drills` |
| Write | `restart_instance`, `create_backup`, `verify_backup` (restore drill into a scratch copy), `create_index` (proposal), `drop_index` (proposal) |
| Sensitive | `tune_postgres` (proposal; approved by typing the server name) |

Long-running work returns a `job_id`; poll `get_job`.

## Example prompts

- "Which of my Odoo instances are unhealthy, and why?"
- "When was the last good backup of acme-prod? Run a restore drill on it."
- "acme-prod is slow since this morning. Find the worst queries and propose an index."

## Privacy

The assistant never sees business records. Query statistics and plans have every literal value replaced by `?` on your own server before they leave it. Every call is written to the organization's audit log, and you can disconnect an assistant at any time under **AI assistants**.

## Links

- Odotify: https://odotify.com
- MCP guide: https://odotify.com/docs/mcp.md
- REST API (OpenAPI): https://api.odotify.com/openapi.json
- Server card: https://odotify.com/.well-known/mcp/server-card.json
- Odotify agent (open source): https://github.com/noordevtech/odotify-agent

Odoo is a trademark of Odoo S.A. Odotify is made by NoorDev Technologies and is not affiliated with Odoo S.A.

## Files in this repo

- `server.json`: the entry published to the official MCP Registry as `com.odotify/mcp`
- `openapi.json`: the public part of the Odotify REST API (OpenAPI 3.1); the live document is at https://api.odotify.com/openapi.json
