# ZATCA Open Specification — Project Map

## TECH_STACK

| Component | Technology | Version |
|-----------|-----------|---------|
| **Spec Format** | YAML | — |
| **Invoice Schema** | UBL 2.1 | 2.1 |
| **Signature** | XAdES EPES | v1.3.2 |
| **Encryption** | ECDSA secp256k1 + SHA-256 | — |
| **QR Encoding** | TLV Base64 (GS1-compatible) | — |
| **ZATCA API** | FATOORA API | V2 (2024+) |
| **Reference SDK** | Laravel (`aghfatehi/laravel-zatca`) | v1.1.0 |
| **Target SDKs** | Laravel, Django, .NET, Node.js | — |
| **Certificate** | X.509 v3 | — |

## SYSTEM_FLOW

```
Phase 1 (Generation):
  Invoice Data (5 fields)
    → TLV Encoder (Tags 1-5)
    → Base64
    → QR Image

Phase 2 (Integration) — Simplified:
  Invoice Data (full)
    → Validation (48 rules)
    → UBL 2.1 XML
    → C14N Exclusive
    → SHA-256 Hash
    → ECDSA Signature
    → XAdES EPES Envelope
    → TLV QR (Tags 1-9)
    → POST /invoices/reporting/single
    → ZATCA Acknowledges
    → State: Reported

Phase 2 (Integration) — Standard:
  [Same steps as Simplified until XAdES]
    → POST /invoices/clearance/single
    → ZATCA Validates & Stamps
    → Cryptographic Stamp Returned
    → State: Cleared

Offline Mode:
  [Sign → Store → Queue → Sync Later]
```

## ARCHITECTURE

```
/zatca-open-spec/           # ← YOU ARE HERE (Source of Truth)
├── contracts/              # API contracts (onboarding, clearance, reporting, compliance)
├── models/                 # Domain models (invoice, party, line-item, tax)
├── rules/                  # Validation engine (48 rules across 8 categories)
├── security/               # Crypto specs (QR TLV, ECDSA signing, X.509 certs)
├── lifecycle/              # State machine (7 states, 12 transitions, 6 events)
├── errors/                 # Standard error codes (35 codes, 8 categories)
└── examples/               # Complete YAML examples (4 invoice types)

Design Principles:
  - Implementation-agnostic (zero code)
  - Deterministic & testable
  - Single source of truth for all SDKs
  - Domain-driven (feature boundaries, not file boundaries)
  - Simplicity First — no abstraction before repetition
```

## ORPHANS & PENDING

| Item | Status | Notes |
|------|--------|-------|
| Invoice Model (invoice.yaml) | Done | Full model w/ sub_types |
| Party Model (party.yaml) | Done | Seller, buyer, address, contact |
| Validation Rules (validation-rules.yaml) | Done | 48 rules: schema, business, crypto, sequence |
| QR Spec (qr-spec.yaml) | Done | Phase 1 (5 tags) + Phase 2 (9 tags) |
| Signature Spec (signature-spec.yaml) | Done | ECDSA + XAdES + hash chaining |
| Certificate Spec (certificate-spec.yaml) | Done | CSR → Onboarding → CSID |
| Lifecycle (lifecycle.yaml) | Done | 7 states, 12 transitions |
| Events (events.yaml) | Done | 6 domain events |
| API Contracts (contracts/*.yaml) | Done | Onboarding, clearance, reporting, compliance |
| Error Codes (errors/error-codes.yaml) | Done | 35 standardized codes |
| Line Item Model (models/line-item.yaml) | Done | Reference to invoice.yaml |
| Tax Model (models/tax.yaml) | Done | KSA VAT categories & calc rules |
| Schema Rules (rules/schema-rules.yaml) | Done | XML/UBL namespace & element rules |
| Tax Rules (rules/tax-rules.yaml) | Done | KSA-specific tax formulas & exemptions |
| Examples (examples/*.yaml) | Done | Simplified, standard, credit, debit |
| SDK Implementations | PENDING | Plan: Laravel → Django → .NET → Node.js |
| Integration Tests | Done | ../zatca-compliance-test-suite (separate repo) |
| CI/CD Pipeline | PENDING | Plan: spec validation, YAML lint |
| Localization (EN/AR) | PENDING | Plan: Arabic field descriptions |
| UBL XSD Schemas | PENDING | Plan: reference schemas for validation |
| Postman Collection | PENDING | Plan: API test suite |

## MILESTONES COMPLETED

- [x] **M1**: Core Models + Validation (3 files)
- [x] **M2**: Security + Lifecycle (5 files)
- [x] **M3**: API Contracts + Errors (5 files)
- [x] **M4**: Examples + PROJECT_MAP + Support Models (7 files)
- [ ] **M5**: SDK Implementations (pending approval)
- [ ] **M6**: Testing & CI (pending approval)

## FILE INDEX

```
zatca-open-spec/
├── contracts/
│   ├── onboarding.yaml         # CSR submission & CSID issuance
│   ├── clearance.yaml          # Standard invoice clearance
│   ├── reporting.yaml          # Simplified invoice reporting
│   └── compliance.yaml         # ZATCA validation response
├── models/
│   ├── invoice.yaml            # Full invoice domain model
│   ├── party.yaml              # Party & address model
│   ├── line-item.yaml          # Line item reference
│   └── tax.yaml                # KSA VAT model
├── rules/
│   ├── validation-rules.yaml   # 48 validation rules (R-KSA + technical)
│   ├── schema-rules.yaml       # UBL/XML schema compliance
│   └── tax-rules.yaml          # Tax calculation formulas
├── security/
│   ├── qr-spec.yaml            # TLV QR code spec
│   ├── signature-spec.yaml     # ECDSA + XAdES spec
│   └── certificate-spec.yaml   # X.509 certificate lifecycle
├── lifecycle/
│   ├── lifecycle.yaml          # State machine (7 states, 12 transitions)
│   └── events.yaml             # 6 domain events
├── errors/
│   └── error-codes.yaml        # 35 error codes
└── examples/
    ├── invoice-simplified.yaml # B2C retail example
    ├── invoice-standard.yaml   # B2B IT services example
    ├── credit-note.yaml        # Goods return example
    └── debit-note.yaml         # Additional charges example
```

Total: **20 files** across **7 directories**
