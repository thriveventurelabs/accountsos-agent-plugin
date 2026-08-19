# Anthropic Plugin Directory Submission

> Internal notes — submission checklist + metadata for the official Claude plugin directory.

## Status

- [x] `plugin.json` with name, description, version, author, license, repo, homepage, tags
- [x] `LICENSE` (MIT)
- [x] `README.md` with install + configure + what's included
- [x] 4 skills with `SKILL.md` frontmatter (name, description, triggers, last_reviewed)
- [x] 6 commands with markdown bodies
- [x] `CONNECTORS.md` documenting MCP tools
- [x] `.mcp.json` configuration file
- [x] Tax facts verified for 2026/27 (last_reviewed: 2026-04-24)
- [ ] Repo published as standalone at `github.com/thriveventurelabs/accountsos-cowork-plugin`
- [ ] Submitted to Anthropic plugin directory

## How to publish + submit

1. **Push to standalone repo**
   ```bash
   cd accountsos-cowork-plugin
   git init
   git add .
   git commit -m "Initial release v1.1.0 (UK tax 2026/27)"
   git remote add origin git@github.com:thriveventurelabs/accountsos-cowork-plugin.git
   git push -u origin main
   git tag v1.1.0
   git push origin v1.1.0
   ```

2. **Submit to Anthropic directory**
   Follow the process at <https://docs.claude.com/en/docs/claude-code/plugins> — open a PR or directory submission.
   The plugin should appear under finance/accounting tags as the UK counterpart to `knowledge-work-plugins/finance`.

3. **Cross-link**
   - Add a link from accounts-os.com → "Use AccountsOS in Claude" → install command
   - Mention in /for-agents page
   - Add to LinkedIn post queue: announce the official plugin

## Versioning policy

- **Patch** (1.1.x): typo fixes, link updates
- **Minor** (1.x.0): new skills, refreshed tax year (annual at start of new tax year — refresh by 30 April)
- **Major** (x.0.0): structural changes (e.g. moving to multi-country, breaking command names)

Each release bumps `last_reviewed` in plugin.json AND in every SKILL.md frontmatter.

## Annual refresh checklist (every April)

When a new tax year starts (6 April):
1. Update `tax_year` in plugin.json
2. Refresh all `last_reviewed` dates
3. Re-verify each rate/threshold against gov.uk source URL in each SKILL.md
4. Bump version (1.x+1.0) and re-publish
5. Mirror changes from `lib/ai/tax-rules.ts` (the canonical source)

## What's the relationship to AccountsOS proper?

| Layer | AccountsOS app | This plugin |
|---|---|---|
| Live company data | ✅ database | via MCP |
| Tax facts | `lib/ai/tax-rules.ts` (canonical) | mirrored in SKILL.md files |
| Long-form guidance | `lib/skills/` (18 skills) | 4 condensed skills here |
| AI persona (Finn) | `lib/ai/prompts.ts` | n/a — Claude is the persona |

The plugin is a **distribution channel**, not a separate product. Its facts must stay in sync with `tax-rules.ts`. Drift is a bug.
