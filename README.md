# Notion workspace knowledge for AI assistants

Verify the connected integration, then search shared notes by title for a meeting briefing.

[Start locally](#get-started) · [Connect your assistant](#connect-your-assistant) · [Available tools](#available-tools) · [Hosted PipMCP setup](#use-pipmcp-for-production)

## Built for useful workflows

- **Build a Notion brief.** Read the integration’s bot user identity. Make that provider response available to your assistant before it proposes a next action.
- **Investigate a chosen Notion result.** Search pages and data sources shared with your integration by title. Use an account or record returned by your provider rather than guessing an identifier.
- **Keep the workflow read-only.** The shipped operations expose Notion reads. Combine their returned context in your assistant without adding a provider write operation.

## Why use this package?

Get a focused Notion MCP integration without assembling request mappings and a stdio transport yourself. The package provides documented input schemas, provider authentication handling and an example CLI, with the same tools available to your assistant.

The selected tools keep this Notion workflow read-only.

## Get started

**Requirements:** Node.js 22 or newer, npm, and access to the provider account you intend to use.

```sh
git clone https://github.com/jannikco/notion-mcp.git
cd notion-mcp
npm ci --ignore-scripts
npm test
npm start
```

`npm start` shows the tool inventory and workflow offline. It does not authenticate or call the provider. The tests use provider fixtures and stdio transport; they do not modify your account.

### Configure your provider account

The current release connects directly to the provider with **your own credentials**. It does not require a PipMCP key for local execution. Configure the environment of the process or MCP client that runs the server; `.env` files are not loaded automatically.

| Environment variable | What to supply |
| --- | --- |
| `NOTION_TOKEN` | Your own provider API key or access token. |

[Provider credential/reference documentation](https://raw.githubusercontent.com/makenotion/notion-mcp-server/main/scripts/notion-openapi.json). Keep credentials out of commits, issue reports and shared client configuration.

### Run your first provider read

After configuring the environment, run `get_bot_user`:

```sh
npm start -- --call get_bot_user '{}'
```

Replace sample account, record, file and project IDs with values from your authorized account. This command makes a real provider request and returns its result for you to inspect.

## Available tools

| Capability | MCP tool | Effect | Required inputs |
| --- | --- | --- | --- |
| Read the integration’s bot user identity. | `get_bot_user` | Read | None |
| Search pages and data sources shared with your integration by title. | `search_pages` | Read | None |

Tool schemas and bindings are in [connector.json](./connector.json). These are the selected operations this package ships.

### A practical tool sequence

1. Use `get_bot_user` — Read the integration’s bot user identity.
2. Use `search_pages` — Search pages and data sources shared with your integration by title.

## Connect your assistant

Add this stdio server to an MCP-compatible client. Replace the absolute path and credential placeholders. Use your client’s secret/environment controls instead of committing this configuration.

```json
{
  "mcpServers": {
    "notion-mcp": {
      "command": "node",
      "args": [
        "/absolute/path/notion-mcp/server.mjs"
      ],
      "env": {
        "NOTION_TOKEN": "YOUR_NOTION_TOKEN"
      }
    }
  }
}
```

`node server.mjs` starts the MCP server; `npm start` runs the example CLI. The server reserves stdout for MCP messages. If the client does not inherit your shell environment, pass the required variables explicitly.

## Read-only scope

The shipped Notion tools are read-only. This package does not provide a write command or silently expand the provider permissions.

## Use PipMCP for production

The local package is useful when you want to run these selected Notion operations in your own process. PipMCP provides a hosted place to configure provider connections, publish tools, issue endpoint access tokens, apply server-side policy and approvals, and inspect execution/usage records. Provider credentials and hosted execution are configured in that service.

**[Set up Notion in PipMCP](https://pipmcp.com/github/start?app=notion&integration=notion-mcp&utm_source=github&utm_campaign=github-notion-mcp&utm_content=readme)**

A managed catalog template is listed, but that is not evidence that its tools match this repository or that your account is connected. Complete provider setup and inspect the endpoint’s actual tool inventory and permissions before use.

Local credentials are not uploaded by this repository. A hosted endpoint uses its own PipMCP endpoint access token and published tool inventory; the local and hosted credentials are separate.

## Scope and validation

- The installed package has HTTP request fixtures and MCP stdio `tools/call` tests.
- Fixture success checks client behavior; it does not establish provider authentication, paid-plan access or live account execution.
- No live provider execution is claimed for this package by these tests.
- Hosted tool names, schemas, permissions and availability can differ from this local package. A queued or accepted job/message is not proof of completion or delivery. Provider API plans, account rules and limits apply.
- See [integration.json](./integration.json) for operation-scoped evidence and the provider-specific limits above.

## FAQ

**Do I need a Notion account?**

You need the provider account, token and permissions required by these operations. Provider charges and API limits remain with that account.

**Does this version require a PipMCP API key?**

This package uses your provider credentials for local execution. To use the hosted service, configure the provider connection in PipMCP and use the endpoint access token issued there.

**Why is a tool missing or a request rejected?**

This repository exposes the selected tools above. Check the required environment variables, provider scopes, account/region requirements and input IDs first. Hosted endpoints have their own published inventory and may exclude tools by role or policy. Do not retry a failed write without checking its outcome.

**Is this an official provider SDK or a hosted-service guarantee?**

This is an independently maintained MCP integration. Its fixtures and source references do not imply provider endorsement, unrestricted API access, or identical hosted behavior.

## Guides and related workflows

- [Notion integration guide](https://jannikco.github.io/pipmcp-connectors/integrations/notion/)
- [Notion workflow and tool sequence](https://jannikco.github.io/pipmcp-connectors/workflows/notion/)
- [More integration workflows](https://jannikco.github.io/pipmcp-connectors/workflows/)
- [Typed LeadAdapter API client](https://github.com/jannikco/linkedin-typescript-sdk)
- [LeadAdapter Python API client](https://github.com/jannikco/linkedin-python-sdk)
- [LinkedIn API quickstart](https://github.com/jannikco/linkedin-api)
- [LinkedIn people and post search](https://github.com/jannikco/linkedin-search-api)
- [LinkedIn post engagers](https://github.com/jannikco/linkedin-post-engagers)
- [LeadAdapter LinkedIn MCP server](https://github.com/jannikco/linkedin-mcp)
- [OpenAPI to MCP import starter](https://github.com/jannikco/openapi-to-mcp)
- [Swagger to MCP import starter](https://github.com/jannikco/swagger-to-mcp)
- [Notion API and credential reference](https://raw.githubusercontent.com/makenotion/notion-mcp-server/main/scripts/notion-openapi.json)
- [Additional official source 2](https://developers.notion.com/reference/post-search)

Maintained by [jannikco](https://github.com/jannikco). MIT for this repository’s code; dependencies retain their licenses. [Report a reproducible issue](https://github.com/jannikco/notion-mcp/issues) with redacted input and error details. For credentials or security issues, use [private contact](https://pipmcp.com/contact).
