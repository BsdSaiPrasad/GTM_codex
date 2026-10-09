# ADR-0005: Opportunity Relationships

- **Decision ID:** D005
- **Status:** Accepted
- **Scope:** P1 revenue model

## Context

A sales opportunity needs a clear buying company but typically includes multiple stakeholders with changing roles.

## Decision

Require exactly one primary buying Account on Opportunity. Represent each participating Person through an effective-dated OpportunityPersonRole.

## Consequences

Pipeline attribution is unambiguous and buying committees are normalized. A future need for partner or multi-account deal roles must be modeled explicitly without weakening the primary-account invariant.
