# ZATCA Open Platform — Project Map

## Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                   zatca-open-specification                       │
│              YAML — Single Source of Truth (20 files)            │
│  models/  rules/  security/  lifecycle/  contracts/  errors/     │
└──────────────────────────┬───────────────────────────────────────┘
                           │ reads at startup
                           ▼
┌──────────────────────────────────────────────────────────────────┐
│                     zatca-core-engine                            │
│              Node.js Express REST microservice                   │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        │
│  │ Validation│  │  Crypto  │  │ Pipeline │  │Client to │        │
│  │  Engine   │  │ Signature│  │ Validate→│  │ Emulator │        │
│  │ (48 rules)│  │  + QR    │  │ Sign→QR  │  │ / ZATCA  │        │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘        │
│  Endpoints: /health /api/v1/{validate,sign,qr,process,          │
│  full-process,submit/compliance,submit/clearance,               │
│  submit/reporting,submit/production-csid,spec/*}                │
└──────────┬──────────────────────────────────────────────────────┘
           │ HTTP (port 3001)
           ▼
┌──────────────────────┐     ┌──────────────────────────┐
│  zatca-emulator      │     │  ZATCA Government API    │
│  (sandbox :8080)     │     │  (production)            │
│  POST /compliance    │     │  POST /compliance        │
│  POST /production/csids│   │  POST /production/csids  │
│  POST /clearance/inv.│     │  POST /clearance/invoices│
│  POST /reporting/inv.│     │  POST /reporting/invoices│
│  GET /certificates   │     │  GET /certificates       │
└──────────────────────┘     └──────────────────────────┘
           │
           ▼
┌──────────────────────────────────────────────────────────────────┐
│                  Per-Language Thin SDKs                          │
│  ┌─────────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐     │
│  │ Laravel SDK │  │ Django   │  │ .NET     │  │ Node.js  │     │
│  │ (✅ Done)   │  │(⏳ Pending)│  │(⏳ Pending)│  │(⏳ Pending)│  │
│  │ 4 source    │  │          │  │          │  │          │     │
│  │ files,      │  │          │  │          │  │          │     │
│  │ cURL client │  │          │  │          │  │          │     │
│  └─────────────┘  └──────────┘  └──────────┘  └──────────┘     │
└──────────────────────────────────────────────────────────────────┘

                        zatca-compliance-test-suite
                 101 test cases (YAML) + expected results (JSON)
```

## TECH_STACK

| Component | Technology | Version |
|-----------|-----------|---------|
| **Spec Format** | YAML | — |
| **Invoice Schema** | UBL 2.1 | 2.1 |
| **Signature** | XAdES EPES | v1.3.2 |
| **Encryption** | ECDSA secp256k1 + SHA-256 | — |
| **QR Encoding** | TLV Base64 (GS1-compatible) | — |
| **ZATCA API** | FATOORA API | V2 (2024+) |
| **Core Engine** | Node.js + Express | 22+ |
| **Emulator** | Node.js + Express | 22+ |
| **Reference SDK** | Laravel (`zatca-platform/zatca-laravel-sdk`) | v1.0 |
| **Target SDKs** | Laravel ✅, Django ⏳, .NET ⏳, Node.js ⏳ |
| **Certificate** | X.509 v3 | — |
| **Test Suite** | YAML + JSON deterministic tests | 101 cases |

## SYSTEM_FLOW

```
Phase 1 (Generation):
  Invoice Data (5 fields)
    → TLV Encoder (Tags 1-5)
    → Base64
    → QR Image

Phase 2 (Onboarding):
  CSR + OTP
    → POST /api/v1/submit/compliance
    → Compliance CSID (certificate + secret)
    → POST /api/v1/submit/production-csid
    → Production CSID

Phase 2 (Full Process — Simplified):
  Invoice Data (full)
    → Validate (48 YAML rules)
    → Sign (ECDSA secp256k1)
    → QR (TLV tags 1-9)
    → POST /api/v1/submit/reporting
    → State: Reported

Phase 2 (Full Process — Standard):
  Invoice Data (full)
    → Validate (48 YAML rules)
    → Sign (ECDSA secp256k1)
    → QR (TLV tags 1-9)
    → POST /api/v1/submit/clearance
    → State: Cleared
```

## SPECIFICATION (zatca-open-spec — 20 YAML files)

```
spec/
├── contracts/              # API contracts (onboarding, clearance, reporting, compliance)
├── models/                 # Domain models (invoice, party, line-item, tax)
├── rules/                  # Validation rules (48 rules across schema, business, tax, crypto, sequence)
├── security/               # Crypto specs (QR TLV, ECDSA signing, X.509 certs)
├── lifecycle/              # State machine (7 states, 12 transitions, 6 events)
├── errors/                 # Standard error codes (35 codes, 8 categories)
└── examples/               # Complete YAML examples (4 invoice types)
```

Design Principles:
- Implementation-agnostic (zero code — pure YAML)
- Deterministic & testable
- Single source of truth for all SDKs
- Domain-driven (feature boundaries, not file boundaries)
- Simplicity First — no abstraction before repetition

## MILESTONES

- [x] **M1**: Core Models + Validation (3 YAML files)
- [x] **M2**: Security + Lifecycle (5 YAML files)
- [x] **M3**: API Contracts + Errors (5 YAML files)
- [x] **M4**: Examples + PROJECT_MAP + Support Models (7 YAML files)
- [x] **M5**: Core Engine (Node.js REST microservice — validation, crypto, pipeline, FATOARA client)
- [x] **M6**: Emulator (Node.js sandbox mimicking ZATCA government API)
- [x] **M7**: Laravel SDK (thin HTTP client — Zatca.php, Manager, ServiceProvider, Facade)
- [x] **M8**: Test Suite (101 deterministic test cases covering phases 1 & 2, including FATOARA APIs)
- [ ] **M9**: Django SDK (thin Python HTTP client)
- [ ] **M10**: .NET SDK (thin C# HTTP client)
- [ ] **M11**: Node.js SDK (thin JavaScript HTTP client)
- [ ] **M12**: CI/CD Pipeline (spec validation, YAML lint, integration tests)
- [ ] **M13**: Docker Deployment (core-engine + emulator containers)

## REPOSITORY STRUCTURE

```
ZATCA-open-Platform/           # Mono repo index (this repo)
├── README.md                  # Repo overview & architecture
├── PROJECT_MAP.md             # This file — project map & status
└── zatca-open-spec/           # Git submodule to zatca-open-specification

zatca-open-specification/      # Separate repo (github.com/zatca-platform/zatca-open-specification)
zatca-core-engine/             # Separate repo (github.com/zatca-platform/zatca-core-engine)
zatca-emulator/                # Separate repo (github.com/zatca-platform/zatca-emulator)
zatca-laravel-sdk/             # Separate repo (github.com/zatca-platform/zatca-laravel-sdk)
zatca-compliance-test-suite/   # Separate repo (github.com/zatca-platform/zatca-compliance-test-suite)
```

## COMPLETED WORK

| Component | Repository | Files | Status |
|-----------|-----------|-------|--------|
| YAML Specification | zatca-open-specification | 20 YAML + PHP parser | ✅ Done |
| Core Engine | zatca-core-engine | 8 source files (server, routes, validation, crypto, pipeline, client, specLoader) | ✅ Done |
| Emulator | zatca-emulator | 6 route files + middleware + config + store | ✅ Done |
| Laravel SDK | zatca-laravel-sdk | 4 Laravel source files | ✅ Done |
| Test Suite | zatca-compliance-test-suite | 101 test cases (YAML + JSON) | ✅ Done |

## PENDING

| Component | Language | Status |
|-----------|----------|--------|
| Django SDK | Python | ⏳ Pending |
| .NET SDK | C# | ⏳ Pending |
| Node.js SDK | JavaScript | ⏳ Pending |
| CI/CD Pipeline | — | ⏳ Pending |
| Docker | — | ⏳ Pending |
