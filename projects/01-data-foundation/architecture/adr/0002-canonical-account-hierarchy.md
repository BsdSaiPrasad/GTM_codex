# ADR-0002: Canonical Account Hierarchy

- **Decision ID:** D002
- **Status:** Accepted
- **Scope:** P1 organization identity

## Context

Parent organizations and subsidiaries may buy, contract, use products, and receive support independently while still requiring roll-up views.

## Decision

Give every parent and subsidiary its own Account ID. Connect them with effective-dated AccountHierarchyRelationship edges.

## Consequences

Entity-specific facts remain correctly attributed and roll-ups are possible. Later physical controls must prevent self-links, invalid cycles, and conflicting active relationships.
