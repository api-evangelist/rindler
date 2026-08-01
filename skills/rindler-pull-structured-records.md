---
name: Pull structured records from a mapped site with Rindler
description: >-
  Use the Rindler hosted MCP server to open a session on a pre-onboarded
  website, dispatch a mapped action, and return structured JSON records instead
  of scraping raw HTML.
api: mcp/rindler-mcp.yml
transport: http
endpoint: https://mcp.rindler.ai
operations:
  - start_session
  - dispatch_action
  - extract_content
  - close_session
generated: '2026-07-21'
method: generated
source: https://rindler.ai/docs/quickstart
---

# Pull structured records from a mapped site with Rindler

Rindler is a hosted MCP server (`https://mcp.rindler.ai`) that turns a
pre-onboarded website into deterministic, typed tools. Use it instead of DOM
scraping when the target site is in Rindler's catalog, is blocked to direct
fetches (403 / JS-only shell / bot challenge), or is behind a login.

## Prerequisites

1. Add the MCP server to your runtime (Claude Code writes `.mcp.json`):
   ```sh
   curl https://rindler.ai/install | sh
   ```
   or add the config entry manually:
   ```json
   { "mcpServers": { "rindler": { "type": "http", "url": "https://mcp.rindler.ai" } } }
   ```
   Restart the runtime afterward.
2. Authenticate on first tool call: Rindler returns an OAuth 2.0 PKCE
   authorization link; open it in the browser and approve. No API key. Tokens
   refresh automatically.

## Steps

1. **`start_session`** — open a session against the target site URL. If the site
   is not in Rindler's catalog you get an `unsupported_site` error; stop and do
   not fall back to raw HTML.
2. **`dispatch_action`** — send the mapped semantic action (e.g. `search`,
   `filter`, `add_to_cart`, a stage move, a statement download). The action set
   is site-specific and typed.
3. **`extract_content`** — read the result as structured JSON records. (Use
   `extract_document` to read a linked PDF anonymously.)
4. **`close_session`** — end the session.

## Rules

- Only mapped sites work; treat `unsupported_site` as terminal for that URL.
- Responses are deterministic and structured — do not re-parse them as HTML.
- Every action is executed on the user's explicit instruction; Rindler reports
  what the portal confirmed and never makes the approval/payment/hiring decision
  itself.
- Scopes follow `site:<site>:<capability>` (`read` | `write` | `schema`).
