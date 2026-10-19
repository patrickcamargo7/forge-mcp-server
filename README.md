# Laravel Forge MCP Server

A Model Context Protocol (MCP) server for interacting with Laravel Forge API. This server enables AI assistants to manage Laravel Forge servers, sites, and deployments, and to read production logs.

This is a fork of [`@bretterer/forge-mcp-server`](https://github.com/bretterer/forge-mcp-server) that adds log-reading tools, so you can debug production issues from your editor (Cursor, Claude Desktop, etc.).

## Features

- List and get details of Laravel Forge servers
- Manage sites on Laravel Forge servers
- Deploy sites and manage deployment scripts
- Toggle quick deploy functionality
- View deployment history
- Reboot servers and check server load
- **Read production logs** (Nginx access/error, application, deployment, server-level)

## Installation

```bash
npm install @patrickcamargo7/forge-mcp-server
```

## Usage

### Prerequisites

You need a Laravel Forge API key with the **`server:view`** scope (sufficient for log reading + all read-only tools). Add other scopes only for the state-changing tools you intend to use.

### Environment Variables

| Variable | Required | Default | Description |
|---|---|---|---|
| `FORGE_API_KEY` | yes | — | Forge API token |
| `FORGE_LOG_LINES_LIMIT` | no | `200` | Max lines returned per log tool call (keeps AI context manageable) |

```bash
export FORGE_API_KEY=your_forge_api_key
export FORGE_LOG_LINES_LIMIT=500   # optional
```

### Running the Server

```bash
npx forge-mcp-server
```

Or add it to your project:

```javascript
import { ForgeServer } from '@patrickcamargo7/forge-mcp-server';

const server = new ForgeServer();
server.run().catch(console.error);
```

### Cursor / MCP clients

```json
{
  "mcpServers": {
    "forge": {
      "command": "npx",
      "args": ["-y", "@patrickcamargo7/forge-mcp-server"],
      "env": { "FORGE_API_KEY": "your_forge_api_key" }
    }
  }
}
```

## Available Tools

| Tool | Description |
|---|---|
| `list_servers` | List all Laravel Forge servers |
| `get_server` | Get details of a specific server |
| `list_sites` | List all sites on a server |
| `get_site` | Get details of a specific site |
| `deploy_site` | Deploy a site |
| `get_deployments` | Get deployment history for a site |
| `get_deployment_script` | Get the deployment script for a site |
| `update_deployment_script` | Update the deployment script for a site |
| `toggle_quick_deploy` | Enable or disable quick deploy for a site |
| `reboot_server` | Reboot a server |
| `get_server_load` | Get server load metrics |
| `reset_deployment_state` | Reset deployment state for a site |
| `get_site_nginx_access_log` | **Read the Nginx access log for a site** |
| `get_site_nginx_error_log` | **Read the Nginx error log for a site (diagnose 5xx)** |
| `get_site_application_log` | **Read the application log for a site (Laravel errors)** |
| `get_deployment_log` | **Read the output of a specific deployment (diagnose failures)** |
| `get_server_log` | **Read a server-level log by key (e.g. `mysql`, `redis`, `system`)** |

> Logs are truncated to the last `FORGE_LOG_LINES_LIMIT` lines (default 200) so AI context stays manageable.

## Security notes

- All log tools are **read-only**.
- Only `server:view` is required for log reading. Generate a token with the minimum scope you need.
- Logs may contain user data (IPs, request bodies, error messages). Don't share them with untrusted clients.

## Development

### Setup

```bash
git clone https://github.com/patrickcamargo7/forge-mcp-server.git
cd forge-mcp-server
npm install
```

### Build

```bash
npm run build
```

### Run in Development Mode

```bash
npm run dev
```

### Inspect with the MCP Inspector

```bash
npx -y @modelcontextprotocol/inspector npx forge-mcp-server
```

## License

MIT

## Acknowledgements

Forked from [`bretterer/forge-mcp-server`](https://github.com/bretterer/forge-mcp-server).
