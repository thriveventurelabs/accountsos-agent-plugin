# AccountsOS Agent Plugin

AI-native accounting across **26 countries** for Claude Code, Claude Cowork, Grok Build and Cursor. Ask your live AccountsOS ledger about transactions, VAT, deadlines, expenses and invoices in plain English.

The tax skills bundled in this plugin are **UK-only for now**, verified for the **2026/27 tax year**. The UK is the proof jurisdiction: HMRC MTD VAT filing is live with official receipts. The other 25 countries run as jurisdiction playbooks inside AccountsOS itself, reachable through the MCP tools below.

One repo serves all three ecosystems: `.claude-plugin/plugin.json` (Claude), `.grok-plugin/plugin.json` (Grok Build) and `.cursor-plugin/plugin.json` (Cursor) describe the same skills, commands and MCP connector.

Built on the AccountsOS MCP server. Pairs with Anthropic's official [knowledge-work-plugins/finance](https://github.com/anthropics/knowledge-work-plugins/tree/main/finance): that plugin gives you US-GAAP methodology (journal entries, reconciliation, close management); this one gives you UK regulatory truth.

---

## Install

### Via Claude (Cowork or Claude Code)

```
/plugin marketplace add thriveventurelabs/accountsos-agent-plugin
/plugin install accountsos@accountsos
```

Or from the shell:

```bash
claude plugin marketplace add thriveventurelabs/accountsos-agent-plugin
claude plugin install accountsos@accountsos
```

### Via Grok Build

Install `accountsos` from the built-in plugin marketplace (`xai-org/plugin-marketplace` catalog).

### Via Cursor

Open **Cursor Settings → Plugins**, search for **AccountsOS**, click **Install**, then set your API key when prompted. Or run `/add-plugin accountsos` in chat.

### Manual

```bash
cd ~/.claude/plugins
git clone https://github.com/thriveventurelabs/accountsos-agent-plugin.git
```

## Configure

**Claude Code / Cowork** prompts for the key when you enable the plugin and stores it in your system keychain. Nothing to export.

**Cursor** prompts for it in the plugin's Configure panel.

**Grok Build**, or a manual install, reads it from the environment:

```bash
export ACCOUNTSOS_API_KEY="sk_live_..."
```

