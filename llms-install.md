# Installing Product Admin by Nova (CIVAI)

This file is for AI assistants setting this server up on a user's behalf.

## What this is

A hosted remote MCP server. There is nothing to clone, build or run: it is a
Streamable HTTP endpoint at `https://nova.civai.co/mcp/agents/product-admin`.

The server exposes 34 tools.

## Step 1: get a credential

Ask the user to create a programmatic Nova API key at
https://nova.civai.co/api-keys and paste it back. Do not try to create one
for them, and do not write the key into any file that gets committed.

## Step 2: add the server

Add this to the MCP settings file, substituting the key:

```json
{
  "mcpServers": {
    "product-admin-by-nova": {
      "type": "streamableHttp",
      "url": "https://nova.civai.co/mcp/agents/product-admin",
      "headers": {
        "Authorization": "Bearer <USER_NOVA_API_KEY>"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

If the client rejects `type`, omit that line: the `url` plus `headers` pair is
enough for any Streamable HTTP client. `X-API-Key` works in place of
`Authorization: Bearer` if headers are easier to set that way.

## Step 3: confirm it worked

List the tools. You should get the full set back. To check the endpoint
independently of the client, note that discovery needs no credential:

```bash
curl -s -X POST https://nova.civai.co/mcp/agents/product-admin \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json, text/event-stream' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Troubleshooting

- **401 on a tool call.** The key is missing, wrong, or expired. Tool discovery
  succeeding proves nothing about the key, since discovery is unauthenticated.
- **Tools list but every call fails.** Check the user's Nova plan covers this
  agent at https://nova.civai.co/agent/product-admin.
- **No network.** This server cannot work offline or air-gapped.
