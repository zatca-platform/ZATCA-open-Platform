# ZATCA Open Platform — Mono Repo Index

## Repositories

| Repo | Status | Path | Description |
|------|--------|------|-------------|
| **zatca-open-specification** | ✅ Done | `../zatca-open-specification/` | Canonical YAML specification + PHP parser (Composer package) |
| **zatca-core-engine** | ✅ Done | `../zatca-core-engine/` | Node.js REST microservice — reads YAML spec, validates, signs, generates QR, submits to FATOORA |
| **zatca-emulator** | ✅ Done | `../zatca-emulator/` | Node.js emulator that simulates ZATCA government API (sandbox) |
| **zatca-laravel-sdk** | ✅ Done | `../zatca-laravel-sdk/` | Thin Laravel HTTP client to core-engine (4 source files, zero business logic) |
| **zatca-compliance-test-suite** | ✅ Done | `../zatca-compliance-test-suite/` | 101 deterministic cross-language compliance tests |
| **zatca-django-sdk** | ⏳ Pending | — | Django SDK (thin HTTP client to core-engine) |
| **zatca-dotnet-sdk** | ⏳ Pending | — | .NET SDK (thin HTTP client to core-engine) |
| **zatca-node-sdk** | ⏳ Pending | — | Node.js SDK (thin HTTP client to core-engine) |

## Architecture

```
zatca-open-specification (YAML — single source of truth)
         │
         ▼
zatca-core-engine (Node.js REST microservice, reads YAML at startup)
         │
         ├── POST /api/v1/validate
         ├── POST /api/v1/sign
         ├── POST /api/v1/qr
         ├── POST /api/v1/process          (Phase 1: validate → sign → QR)
         ├── POST /api/v1/full-process     (Phase 1 + Phase 2: validate → sign → QR → submit)
         ├── POST /api/v1/submit/compliance
         ├── POST /api/v1/submit/clearance
         ├── POST /api/v1/submit/reporting
         ├── POST /api/v1/submit/production-csid
         ├── GET  /api/v1/spec/rules
         ├── GET  /api/v1/spec/errors
         └── GET  /api/v1/spec/contracts
                 │
         ┌───────┴───────┐
         ▼               ▼
zatca-emulator     ZATCA government API
(sandbox:8080)     (production)
         │
         ▼
  per-language SDKs (thin HTTP clients, ~20–30 lines each)
  - zatca-laravel-sdk
  - zatca-django-sdk (planned)
  - zatca-dotnet-sdk (planned)
  - zatca-node-sdk (planned)
```

## Key Principles

- **Zero business logic in SDKs** — SDKs are thin HTTP clients that call core-engine REST API
- **Core engine is language-agnostic** — any language can call it via HTTP
- **Spec is the source of truth** — YAML files, not hardcoded rules
- **Deterministic testing** — 101 test cases validated across all implementations

## Ports

| Service | Port |
|---------|------|
| zatca-core-engine | 3001 |
| zatca-emulator | 8080 |