Get your API key from [accounts-os.com](https://accounts-os.com) under Settings → API Keys, or have your agent self-signup via the [agent endpoint](https://accounts-os.com/skill.md).

---

## What's Included

### 6 Slash Commands

| Command | What it does |
|---------|-------------|
| `/weekly-check` | Full financial review: balance, transactions, deadlines, VAT, invoices, DLA |
| `/log-expense` | Quick expense logging with auto-categorisation |
| `/vat-check` | Quarterly VAT position and filing checklist |
| `/deadlines` | Filing deadline overview with penalty warnings |
| `/invoices` | Outstanding and overdue invoices with chase suggestions |
| `/categorize` | Bulk review and categorise uncategorised transactions |

### 4 Skills (auto-fire)

| Skill | When it fires | Covers |
|-------|--------------|--------|
| **uk-accounting** | Director / CT / dividends / capital allowances queries | Corp Tax 2026/27 (19/25%), dividend allowance £500, salary vs dividend, DLA + S455, capital allowances (AIA £1m + Full Expensing), R&D merged scheme, BADR (10/14/18%) |
| **vat-rules** | VAT / MTD / registration questions | £90k registration threshold, £88k deregistration, schemes (Standard/Cash/Flat Rate/Annual), MTD VAT mandatory, MTD ITSA from April 2026 |
| **expense-categories** | Expense / receipt / claim questions | What's deductible, mileage rates 55p/25p from 6 April 2026, home office (sole trader simplified £10/£18/£26), trivial benefits £50/£300, capital vs revenue |
| **tax-deadlines** | Deadline / penalty / filing questions | CT600 (12m + 9m1d), Confirmation Statement (£34/£62 fee, 14 days), accounts (9m), VAT (1m+7d), SA (31 Jan online), MTD ITSA quarterly, late penalties |

### 86 API Tools (via MCP)

Full read/write access to your accounting data. The tool list is fetched live from AccountsOS at startup, so it tracks whatever the product exposes rather than a list frozen in this package. See [CONNECTORS.md](./CONNECTORS.md) for the reference.

**Read**: `get_transactions`, `get_balance`, `get_vat_summary`, `get_deadlines`, `get_invoices`, `get_dla_balance`, `list_categories`, `search_documents`

**Write**: `create_transaction`, `update_transaction`, `categorize_transaction`, `create_deadline`, `upload_document`

---

## What this plugin does that vanilla Claude doesn't

Vanilla Claude knows accounting concepts but **drifts** on UK specifics, it'll quote a 2024/25 dividend allowance, an old VAT threshold, or a wrong CT filing rule. This plugin pins Claude to **verified, sourced** UK rules, every fact citing gov.uk.

Concretely:
- Confirmation Statement fee: £34 online (NOT £13, raised May 2024)
- Dividend allowance: £500/year (NOT £1,000 or £2,000)
- Class 2 NI: abolished for self-employed from April 2024
- BADR rates: 18% from 6 April 2026 (was 14% 2025/26, was 10% before April 2025)
- Employer NI: 15% above £5,000 (rate AND threshold both changed April 2025)
- Trivial benefits: £50/item AND £300/year director cap
- Full Expensing is COMPANIES ONLY (not sole traders/partnerships)

---

## Requirements

- Claude Cowork, Claude Code, Grok Build or Cursor with plugin support
- AccountsOS account ([sign up free](https://accounts-os.com/signup), Early Access pricing)
- API key with `read` + `write` scopes

## Network endpoints and credentials

Declared for plugin-marketplace security review:

- **The only network endpoint this plugin calls is `https://accounts-os.com/api/mcp`** (the AccountsOS API, HTTPS). It is reached through the published stdio MCP server [`@thriveventurelabs/accountsos-mcp`](https://www.npmjs.com/package/@thriveventurelabs/accountsos-mcp), launched with `npx` and configured in [`.mcp.json`](./.mcp.json) (Grok Build, Cursor) and [`.claude-plugin/mcp.json`](./.claude-plugin/mcp.json) (Claude). Source: https://github.com/thriveventurelabs/accountsos-mcp-server
- **The only credential it uses is `ACCOUNTSOS_API_KEY`**, read from your environment and sent as a Bearer token to that endpoint. You create and revoke the key yourself in AccountsOS Settings, scoped to `read` + `write` on your own company data.
- No lifecycle hooks, no postinstall scripts, no telemetry. Skills and commands are plain markdown. The one process the plugin starts is that npm MCP server.

## Privacy

Your AccountsOS data stays in your AccountsOS account (Supabase eu-west-2). The plugin sends queries to your AccountsOS API key and the agent reads the responses. No customer financial data is sent to the model provider in your context except the responses you receive.

## Countries

AccountsOS runs jurisdiction playbooks for 26 countries: GB, IE, AU, US, AE, BG, HK, TR, IM, GG, DE, DK, SG, NL, SE, IN, CH, CA, AT, NO, NZ, SI, PA, CY, MT, BE. Bookkeeping, invoicing, expenses, reporting and multi-currency work everywhere; the MCP tools in this plugin read and write whichever ledger your API key belongs to.

The **tax skills shipped in this plugin are GB-only**. They pin the agent to HMRC and Companies House rules for the 2026/27 tax year: corporation tax, VAT schemes, MTD, capital allowances, BADR, filing deadlines and penalties. The UK is the proof jurisdiction, with live HMRC MTD VAT filings returning official receipts. Filing coverage in the other 25 countries is a playbook in the product, not a skill in this plugin, so do not read the country count as universal filing support.

---

## Links

- [AccountsOS](https://accounts-os.com)
- [API Documentation](https://accounts-os.com/skill.md)
- [Support](mailto:hello@accounts-os.com)
- [Anthropic finance plugin (companion)](https://github.com/anthropics/knowledge-work-plugins/tree/main/finance)

Built by [Thrive Venture Labs](https://thriveventurelabs.com)
