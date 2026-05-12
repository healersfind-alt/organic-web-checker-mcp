# Organic Web Checker — MCP Server

> Cross-reference certified organic operation websites against live USDA Organic Integrity Database (OID) certificate data. Identifies organic product representations that may not be on the certificate of organic operation.

**Status:** Live · Remote HTTP MCP server · JSON-RPC 2.0 · API key required

- **Endpoint:** `https://www.organicwebchecker.com/mcp`
- **Website:** [organicwebchecker.com](https://www.organicwebchecker.com)
- **Protocol:** MCP `2024-11-05`

---

## What it does

Organic Web Checker (OWC) compares products marketed as organic on a certified operation's public website against the operation's live USDA OID certificate. It surfaces:

- ✅ **Verified** — product representation found on the cert
- 🔴 **Flagged** — organic representation with no matching cert item
- 🟡 **Caution** — close name variation, processing-form question, or grouped-scope match requiring review by the certifying agent

Built for organic certifying agents, compliance consultants, and certified operations doing self-review. **Decision-support tool — not a compliance determination.** Manual review of flagged items is required.

---

## Tools

### `check_organic_compliance`

Runs a full website-vs-OID compliance check and returns a structured report.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `operation_name` | string | yes | Operation name exactly as listed in USDA OID |
| `website_url` | string | yes | Full URL of the operation's website |
| `force_fresh` | boolean | no | Bypass 24-hour OID cache and pull live from USDA (default `false`) |

**Returns:** structured JSON report with `verified`, `flagged`, `caution`, `marketing` lists, cert metadata, and item-level URLs.

**Cost:** 1 credit per successful run.

### `get_oid_certificate`

Fetches an operation's current USDA OID certificate without running a website check.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `operation_name` | string | yes | Operation name as listed in USDA OID |

**Returns:** certified products, certifier, status, location, scopes.

---

## Connect

### Claude Desktop / Cursor / other MCP clients

```json
{
  "mcpServers": {
    "organic-web-checker": {
      "url": "https://www.organicwebchecker.com/mcp",
      "headers": {
        "X-API-Key": "owc_live_..."
      }
    }
  }
}
```

### Raw JSON-RPC

```bash
curl -X POST https://www.organicwebchecker.com/mcp \
  -H "Content-Type: application/json" \
  -H "X-API-Key: owc_live_..." \
  -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "tools/call",
    "params": {
      "name": "check_organic_compliance",
      "arguments": {
        "operation_name": "Example Organic Farm LLC",
        "website_url": "https://example.com"
      }
    }
  }'
```

The server implements `initialize`, `tools/list`, and `tools/call`. Tools are discovered automatically by MCP-compatible clients on connect.

---

## Get an API key

1. Create an account at [organicwebchecker.com](https://www.organicwebchecker.com)
2. Visit [organicwebchecker.com/api](https://www.organicwebchecker.com/api) to generate a key (prefix `owc_live_`)
3. Buy credits at [organicwebchecker.com/pricing](https://www.organicwebchecker.com/pricing) (or use a promo code if you have one)

Each successful `check_organic_compliance` call deducts 1 credit. `get_oid_certificate` is free of credit cost. Rate limit: 60 checks/hour per key.

---

## Regulatory context

OWC checks public-facing organic representations against publicly available OID certificate information. It acknowledges:

- **Retail exemption** — 7 CFR §205.101(a)(2)
- **Handler/OSP scope** — 7 CFR §205.201; detailed product coverage held by the certifying agent
- **SOE rule** (March 19, 2024) — brokers, traders, importers, exporters now require handler certification

Results are decision-support only. Final compliance determinations are made by certifying agents.

---

## License

This repository (server metadata and documentation) is licensed under the **MIT License** — see [LICENSE](./LICENSE).

The Organic Web Checker service itself is proprietary software. Method protected by U.S. provisional patent application No. 64053105.

---

## Operator

**Organic Web Checker LLC** (Florida)
Inventor / Founder: Christian Strickland
Contact: [hello@organicwebchecker.com](mailto:hello@organicwebchecker.com)
