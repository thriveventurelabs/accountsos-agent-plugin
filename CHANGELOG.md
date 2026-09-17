# Changelog

## 1.3.4 - 2026-09-17

- **Pinned the MCP server to `@thriveventurelabs/accountsos-mcp@0.3.1`.** `npx -y <pkg>` does not reliably fetch the latest version: npx caches by package spec, so a machine that has run the plugin once keeps running whatever build it first downloaded, indefinitely, even after `npm cache clean --force`. Verified: an unpinned spec kept serving a months-old build reporting version 0.1.0, while the pinned spec fetched 0.3.1. Pinning makes the version deterministic and makes every bump cache-busting.
- **That stale build was serving 10 tools instead of 86.** AccountsOS exposes 86 MCP tools; the package asks the API for its live catalogue at startup, but the published tarball had been built before several of those improvements and fell back to a 10-tool built-in list. `get_invoices` and `get_dla_balance`, which `/invoices` and `/weekly-check` depend on, were among the missing.
- Server-side fixes in `@thriveventurelabs/accountsos-mcp@0.3.1`: the catalogue fetch retries once with a 10s timeout, a failed fetch now logs a loud warning naming how many tools you are actually getting instead of degrading silently, and the reported version is read from `package.json` rather than a literal that had already drifted twice.
- README: the tool count said 13 and named three tools the package did not ship. It is 86, fetched live.

## 1.3.3 - 2026-09-17

- **The MCP connector could never complete a handshake in any ecosystem.** `.mcp.json` declared `type: "http"` against `https://accounts-os.com/api/mcp`, but that endpoint is not an MCP server. It answers exactly three JSON-RPC discovery methods (`initialize`, `tools/list`, `resources/list`) and expects every other call as a custom REST body (`{"type": "tool", "name", "arguments"}`), so a client got a clean `initialize` and then a `400 Request body must include type: "tool" or "resource"` on the very next message. The sibling endpoint `/api/mcp/plugin` does speak MCP, but it is OAuth-only and explicitly rejects `sk_` API keys, so it was never an option either.
- Both configs now run the published stdio server, `@thriveventurelabs/accountsos-mcp`, which is what translates MCP to that REST API and is the transport Claude Desktop has always used. `.claude-plugin/mcp.json` takes the key from `userConfig`; the root `.mcp.json` takes it from `ACCOUNTSOS_API_KEY` for Grok Build and Cursor.

## 1.3.2 - 2026-09-17

- Claude Code now prompts for the API key when you enable the plugin (`userConfig.accountsos_api_key`, sensitive, stored in the system keychain) instead of requiring a manual `export`. Claude Code has no `${ACCOUNTSOS_API_KEY}` in scope, so the shared `.mcp.json` resolved to a literal and every Claude install failed its only connector with a 401. The Claude connector now reads `.claude-plugin/mcp.json` and interpolates `${user_config.accountsos_api_key}`. The root `.mcp.json` is untouched, so Grok Build and Cursor keep reading the environment variable.

## 1.3.1 - 2026-09-17

- **The plugin is installable in Claude Code for the first time.** Added `.claude-plugin/marketplace.json`, which Claude Code requires to resolve a plugin from a git repo. Without it `/plugin marketplace add` had nothing to read.
- Fixed `.claude-plugin/plugin.json` against the Claude Code plugin schema (`claude plugin validate` reported 12 errors before, 0 now): `name` is kebab-case `accountsos` with `displayName` "AccountsOS", `author` is an object, `skills` and `commands` point at their directories, the MCP server is declared through `mcpServers` (the old `connectors` field was unknown and ignored, so the connector never loaded), and `tags` moved to the marketplace entry as `keywords`.
- Descriptions across all three manifests now lead with the 26 countries AccountsOS runs, and state plainly that the bundled tax skills are GB-only, with the UK as the proof jurisdiction for live HMRC MTD VAT filing.
- README: corrected the Claude install commands, replaced the "UK Specificity" section with the honest country position.

## 1.3.0 - 2026-08-31

- Cursor support: `.cursor-plugin/plugin.json` manifest with a typed `ACCOUNTSOS_API_KEY` variable, so Cursor's settings UI prompts for the key instead of a manual export.
- Declared the MCP server as `type: "http"` (hosted endpoint at `https://accounts-os.com/api/mcp`, bearer auth).
- Added `assets/logo.png` and this changelog.

## 1.2.0 - 2026-08-19

- Grok Build support: `.grok-plugin/plugin.json` manifest describing the same skills, commands, and MCP connector.
- Repo renamed to `accountsos-agent-plugin` (ecosystem-neutral).

## 1.1.0 - 2026-04-24

- Initial release for Claude Cowork and Claude Code.
- 4 auto-firing UK tax skills (uk-accounting, vat-rules, expense-categories, tax-deadlines), verified for the 2026/27 tax year.
- 6 slash commands (weekly-check, log-expense, vat-check, deadlines, invoices, categorize).
- Hosted MCP connector to the AccountsOS API.
