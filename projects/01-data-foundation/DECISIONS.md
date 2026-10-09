# P1 Architecture Decision Index

These decisions were approved before implementation and are not reopened by P1.0/P1.1. Status `Accepted` means the logical model must conform. A future change requires an explicit superseding ADR; cross-project impact must be labeled **MASTER ARCHITECTURE DECISION NEEDED**.

| ID | Decision | Status | Reasoning | Important tradeoffs | ADR |
|---|---|---|---|---|---|
| D001 | One durable canonical Person per human across sources, stages, and employers. | Accepted | Stable human identity prevents CRM lifecycle/source representations from fragmenting history. | Resolution and merge/unmerge become explicit operational responsibilities. | [ADR-0001](architecture/adr/0001-canonical-person.md) |
| D002 | Parent companies and subsidiaries are separate Accounts joined by explicit hierarchy relationships. | Accepted | Revenue, contracts, territories, and engagement often occur at different organizational levels. | Hierarchy traversal and cycle validation are required. | [ADR-0002](architecture/adr/0002-canonical-account-hierarchy.md) |
| D003 | Preserve selected Account name, domain, and hierarchy history with selective SCD2. | Accepted | As-of analysis and identity evidence require time-aware attributes. | More joins and interval-quality tests; avoids blanket SCD2 complexity. | [ADR-0003](architecture/adr/0003-historical-account-attributes.md) |
| D004 | Lead and Contact are source-system representations linked to Person. | Accepted | Operational object type and conversion state do not define a human. | Source conversion history must be preserved outside the canonical Person. | [ADR-0004](architecture/adr/0004-lead-and-contact.md) |
| D005 | Opportunity has one primary Account; people participate through OpportunityPersonRole. | Accepted | Makes pipeline ownership unambiguous while supporting a multi-person buying committee. | Multi-account deals require later explicit extensions rather than overloading primary Account. | [ADR-0005](architecture/adr/0005-opportunity-relationships.md) |
| D006 | Customer is Account lifecycle status; Contract and Subscription retain independent identities/history. | Accepted | Avoids a duplicate company identity and preserves commercial object lifecycles. | The authoritative derivation of customer status is deferred and must be governed. | [ADR-0006](architecture/adr/0006-customer-lifecycle.md) |
| D007 | Activity, Campaign, and ProductUser are distinct and link to canonical context while supporting unresolved identities. | Accepted | Separates interactions, initiatives, and product profiles while preventing data loss before matching. | More explicit joins and resolution-state handling. | [ADR-0007](architecture/adr/0007-interaction-model.md) |
| D008 | Use a separate auditable external-ID crosswalk with effective-dated mappings and corrections. | Accepted | Decouples source keys from canonical identity and makes reconciliation explainable. | Requires mapping governance and time-aware uniqueness controls. | [ADR-0008](architecture/adr/0008-external-identity-crosswalk.md) |

## Implementation note

P1.1 implements these decisions in the logical entity catalog and ERD. It does not implement the matching, survivorship, reconciliation, or physical history mechanisms that operationalize them; those begin in later P1 phases.
