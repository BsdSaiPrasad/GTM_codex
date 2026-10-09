# SAI RevenueOS

SAI RevenueOS is a synthetic, enterprise-style B2B SaaS Revenue/GTM platform. This monorepo is organized as seven bounded projects with shared contracts at the repository root. Only Project 1 (P1) is implemented in the current milestone.

All companies and business data used by this repository must be fictional or synthetic. Do not commit credentials, production extracts, or real customer information.

## Current scope

- **P1.0 — Engineering Foundation:** repository conventions, project continuity, configuration safety, and validation tooling.
- **P1.1 — Canonical Business Model & ERD:** logical canonical entities, identity/history structures, source-system mappings, relationship cardinalities, and shared machine-readable contracts.
- **P2–P7:** intentionally not scaffolded or implemented yet.

## Repository map

```text
.
├── docs/MASTER_ARCHITECTURE.md
├── shared/contracts/
│   ├── README.md
│   └── canonical-model.v1.json
└── projects/01-data-foundation/
    ├── README.md
    ├── PROJECT_STATE.md
    ├── DECISIONS.md
    ├── WORK_LOG.md
    ├── architecture/
    │   ├── CANONICAL_BUSINESS_MODEL.md
    │   ├── adr/
    │   └── diagrams/canonical-model.mmd
    └── scripts/validate_architecture.py
```

Start with [the master architecture](docs/MASTER_ARCHITECTURE.md), then use [the P1 README](projects/01-data-foundation/README.md) for the model and verification entry points.

## Verification

P1.0/P1.1 have no runtime services or external dependencies. Validate the architecture with Python 3.9 or newer:

```bash
python3 projects/01-data-foundation/scripts/validate_architecture.py
```

The validator checks the shared JSON contract, required entities and decisions, relationship endpoints, temporal invariants, Mermaid coverage, and accidental secret-like tracked filenames.
