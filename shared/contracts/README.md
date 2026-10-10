# Shared Contracts

## What Is a Shared Contract?

A data contract is a versioned agreement about data meaning, structure, and allowed relationships. This directory is the authoritative cross-project contract boundary for SAI RevenueOS.

`canonical-model.v1.json` defines Project 1's logical entity and relationship catalog. It is machine-readable and implementation-neutral. Future projects may map it into warehouse tables, Application Programming Interfaces (APIs), events, or semantic models without changing what an Account, Person, or other canonical entity means.

For a plain-English explanation of every entity, read the [canonical business model](../../projects/01-data-foundation/architecture/CANONICAL_BUSINESS_MODEL.md). For terminology, use the [glossary](../../docs/GLOSSARY.md).

## Versioning rules

- Additive, backward-compatible documentation or optional attributes may retain major version 1.
- Renaming/removing an entity, changing identifier semantics, or weakening a cardinality invariant requires a new major version.
- Do not copy and modify this contract inside a project. Propose changes here and assess consumers.
- A contract update must pass `projects/01-data-foundation/scripts/validate_architecture.py` and update P1 continuity files.

The current contract is architecture, not deployable Data Definition Language (DDL), and it does not claim that source data has been loaded.
