# Architecture: Sovereign Venture Underwriting

## Overview

**Package ID:** `PKG-017`  
**Domain:** Fintech & Capital Underwriting  
**Microservice Port:** `8795`  
**n8n Webhook Path:** `venture-underwriting-trigger`  
**GitHub:** [BlackFoxgamingstudio/venture-underwriting](https://github.com/BlackFoxgamingstudio/venture-underwriting)

AI-powered venture capital underwriting engine: startup scoring, due diligence automation, financial model analysis, cap table modeling, and term sheet generation.

---

## System Architecture

```
                     ┌──────────────────────────────────┐
                     │       Sovereign Venture Underwriting│
                     │       Port: 8795            │
                     ├──────────────┬───────────────────┤
   n8n Webhook ────▶ │  REST API    │   Core Engine     │
   HTTP POST         │  /api/v1/*   │   Dispatcher      │
                     └──────┬───────┴────────┬──────────┘
                            │                │
              ┌─────────────▼────────────────▼─────────┐
              │          Component Layer                 │
              │  StartupScorer   | DueDiligenceEng | FinancialMod  │
              └────────────────────────┬────────────────┘
                                       │
              ┌────────────────────────▼────────────────┐
              │      n8n Central Event Bus (:5678)       │
              └─────────────────────────────────────────┘
```

## Core Components

### `StartupScorer`
Handles all startupscorer operations. Exposes async methods callable from the core dispatcher.

### `DueDiligenceEngine`
Handles all duediligence operations. Exposes async methods callable from the core dispatcher.

### `FinancialModelAnalyzer`
Handles all financialmodelanalyzer operations. Exposes async methods callable from the core dispatcher.

### `CapTableModeler`
Handles all captablemodeler operations. Exposes async methods callable from the core dispatcher.

### `TermSheetGenerator`
Handles all termsheetgenerator operations. Exposes async methods callable from the core dispatcher.

---

## API Contract

All interactions follow the SBB standard envelope:

```http
POST /api/v1/execute
Content-Type: application/json
X-SBB-API-Key: <api-key>

{
  "action": "<operation>",
  "payload": {},
  "trace_id": "optional-uuid"
}
```

**Success Response (HTTP 200):**
```json
{
  "status": "success",
  "data": {},
  "trace_id": "...",
  "timestamp": "2025-01-01T00:00:00Z"
}
```

**Health Check:**
```http
GET /health
→ {"status": "healthy", "service": "sovereign-venture-underwriting", "port": 8795}
```

## Integration Matrix

| System | Protocol | Direction | Purpose |
|--------|----------|-----------|---------|
| n8n Event Bus (:5678) | HTTP POST | Outbound | Event forwarding |
| n8n Webhook | HTTP POST | Inbound | Trigger execution |
| SBB Codebase Vault (:8766) | HTTP | Outbound | Code analysis |
| SBB Patterns Bible (:8794) | HTTP | Outbound | Standards validation |
| External APIs | HTTPS | Outbound | Domain-specific data |

## Deployment Architecture

```yaml
# docker-compose excerpt
sovereign-venture-underwriting:
  image: sovereign-venture-underwriting:latest
  ports: ["8795:8795"]
  healthcheck:
    test: curl -f http://localhost:8795/health
    interval: 30s
```

## Security Model

| Control | Implementation |
|---------|---------------|
| Authentication | `X-SBB-API-Key` header (env: `SBB_API_KEY`) |
| Rate Limiting | 100 req/min per client IP |
| Input Validation | Pydantic models (strict mode) |
| Container Security | Non-root user (`appuser:1001`) |
| Secrets | Environment variables only (never hardcoded) |
| TLS | Terminate at reverse proxy (nginx/caddy) |

## Tags
`fintech`, `vc`, `underwriting`, `ai`
