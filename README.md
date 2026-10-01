# Aidelly Claude Code Plugin

[![Aidelly/claude-plugin MCP server](https://glama.ai/mcp/servers/Aidelly/claude-plugin/badges/score.svg)](https://glama.ai/mcp/servers/Aidelly/claude-plugin)

Automate Aidelly social media management directly from Claude Code. Create, schedule, and manage posts across all connected platforms with AI-powered MCP tools.

## Features

- **Create & Schedule Posts** – Publish instantly or schedule for later across multiple platforms
- **Approval Workflows** – Require and manage post approvals before publishing
- **Analytics** – Pull engagement metrics and top-performing content
- **Multi-Platform Support** – Instagram, Facebook, LinkedIn, Twitter/X, TikTok, YouTube, Pinterest, Bluesky, Threads, Mastodon, Google Business
- **Media Upload** – Fetch and upload images/videos directly

## Installation

> **Sign-in is one click.** The Aidelly MCP server supports OAuth 2.1 with PKCE and dynamic client registration. Claude walks you through an Aidelly login and consent screen, so there's no key or workspace ID to copy, and the plugin never reads credentials from your machine.

### Install via Claude Code Plugin Marketplace

```bash
/plugin marketplace add Aidelly/claude-plugin
```

Then sign in to Aidelly (see [Setup](#setup) below).

### Manual Installation (Development)

Clone this repository and point Claude Code to the local plugin path:

```bash
git clone https://github.com/Aidelly/claude-plugin
cd claude-plugin
# Add to Claude Code plugin settings
```

> **Note:** Ensure `.mcp.json` is committed in your local clone. The Aidelly monorepo ignores `*.mcp.json` files, so the public `Aidelly/claude-plugin` repository explicitly commits it. If you see a missing `.mcp.json` after cloning, run `git check-ignore .mcp.json` — if no output, it's been properly committed.

## Setup

### 1. Sign in

The first time Claude uses an Aidelly tool, Claude Code asks you to sign in:

1. Run `/mcp` in Claude Code and choose **aidelly → Authenticate** (or start with any Aidelly request).
2. Your browser opens the Aidelly login and consent screen. Log in and approve access.
3. Return to Claude Code. The server shows as connected.

Signing in needs an Aidelly plan that includes API access. The token is issued to your account and expires; sign in again from `/mcp` when it does.

### 2. Pick a workspace

If you manage several workspaces, ask Claude to list them (`aidelly_list_workspaces`) and say which one to use. Claude passes the workspace to each tool, so there's nothing to configure.

### 3. Verify the connection

In Claude Code, run:

```
List my Aidelly workspaces and connected accounts.
```

Claude should return your workspace data.

## Usage

### Create a Post

```
Create a new post on Instagram with the text "Hello world!" and upload media.
```

### Schedule a Post

```
Schedule a post for tomorrow at 2 PM with approval required.
```

### Check Pending Approvals

```
List all posts waiting for approval and approve the first one.
```

### Pull Analytics

```
Get the top 10 posts from the last 30 days and their engagement metrics.
```

## Skill Documentation

Full endpoint reference and examples: [skills/aidelly-social/SKILL.md](./skills/aidelly-social/SKILL.md)

## Configuration

### .mcp.json

The plugin wires to Aidelly's remote MCP server over streamable HTTP. There are no headers and no environment variables; OAuth handles sign-in:

```json
{
  "mcpServers": {
    "aidelly": {
      "type": "http",
      "url": "https://app.aidelly.ai/api/mcp/public-api"
    }
  }
}
```

### plugin.json

Metadata for the Claude Code plugin directory:

```json
{
  "name": "aidelly",
  "description": "Aidelly social content automation for Claude Code — create posts, schedule content, check approvals, and pull analytics.",
  "author": { "name": "Aidelly", "url": "https://aidelly.ai" },
  "homepage": "https://aidelly.ai",
  "repository": "https://github.com/Aidelly/claude-plugin",
  "privacyPolicyUrl": "https://www.aidelly.ai/privacy-policy",
  "icon": "./assets/icon.png",
  "version": "0.2.2"
}
```

## Troubleshooting

### "Unauthorized" Error

- Run `/mcp`, choose **aidelly → Authenticate**, and sign in again (the token may have expired)
- Confirm your Aidelly plan includes API access

### "Forbidden" Error

- Make sure the workspace Claude is using is one your Aidelly account can access
- Ask Claude to list your workspaces and pick the right one

### Rate Limit Exceeded

- The server allows 120 tool calls per minute per signed-in user
- Retry after a few seconds with exponential backoff

### Platform-Specific Issues

- **Instagram/TikTok deletion:** These platforms don't allow post deletion via API
- **LinkedIn org routing:** Double-check that the author target matches your account type (member vs. organization)
- **Text too long:** Verify text length against platform limits in the skill documentation

## Architecture

```
.claude-plugin/
  └─ plugin.json          # Plugin metadata
.mcp.json                 # MCP server configuration
assets/
  └─ icon.png             # 512×512 listing icon
skills/
  └─ aidelly-social/
     └─ SKILL.md          # Tool documentation & examples
```

## API Endpoints

The plugin connects to `https://app.aidelly.ai/api/mcp/public-api` (Aidelly's MCP HTTP endpoint).

**Authentication:** OAuth 2.1 (PKCE + dynamic client registration), discovered from the server's `.well-known/oauth-protected-resource` metadata. Tool calls without a token return `401` with a `WWW-Authenticate` challenge, which starts the sign-in.

**Tools exposed by the server:**

- `aidelly_create_post` – Instant post publish
- `aidelly_create_scheduled_post` – Schedule a post
- `aidelly_list_pending_approvals` – Check pending approvals
- Approving or rejecting posts happens in the Aidelly app; the hosted server does not expose approval actions
- `aidelly_get_analytics_summary` – Pull engagement metrics
- `aidelly_get_post` – Get post details
- `aidelly_upload_media` – Upload image/video
- [Full list of available tools](./skills/aidelly-social/SKILL.md)

## Publishing & Distribution

This repository is designed to be published to Claude Code plugin marketplaces. Follow the checklist below for each distribution channel.

### Distribution Channels

#### 1. Anthropic Claude Code Plugin Directory

**Status:** [Pending / In progress / Published]

**Submission checklist:**

- [ ] README.md complete with features, installation, setup
- [ ] .claude-plugin/plugin.json valid and descriptive
- [ ] .mcp.json correctly configured for production MCP endpoint
- [ ] Skills documentation comprehensive with examples
- [ ] All JSON files validate (no parse errors)
- [ ] Version bumped to match release (semver)
- [ ] LICENSE file included (if required)
- [ ] Documentation reviewed for accuracy

**How to submit:**

1. Contact Anthropic at plugins@anthropic.com with:
   - GitHub repository link
   - Brief description of plugin capabilities
   - API authentication requirements
2. Follow Anthropic's submission process
3. Monitor distribution portal for approval status

#### 2. Official MCP Registry (Smithery)

**Status:** [Pending / In progress / Published]

**Registry:** https://smithery.ai or https://registry.smithery.ai

**Submission checklist:**

- [ ] Repository public on GitHub
- [ ] .mcp.json follows MCP spec v1.0+
- [ ] Version in package.json matches .claude-plugin/plugin.json
- [ ] README includes authentication setup
- [ ] Endpoint URL is production-ready (https://)
- [ ] Rate limits documented
- [ ] Error handling documented
- [ ] Test credentials available for verification (optional)

**How to submit:**

1. Fork the MCP registry (https://github.com/smithery/smithery-registry)
2. Add entry in registries.json (or equivalent)
3. Include metadata: name, version, description, auth requirements, URL
4. Open pull request
5. Address review feedback

#### 3. Listed on

**Glama** – https://glama.ai/mcp/servers/Aidelly/claude-plugin

The badge at the top of this README reflects Glama's live quality score for
this server.

#### 4. Alternative MCP Registries

**mcp.so** – https://mcp.so
**PulseMCP** – https://pulsemcp.com

**Submission checklist (each):**

- [ ] Plugin discoverable via their search
- [ ] Correct metadata (name, description, author)
- [ ] Production MCP endpoint working
- [ ] Authentication method documented
- [ ] Contact/support information provided

**How to submit:**

- Each registry has its own submission process (check their websites)
- Generally requires GitHub repo + metadata + contact info

### Pre-Release Checklist

Before submitting to any marketplace:

```bash
# 1. Validate JSON files
jq . .claude-plugin/plugin.json
jq . .mcp.json

# 2. Verify the MCP endpoint answers (no token needed for initialize)
curl -s -X POST https://app.aidelly.ai/api/mcp/public-api \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"initialize","id":1}'

# 3. Verify the OAuth challenge on a tool call (expect HTTP 401 + WWW-Authenticate)
curl -s -D - -o /dev/null -X POST https://app.aidelly.ai/api/mcp/public-api \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"aidelly_list_workspaces","arguments":{}}}'

# 4. Review skill documentation
cat skills/aidelly-social/SKILL.md | wc -l  # Should be > 200 lines
```

### Release Notes

When bumping version in `.claude-plugin/plugin.json` and README:

```
## Version 0.2.2

- Uses the renamed tool IDs (for example `aidelly_create_content_automation`); the old IDs stay callable as hidden aliases
- The approval workflow approves in the Aidelly app, because the hosted server does not expose approval actions

## Version 0.2.1

- Adds `privacyPolicyUrl` (https://www.aidelly.ai/privacy-policy) to plugin.json

## Version 0.2.0

- `.mcp.json` declares `"type": "http"` so Claude Code loads the remote server
- Sign-in is OAuth only; no credentials are read from the user's machine
- Adds a 512×512 listing icon

## Version 0.1.0

**Initial release**
- Create, schedule, and publish posts across multiple platforms
- Approval workflow integration
- Analytics summary pull
- Media upload support
- 11 platforms supported
```

## Support

- **Issues:** https://github.com/Aidelly/claude-plugin/issues
- **Docs:** https://docs.aidelly.ai
- **Email:** support@aidelly.ai

## License

[License TBD – check Aidelly open-source policy]

## Maintainers

- Aidelly Team (@aidelly)

---

**Last updated:** 2026-10-01  
**Plugin version:** 0.2.2  
**MCP protocol version:** 2024-11-05+
