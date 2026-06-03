# BuildCalc API — MCP Server

> Vertical construction data API for AI agents and developers. Hosted MCP server providing ~105 tools across 5 federated verticals, grounded in U.S. federal sources + ICC-ES Evaluation Service Reports + manufacturer spec sheets.

[![PyPI](https://img.shields.io/pypi/v/buildcalcapi?label=PyPI%20%E2%80%94%20buildcalcapi)](https://pypi.org/project/buildcalcapi/) [![npm](https://img.shields.io/npm/v/@buildcalcapi/sdk?label=npm%20%E2%80%94%20%40buildcalcapi%2Fsdk)](https://www.npmjs.com/package/@buildcalcapi/sdk)

**Production endpoint** (Streamable HTTP, MCP spec 2025-03-26): `https://api.buildcalcapi.dev/mcp`

**Status**: production, `v0.4.1` (June 2026). Free tier 1,000 req/month at https://buildcalcapi.dev/signup.

This repository hosts the public manifests + integration docs. The MCP server itself is operated by NF Nation LLC; backend code is proprietary. SDKs (Python, TypeScript) are MIT-licensed and auto-generated from the OpenAPI spec on every release.

---

## Quick connect

### Claude Desktop (`claude_desktop_config.json`)

```json
{
  "mcpServers": {
    "buildcalc-api": {
      "url": "https://api.buildcalcapi.dev/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_BCAPI_KEY"
      }
    }
  }
}
```

### Cursor / VS Code with MCP

Same `url` + `Authorization: Bearer YOUR_BCAPI_KEY` header pattern in your client's MCP server config.

### Smithery

Listed at <https://smithery.ai/servers/buildcalcapi/server>. Smithery's hosted proxy handles the API key config UI for you.

### Anthropic Official MCP Registry

Listed at <https://registry.modelcontextprotocol.io/v0.1/servers?search=buildcalcapi> as `dev.buildcalcapi/server`. Domain-namespaced via DNS TXT proof at `buildcalcapi.dev` apex.

### Programmatic access (SDKs)

- **Python**: `pip install buildcalcapi` ([PyPI](https://pypi.org/project/buildcalcapi/))
- **TypeScript**: `npm install @buildcalcapi/sdk` ([npm](https://www.npmjs.com/package/@buildcalcapi/sdk))

Both SDKs are auto-regenerated from `docs/openapi.json` on every release. Source-of-truth OpenAPI spec lives at <https://docs.buildcalcapi.dev/openapi.json>.

---

## What the server provides

5 federated verticals, ~105 MCP tools total. Tag-filtered to `calc:*` + `data:*` — admin / signup / billing endpoints intentionally excluded.

### `calc:*` — 81 building-code-grounded calculators (16 categories)

Concrete, framing, lumber, drywall, roofing, paint, flooring, HVAC sizing, insulation, masonry, electrical (NEC), plumbing (UPC/IPC), doors/windows (IECC), gutters, stairs (IRC), site, misc. Pure-Python public-domain math. Inputs validated by Pydantic; results include code references where applicable.

### `data:codes:*` — 937 IRC/IBC/NEC/IECC/IPC sections with full-text search

Dual-edition 2021/2024 metadata, calculator cross-references, per-jurisdiction adoption matrix.

### `data:costs:*` — Federal cost data

- BLS PPI materials (288 series across 15 construction categories)
- BLS OEWS + QCEW labor wages (11 trades at MSA/county/ZIP granularity, 4-level confidence taxonomy)
- Census BPS permits (7,800+ jurisdictions)

### `data:products:*` — 43,400+ certified product SKUs (10 categories)

HVAC · plumbing · electrical · windows · doors · insulation · roofing · lumber · drywall · hardware. Sources: ENERGY STAR Certified product datasets (HVAC, electrical, insulation, storm-windows, roofing), NFRC CPD, EPA WaterSense, ICC-ES Evaluation Service Reports (Simpson Strong-Tie ESR-2552/3096/2236), manufacturer PDFs (Tier-A regex extractors). JSONB spec-filter envelope (`?filter[specs.seer2.gte]=16`, etc.).

**96% of catalog is `confidence='certified'`** from federal/regulatory/third-party-independent sources (per ADR-0015 confidence taxonomy: `certified` > `mfr_published` > `ollama_extracted` > `legacy_or_stale`).

### `data:benchmarks:*` — 15 standardized project benchmarks × 5 geographies

Component-derived cost + duration ranges. Federally-grounded methodology shipping 2026-07-15 (v0.4.0).

---

## Operational posture

- **Rate limits**: IETF draft-04 — `RateLimit-Limit` / `RateLimit-Remaining` / `RateLimit-Reset` headers on every response. Per-tier defaults: Free 1k/mo + 10 rpm; Starter 50k/mo; Growth 500k/mo; Enterprise unlimited.
- **Errors**: RFC 7807 problem+json with `request_id` header for support. Schema at <https://docs.buildcalcapi.dev/docs/errors>.
- **Auth**: HMAC-SHA-256 API keys via `Authorization: Bearer bcapi_<key>`. Free-tier signup at <https://buildcalcapi.dev/signup>.
- **Backups**: daily DB snapshots to Cloudflare R2, monthly restore drills, RPO ≤24h.
- **Status**: <https://status.buildcalcapi.dev>.

---

## Pricing

| Tier | Price | Requests / month |
|------|-------|------------------|
| Free | $0 | 1,000 |
| Starter | $49 / mo | 50,000 |
| Growth | $249 / mo | 500,000 |
| Enterprise | from $1,500 / mo | Unlimited + SLA + custom support |

Stripe metered billing. Card on file required (anti-abuse, not charged on Free).

---

## Files in this repo

- [`smithery.yaml`](./smithery.yaml) — Smithery manifest (configSchema for the API key, HTTP transport pointing at the production endpoint)
- [`server.json`](./server.json) — Anthropic Official MCP Registry manifest (Streamable HTTP transport)
- [`.well-known/mcp.json`](./.well-known/mcp.json) — proposed MCP server discovery file (also served at <https://buildcalcapi.dev/.well-known/mcp.json>)

## Contact

- Support: support@buildcalcapi.dev
- Partnerships / acquisition inquiries: partners@buildcalcapi.dev
- DMCA: dmca@buildcalcapi.dev
- Status / uptime: <https://status.buildcalcapi.dev>

## License

This repo (manifests + docs): MIT. The MCP service itself is proprietary, operated by NF Nation LLC.
