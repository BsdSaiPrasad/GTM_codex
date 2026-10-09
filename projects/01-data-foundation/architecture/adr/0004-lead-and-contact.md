# ADR-0004: Lead and Contact as Source Representations

- **Decision ID:** D004
- **Status:** Accepted
- **Scope:** P1 person modeling

## Context

Salesforce and HubSpot use lifecycle-specific Lead and Contact objects. The same human can have more than one of these records and can undergo conversion.

## Decision

Represent Lead and Contact as SourceRecord object types linked, when resolved, to Person through ExternalIdentityCrosswalk. Preserve external identifiers and conversion history.

## Consequences

Canonical identity is not coupled to CRM lifecycle. Consumers that need operational Lead/Contact fields must join source lineage rather than expect those fields on Person.
