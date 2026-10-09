# ADR-0007: Distinct Interaction Concepts

- **Decision ID:** D007
- **Status:** Accepted
- **Scope:** P1 engagement model

## Context

An interaction event, the campaign that influenced it, and a user's product-system profile have different identities, lifecycles, and analytical meanings.

## Decision

Model Activity, Campaign, and ProductUser separately. Link them to canonical Person, Account, or Opportunity context when known and preserve SourceRecord context plus resolution status when unknown.

## Consequences

Attribution and usage analysis do not conflate unlike concepts, and early-arriving events are not lost. Consumers must handle optional canonical context explicitly.
