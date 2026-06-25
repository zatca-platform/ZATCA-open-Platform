# ZATCA Open Specification — Mono Repo Index

## Repositories

| Repo | Path | Description |
|------|------|-------------|
| **zatca-open-spec** | `./zatca-open-spec/` | Canonical specification: models, rules, security, lifecycle, contracts, errors |
| **zatca-compliance-test-suite** | `../zatca-compliance-test-suite/` | 84 deterministic cross-language compliance tests |
| **laravel-zatca** (planned) | — | Laravel SDK implementation |
| **django-zatca** (planned) | — | Django SDK implementation |
| **dotnet-zatca** (planned) | — | .NET SDK implementation |
| **node-zatca** (planned) | — | Node.js SDK implementation |

## Binding Model

Specification → Versioning → Packages → Shared Tests

Each repo maintains its own version, referencing `zatca-open-spec` as the source of truth and using `zatca-compliance-test-suite` for cross-language parity verification.
