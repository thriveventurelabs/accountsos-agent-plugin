# AccountsOS Connector

## Connection: AccountsOS API

**Protocol:** MCP (Model Context Protocol) over HTTPS
**Base URL:** `https://accounts-os.com/api/mcp`
**Auth:** Bearer token via `ACCOUNTSOS_API_KEY` environment variable

### Setup

1. Get your API key from [accounts-os.com](https://accounts-os.com) dashboard
2. Set the environment variable: `ACCOUNTSOS_API_KEY=sk_live_...`

### Alternative: npm package (stdio)

If you prefer a local MCP server via stdio:

```bash
npm install -g @thriveventurelabs/accountsos-mcp
```

Then configure in `.mcp.json`:

```json
{
  "mcpServers": {
    "accountsos": {
      "command": "accountsos-mcp",
      "env": {
        "ACCOUNTSOS_API_KEY": "sk_live_..."
      }
    }
  }
}
```

### Available Tools (13)

| Tool | Scope | Description |
|------|-------|-------------|
| `get_transactions` | read | List and filter transactions by date, direction, category |
| `get_balance` | read | Current account balance with pending amounts |
| `get_vat_summary` | read | VAT position for a quarter (9-box calculation) |
| `get_deadlines` | read | Upcoming filing deadlines with days remaining |
| `get_dla_balance` | read | Director's Loan Account balance and S455 warnings |
| `get_invoices` | read | Outstanding invoices with summary stats |
| `list_categories` | read | Available categories by type (income/expense/asset/liability/equity) |
| `search_documents` | read | Find stored receipts, invoices, contracts |
| `create_transaction` | write | Record income or expense |
| `update_transaction` | write | Modify category, notes, or other fields |
| `categorize_transaction` | write | Get and apply AI category suggestion |
| `create_deadline` | write | Create filing or tax deadline reminders |
| `upload_document` | write | Store receipts and invoices |

### Available Resources (4)

| URI | Description |
|-----|-------------|
| `accountsos://company` | Company name, registration, entity type |
| `accountsos://transactions` | Recent transactions (last 50) |
| `accountsos://documents` | Stored receipts and invoices |
| `accountsos://deadlines` | Upcoming filing deadlines |
