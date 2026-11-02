# Product Admin by Nova (CIVAI)

I manage your Nova products, create/update/delete, payment & logistics config, icons, webhook secrets, bank-transfer onboarding, and deposit/bank-credit reconciliation

**Keywords:** mcp-server, model-context-protocol, nova-by-civai, remote-mcp, streamable-http, civai, cursor, claude-desktop, product-management, payments, bank-transfer, deposits, reconciliation

Remote **Model Context Protocol (MCP)** server from **Nova by CIVAI**. Connect from **Cursor**, **Claude Desktop**, **Claude Code**, or any MCP client that supports **Streamable HTTP**.

## Links

| Resource | URL |
|----------|-----|
| MCP endpoint | https://nova.civai.co/mcp/agents/product-admin |
| Agent page | https://nova.civai.co/agent/product-admin |
| API keys | https://nova.civai.co/api-keys |
| Official Registry | `co.civai.nova/product-admin` |

## Prerequisites

1. Sign in at [nova.civai.co/api-keys](https://nova.civai.co/api-keys)
2. Create a **programmatic Nova API key** (JWT)
3. Add the server to your MCP client with `Authorization: Bearer <your_nova_jwt>`

## Cursor Marketplace plugin

This repository ships a **Cursor Plugin** manifest (`.cursor-plugin/plugin.json` + root `mcp.json`) for one-click install after marketplace review. Users configure `NOVA_API_KEY` in Cursor **Plugins → Configure** (never commit secrets).

Local test: copy this folder to `~/.cursor/plugins/local/product-admin-by-nova`, reload the window, set `NOVA_API_KEY`, verify MCP tools load.

## Cursor (manual MCP)

Add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "product-admin-by-nova": {
      "url": "https://nova.civai.co/mcp/agents/product-admin",
      "headers": {
        "Authorization": "Bearer YOUR_NOVA_API_KEY"
      }
    }
  }
}
```

## Claude Code

Install as a plugin from the Nova marketplace, which prompts for your API key on first enable:

```bash
/plugin marketplace add civai-technologies/nova-catalog-mcp-plugins-by-nova-civai
/plugin install product-admin-by-nova --marketplace civai-technologies/nova-catalog-mcp-plugins-by-nova-civai
```

Or add the MCP server directly:

```bash
claude mcp add --transport http product-admin-by-nova \
  https://nova.civai.co/mcp/agents/product-admin \
  --header "Authorization: Bearer $NOVA_API_KEY"
```

Setting this up on someone's behalf? [`llms-install.md`](./llms-install.md) is written for that.

## Transport

- **Type:** Streamable HTTP (stateless)
- **Auth:** Bearer JWT or `X-API-Key` header
- **Tools:** 34 (bundled catalog)

## About Nova by CIVAI

**Nova by CIVAI** is a platform for running, building, and deploying AI agents. This MCP server exposes **Product Admin** tools to external MCP hosts without sharing upstream credentials.

| | |
|---|---|
| **Company** | CIVAI TECHNOLOGIES |
| **Creator** | [Kayode Femi Amoo](https://x.com/usecodenaija) |
| **Product** | [Nova by CIVAI](https://nova.civai.co) |

## License

MIT. See [LICENSE](LICENSE).
