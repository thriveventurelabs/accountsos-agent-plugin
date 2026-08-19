# AccountsOS Agent Plugin

UK accounting for Claude Cowork, Claude Code and Grok Build. Track transactions, manage VAT, monitor HMRC deadlines, and categorise expenses through natural language. Verified for the **2026/27 tax year**.

One repo serves both ecosystems: `.claude-plugin/plugin.json` (Claude) and `.grok-plugin/plugin.json` (Grok Build) describe the same skills, commands and MCP connector.

Built on the AccountsOS MCP server. Pairs with Anthropic's official [knowledge-work-plugins/finance](https://github.com/anthropics/knowledge-work-plugins/tree/main/finance): that plugin gives you US-GAAP methodology (journal entries, reconciliation, close management); this one gives you UK regulatory truth.

---

## Install

### Via Claude (Cowork or Claude Code)

```bash
claude plugin install thriveventurelabs/accountsos-cowork-plugin
```

### Via Grok Build

Install `accountsos` from the built-in plugin marketplace (`xai-org/plugin-marketplace` catalog).

### Manual

```bash
cd ~/.claude/plugins
git clone https://github.com/thriveventurelabs/accountsos-cowork-plugin.git
```

## Configure

Set your AccountsOS API key as an environment variable:

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
| **expense-categories** | Expense / receipt / claim questions | What's deductible, mileage rates 45p/25p, home office (sole trader simplified £10/£18/£26), trivial benefits £50/£300, capital vs revenue |
| **tax-deadlines** | Deadline / penalty / filing questions | CT600 (12m + 9m1d), Confirmation Statement (£34/£62 fee, 14 days), accounts (9m), VAT (1m+7d), SA (31 Jan online), MTD ITSA quarterly, late penalties |

### 13 API Tools (via MCP)

Full read/write access to your accounting data. See [CONNECTORS.md](./CONNECTORS.md) for the complete tool reference.

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

- Claude Cowork, Claude Code or Grok Build with plugin support
- AccountsOS account ([sign up free](https://accounts-os.com/signup), Early Access pricing)
- API key with `read` + `write` scopes

## Network endpoints and credentials

Declared for plugin-marketplace security review:

- **The only network endpoint this plugin calls is `https://accounts-os.com/api/mcp`** (the AccountsOS hosted MCP server, HTTPS), configured in [`.mcp.json`](./.mcp.json).
- **The only credential it uses is `ACCOUNTSOS_API_KEY`**, read from your environment and sent as a Bearer token to that endpoint. You create and revoke the key yourself in AccountsOS Settings, scoped to `read` + `write` on your own company data.
- No lifecycle hooks, no shell execution, no postinstall scripts, no telemetry. Skills and commands are plain markdown.

## Privacy

Your AccountsOS data stays in your AccountsOS account (Supabase eu-west-2). The plugin sends queries to your AccountsOS API key and the agent reads the responses. No customer financial data is sent to the model provider in your context except the responses you receive.

## UK Specificity

Built for UK Limited Companies and sole traders. Knows HMRC rules, Companies House requirements, VAT schemes, MTD compliance, and 2026/27 tax year figures.

For US, AU, UAE, India: see roadmap on [accounts-os.com/roadmap](https://accounts-os.com).

---

## Links

- [AccountsOS](https://accounts-os.com)
- [API Documentation](https://accounts-os.com/skill.md)
- [Support](mailto:hello@accounts-os.com)
- [Anthropic finance plugin (companion)](https://github.com/anthropics/knowledge-work-plugins/tree/main/finance)

Built by [Thrive Venture Labs](https://thriveventurelabs.com)
