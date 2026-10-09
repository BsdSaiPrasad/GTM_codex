# Shared Contracts

This directory is the authoritative cross-project contract boundary for SAI RevenueOS.

`canonical-model.v1.json` defines P1's versioned logical entity and relationship catalog. It is deliberately implementation-neutral: downstream projects may map it into warehouse, API, event, or semantic-layer representations without changing canonical meaning.

## Versioning rules

- Additive, backward-compatible documentation or optional attributes may retain major version 1.
- Renaming/removing an entity, changing identifier semantics, or weakening a cardinality invariant requires a new major version.
- Do not copy and modify this contract inside a project. Propose changes here and assess consumers.
- A contract update must pass `projects/01-data-foundation/scripts/validate_architecture.py` and update P1 continuity files.

The contract is architectural in P1.1. It is not a deployable database DDL or an assertion that source data has already been loaded.
