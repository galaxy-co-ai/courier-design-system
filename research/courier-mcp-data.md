# Courier MCP Data

> Captured: 2026-03-21

## Connection Status

**Courier MCP was not connected during this research session.**

The workspace `.mcp.json` only contains `cbos-knowledge` (CB Media OS knowledge pipeline). Courier's MCP server (`<base_url>/mcp/sse`) was not configured.

## Known MCP Capabilities (from documentation)

Courier exposes an MCP server via SSE transport at `<base_url>/mcp/sse`. Based on the product context:

### Expected Tool Surface
- **Model management** — list workbench models, list library models
- **Analytics** — pull usage/performance data
- **Configuration** — model deployment settings, API key management
- **Documentation** — product docs, API reference

### API Styles (REST, not MCP)
Courier offers three REST API styles:
1. **Courier Custom** — native API format
2. **OpenAI-compatible completions** — drop-in replacement for `/v1/chat/completions`
3. **OpenAI-compatible responses** — drop-in replacement for `/v1/responses`

## Recommendation

When Jackson applies this design system, he should:
1. Connect Courier MCP in his `.mcp.json` with his API key
2. Re-run the brand audit prompt (`00-brand-audit.prompt.md`) with MCP connected
3. The MCP data will enrich model card designs, analytics views, and API documentation patterns

## Data to Capture on Next Connection
- Full model inventory (names, sizes, quantization levels, status)
- Analytics schema (what metrics are tracked, time ranges available)
- Documentation structure (what docs are exposed via MCP)
- Any configuration or settings endpoints
