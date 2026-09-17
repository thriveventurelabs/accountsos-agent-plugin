# Changelog

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
