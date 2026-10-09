# ADR-0003: Selective Historical Account Attributes

- **Decision ID:** D003
- **Status:** Accepted
- **Scope:** P1 temporal modeling

## Context

Account names, domains, and organizational parents change. Overwriting them destroys identity evidence and as-of reporting, while applying SCD2 indiscriminately makes the whole model difficult to operate.

## Decision

Use explicit effective-dated history for Account names, domains, and hierarchy relationships. Apply temporal structures elsewhere only when a business requirement justifies them.

## Consequences

Important change history remains queryable with contained complexity. Interval overlap, current-row, and provenance tests become mandatory in later physical implementation.
