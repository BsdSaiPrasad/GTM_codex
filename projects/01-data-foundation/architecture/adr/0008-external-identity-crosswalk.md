# ADR-0008: Auditable External Identity Crosswalk

- **Decision ID:** D008
- **Status:** Accepted
- **Scope:** P1 source-to-canonical identity

## Context

Canonical entities can have multiple source records, source identifiers can change, and an initially plausible match can later require correction.

## Decision

Use ExternalIdentityCrosswalk as a separate effective-dated mapping from SourceRecord to a supported canonical entity. Record method, status, confidence, validity, review, and supersession metadata.

## Consequences

Canonical IDs remain source-independent and corrections are explainable without rewriting history. P1.2 must define ID generation, active-mapping uniqueness, matching states, and reconciliation behavior.
