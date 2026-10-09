# P1 — GTM Data Foundation & Customer Identity Platform

P1 establishes the source-independent identities and relationships that make revenue operations data trustworthy across CRM, marketing, product, billing, support, and future systems. P1.0 provides the engineering/documentation foundation; P1.1 defines the logical canonical business model and ERD.

## Business purpose

Operational systems describe the same company or human differently. P1 supplies durable Account and Person identities, preserves source provenance, records selected history, and exposes consistent commercial entities to future SAI RevenueOS projects. This avoids treating a CRM Lead, CRM Contact, product user, or a renamed subsidiary as a new real-world identity by default.

## Architecture at a glance

1. `SourceRecord` identifies the source-system object without forcing a canonical match.
2. `ExternalIdentityCrosswalk` links that source record to a canonical entity with effective dates, method, confidence, and review metadata.
3. `Account` and `Person` provide stable identities; relationship tables preserve employer, hierarchy, name, and domain history.
4. Revenue, interaction, service, signal, decision, and workflow entities reference canonical context while retaining provenance.
5. `shared/contracts/canonical-model.v1.json` is the cross-project logical contract.

## Key artifacts

- [`architecture/CANONICAL_BUSINESS_MODEL.md`](architecture/CANONICAL_BUSINESS_MODEL.md) — entity dictionary, cardinalities, lifecycle, provenance, security, and future physical-design guidance.
- [`architecture/diagrams/canonical-model.mmd`](architecture/diagrams/canonical-model.mmd) — version-controlled Mermaid ERD.
- [`../../shared/contracts/canonical-model.v1.json`](../../shared/contracts/canonical-model.v1.json) — machine-readable logical contract.
- [`DECISIONS.md`](DECISIONS.md) and [`architecture/adr/`](architecture/adr/) — approved decision index and records.
- [`PROJECT_STATE.md`](PROJECT_STATE.md) — authoritative current state and next step.
- [`WORK_LOG.md`](WORK_LOG.md) — evidence-based implementation log.

## Inspect and validate

Requirements: Python 3.9+ only. No credentials, warehouse, package installation, or external service is needed.

```bash
python3 projects/01-data-foundation/scripts/validate_architecture.py
```

The command is intended to run from any working directory. It validates contract structure, foreign-key endpoints, required decisions/entities, temporal structures, Mermaid entity/relationship coverage, continuity artifacts, and tracked secret-like filenames.

To preview the ERD, open `canonical-model.mmd` in a Mermaid-compatible Markdown/editor integration or paste its contents into a local Mermaid renderer. Rendering is a documentation convenience; the repository validator performs offline structural checks.

## Current limitations

This milestone is a logical architecture, not a physical Snowflake schema. It does not include source connectors, matching, survivorship, dbt models, reverse ETL, workflow implementations, authoritative customer-status rules, row-level security, or deployed monitoring. Polymorphic subject references are explicit logical contracts; their physical enforcement is deferred until platform/tooling decisions are approved.

## Future directories

The approved project layout anticipates `ingestion/`, `identity/`, `dbt/`, `activation/`, `tests/`, `runbooks/`, and `sample-data/`. They are intentionally not created as empty scaffolds in P1.0/P1.1. Add each only when its phase delivers working content.
