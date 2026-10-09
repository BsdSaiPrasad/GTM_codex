# ADR-0001: Durable Canonical Person

- **Decision ID:** D001
- **Status:** Accepted
- **Scope:** P1 canonical identity

## Context

A human may appear as a marketing contact, CRM Lead, converted Contact, product profile, and support requester, and may change employers.

## Decision

Represent the human once as a durable Person. Model employer history through AccountPersonRelationship and source representations through SourceRecord plus ExternalIdentityCrosswalk.

## Consequences

Longitudinal history is coherent and source lifecycle changes do not create new people. P1 must later provide conservative match, merge, unmerge, and stewardship controls because a bad canonical merge has broad impact.
