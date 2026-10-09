# Canonical Business Model

## 1. Scope

This is the P1.1 logical model for SAI RevenueOS. It specifies business meaning, stable identity, key relationships, selective history, and source provenance. It intentionally stops before physical Snowflake DDL, matching rules, survivorship, pipeline design, and activation.

The version-controlled visual is [`diagrams/canonical-model.mmd`](diagrams/canonical-model.mmd). The authoritative cross-project machine-readable catalog is [`../../../shared/contracts/canonical-model.v1.json`](../../../shared/contracts/canonical-model.v1.json). When this narrative and the contract differ, treat the contract as the interface and resolve the documentation defect before implementation.

## 2. Modeling conventions

### Stable identity

Canonical primary keys are opaque, source-independent identifiers named `<entity>_id`. They never encode a CRM ID, email, domain, or mutable business attribute. P1.2 will choose the generation mechanism and crosswalk reconciliation strategy.

### Time and history

Selective SCD Type 2 semantics apply only where the business needs state as-of-time: account name, account domain, account hierarchy, person-account affiliation, opportunity participant role, and crosswalk mapping. These structures use half-open intervals: `valid_from` is inclusive, `valid_to` is exclusive, and a null `valid_to` means current. Immutable events/snapshots are appended. This avoids applying SCD2 to every table.

### Optional resolution

Interactions and product profiles may arrive before identity resolution. Their source reference and `resolution_status` remain usable while canonical foreign keys are null. A missing match never justifies fabricating a Person or Account.

### Polymorphic subjects

Signal, EnrichmentSnapshot, and Decision use an explicit `subject_entity_type` plus `subject_entity_id` logical reference. ExternalIdentityCrosswalk uses the analogous `canonical_entity_type` and `canonical_entity_id`. The allowed targets are enumerated in the shared contract. A later physical design must enforce them through a validated registry, subtype link tables, or equivalent warehouse tests; unrestricted strings are not acceptable in production.

## 3. Identity and organization model

| Entity | Business definition and purpose | PK and important attributes | FKs / relationships and cardinality | History and source relationship | Future consumers |
|---|---|---|---|---|---|
| **Account** | Durable identity for a selling-relevant organization. Parent and subsidiary companies remain separate Accounts. Customer is a lifecycle status on this entity, not another company record. | `account_id`; type, lifecycle status, customer start/end timestamps, audit timestamps. | One Account has 1..* names, 0..* domains, affiliations, Opportunities, commercial objects, and hierarchy edges. | Identity is durable. Selected mutable facts live in effective-dated tables. Source companies/accounts map through the crosswalk. | P2–P7 |
| **Person** | One human across systems, lifecycle stages, and employers. Prevents Lead, Contact, and product identities from becoming duplicate humans. | `person_id`; display name, normalized primary email, identity status, audit timestamps. | One Person has 0..* affiliations, Opportunity roles, Activities, product profiles, and cases. | Person survives employer/source changes. Lead, Contact, marketing, support, and product records map through the crosswalk. | P2–P7 |
| **AccountPersonRelationship** | Effective-dated Person affiliation with an Account (employee, advisor, executive sponsor, and similar). | `account_person_relationship_id`; relationship type, title, department, primary flag, validity. | Each row belongs to exactly one Account and one Person; both may have 0..* rows. | Selective SCD2. Source association provenance is retained; primary intervals should not overlap for the same relationship class. | P2, P3, P4, P6 |
| **AccountHierarchyRelationship** | Directed parent/subsidiary or other organizational relationship between distinct Accounts. | `account_hierarchy_relationship_id`; parent, child, relationship type, validity, source reference. | Each row has exactly one parent and one child; Accounts may participate in 0..* edges. Self-links and cycles are invalid. | Selective SCD2; former parents remain historical. Source assertion or stewardship action is recorded. | P2–P6 |
| **AccountNameHistory** | Effective-dated legal name, trade name, or alias for an Account. | `account_name_history_id`; account, name, type, preferred flag, validity, source reference. | Account has 1..* names; each name row belongs to one Account. Exactly one preferred active name is expected. | Selective SCD2; never rename the Account identity. Source/steward provenance is required. | P1–P6 |
| **AccountDomainHistory** | Effective-dated normalized domain associated with an Account. A domain is evidence, not identity. | `account_domain_history_id`; account, normalized domain, type, primary flag, validity, source reference. | Account has 0..* domains; each row belongs to one Account. | Selective SCD2. Reassignment requires non-overlapping evidence and preserved prior ownership. | P1–P4, P6 |
| **SourceRecord** | Stable pointer to one object in one source tenant/system. Preserves records even before or without canonical resolution. | `source_record_id`; system, tenant, object type/ID, source and ingestion timestamps, payload reference, fingerprint, deletion flag. | One SourceRecord has 0..* historical crosswalk mappings and may provide provenance to operational entities. | The source natural key is stable; raw record versions belong to later ingestion storage. Lead and Contact are `source_object_type` values, not canonical entities. | P1, P7 |
| **ExternalIdentityCrosswalk** | Auditable effective-dated link from one SourceRecord to one canonical entity at a time. | `external_identity_crosswalk_id`; source record, canonical type/ID, status, method, confidence, validity, supersession/review fields. | Each row has one SourceRecord and one supported canonical target. A SourceRecord may have multiple non-overlapping mappings over time. | Append/effective-date corrections; never rewrite mapping history. Detailed matching and survivorship are deferred to P1.2+. | P1–P7 |

## 4. Revenue and commercial model

| Entity | Business definition and purpose | PK and important attributes | FKs / relationships and cardinality | History and source relationship | Future consumers |
|---|---|---|---|---|---|
| **Opportunity** | Source-independent potential commercial transaction with exactly one primary buying Account. | `opportunity_id`; primary account, name, stage, amount/currency, expected close, open/close timestamps. | Exactly one Account per Opportunity; Account has 0..* Opportunities. Opportunity has 0..* people, activities, quotes, and contracts. | Stable identity; stage/amount event history is deferred. CRM opportunities map through the crosswalk. | P2–P6 |
| **OpportunityPersonRole** | A Person's role in a specific buying process, supporting buying committees. | `opportunity_person_role_id`; opportunity, person, role, influence, primary-contact flag, validity. | Exactly one Opportunity and Person per row; each can have 0..* role rows. | Effective-dated role changes. Source opportunity-contact roles and future inferences retain provenance. | P3–P6 |
| **Quote** | Versioned priced proposal for an Opportunity and buying Account. | `quote_id`; opportunity, account, quote number, version, status, value/currency, expiry. | One Opportunity/Account may have 0..* Quotes; Quote may authorize 0..* Orders. | Each revision is a distinct version; accepted facts are not overwritten. CPQ/CRM quotes map through the crosswalk. | P4, P5 |
| **Contract** | Legally binding agreement with an Account, independent of Account lifecycle and Subscription identity. | `contract_id`; account, optional opportunity, number, status, signed/start/end dates, value/currency. | One Account has 0..* Contracts; Opportunity may yield 0..*; Contract governs 0..* Orders and Subscriptions. | Amendments/renewals are linked identities; executed terms change only through audited correction. CLM/CRM/billing objects map through crosswalk. | P4–P6 |
| **Order** | Accepted request to provision or fulfill products/services. | `order_id`; account, optional contract/quote, order number, status, ordered time, total/currency. | Exactly one Account; optional Contract/Quote; one Order may provision 0..* Subscriptions. | Status transitions must be auditable; accepted identity is stable. ERP/CPQ/billing orders map through crosswalk. | P5, P6 |
| **Subscription** | Time-bounded recurring entitlement for an Account. | `subscription_id`; account, optional contract/order, product, status, term, quantity, billing frequency. | Exactly one Account; optional Contract and Order; one Subscription can have 0..* Invoices and Cases. | Terms/status will be effective-dated or amendment-based in physical design. Billing/entitlement records map through crosswalk. | P4–P6 |
| **Invoice** | Billing demand issued to an Account for a recurring or commercial obligation. | `invoice_id`; account, optional subscription, number, status, issue/due dates, due/paid amounts, currency. | Exactly one Account; optional Subscription; each parent can have 0..* Invoices. | Append-oriented; credits, voids, and corrections remain auditable. ERP/billing invoices map through crosswalk. | P4–P6 |

No Contract, Order, Subscription, or Invoice is collapsed into Account. The still-unapproved rule that derives Account `lifecycle_status = customer` will be designed later from authoritative commercial evidence.

## 5. Engagement, product, service, and execution model

| Entity | Business definition and purpose | PK and important attributes | FKs / relationships and cardinality | History and source relationship | Future consumers |
|---|---|---|---|---|---|
| **Campaign** | Canonical marketing or coordinated outreach initiative, distinct from the interactions it generates. | `campaign_id`; name, type, status, start/end, owner team. | Campaign groups 0..* Activities; an Activity references 0..1 Campaign. | Stable identity with future status history as needed. Source campaigns map through crosswalk. | P2–P4 |
| **Activity** | Time-stamped business interaction such as email, meeting, call, form submission, or product-related touch. | `activity_id`; type, occurrence time, optional canonical contexts, source reference, resolution status. | Each Activity has 0..1 Person, Account, Opportunity, Campaign, and SourceRecord; each parent has 0..* Activities. | Append-oriented with audited corrections. Can remain unresolved using SourceRecord only. | P2–P4, P6, P7 |
| **ProductUser** | Product-system profile linked, when resolvable, to one Person and Account context. It is not the canonical human. | `product_user_id`; optional person/account, source record, tenant key, status, first/last seen, resolution status. | Exactly one SourceRecord; 0..1 Person and Account. Each canonical entity may link to 0..* product profiles. | Profile changes stay in source history; link corrections are crosswalk-audited. | P4, P6 |
| **SupportCase** | Customer support request/incident with relevant Account, Person, and Subscription context. | `support_case_id`; required account, optional person/subscription, number, status, priority, open/close timestamps. | Account has 0..* Cases; Person and Subscription each relate to 0..* optionally. | Stable identity; detailed transition events are deferred. Support objects map through crosswalk. | P4, P6, P7 |
| **Signal** | Derived time-stamped observation (intent, engagement, risk, or data quality) about an allowed canonical subject. | `signal_id`; subject type/ID, type, observed time, value, confidence, derivation version, expiry. | Logical 1 subject per Signal; a subject has 0..* Signals. | Append-only and derivation-versioned; expiry never deletes evidence. Input lineage arrives with later pipelines. | P2–P4, P6, P7 |
| **EnrichmentSnapshot** | Immutable point-in-time provider/internal enrichment response for Account or Person. | `enrichment_snapshot_id`; subject type/ID, provider, observation time, payload reference, schema version, quality status. | One Account/Person logical subject; subject has 0..* snapshots. | Immutable; each refresh creates a row. Provider request/response and licensing provenance must be retained. | P2–P4, P6 |
| **Decision** | Auditable human or machine decision about a business subject. | `decision_id`; subject, outcome, reason codes, policy version, decision time/actor, optional workflow run. | One allowed logical subject; 0..1 producing WorkflowRun. A run may produce 0..* Decisions. | Append-only; a superseding decision references prior evidence rather than erasing it. | P2–P7 |
| **WorkflowRun** | One attempt of an automated or human-assisted revenue workflow. | `workflow_run_id`; name/version, idempotency key, status, timestamps, trigger, optional retry parent, error code. | Self-reference 0..1 prior attempt; a run can have 0..* retries and Decisions. | Immutable attempt record; retry creates a new linked run. Generated by future automations/pipelines. | P2–P7 |

## 6. Source-system representation

Example: Salesforce Lead `00Q-fictional-17` and later Contact `003-fictional-82` are two SourceRecords. Both can map to the same Person after resolution. Lead conversion is preserved as source lineage and mapping history; it does not transform one canonical Person into another. A HubSpot contact or product user for that human can add further SourceRecords and crosswalk rows.

Source uniqueness is scoped by `(source_system, source_tenant, source_object_type, source_object_id)`. Source deletion is represented as observed source state, not permission to erase canonical or audit history. Raw payload location and access controls remain ingestion responsibilities.

## 7. Cardinality and integrity rules

- Opportunity → Account is mandatory many-to-one; all other context FKs explicitly marked optional may be null while unresolved or inapplicable.
- Parent and child Account IDs in a hierarchy edge must differ. The active hierarchy must be acyclic. Whether multiple simultaneous parents are allowed depends on `relationship_type` and will be enforced through later tests.
- Effective-dated records must satisfy `valid_from < valid_to` when `valid_to` exists. Current preferred/primary intervals must not conflict.
- Crosswalk canonical target type must be allowed by the shared contract, and its ID must exist before the mapping becomes active.
- `source_system + source_tenant + source_object_type + source_object_id` identifies one SourceRecord.
- Monetary values always travel with `currency_code`; conversion policy is outside P1.1.
- Timestamps are stored as UTC instants in the physical model; business dates remain dates.

## 8. Auditability, security, and recovery considerations

- **Provenance:** SourceRecord, source references, mapping method, policy/derivation version, and observation timestamps make facts explainable.
- **Correction:** close/supersede erroneous time-bound rows; do not delete evidence. Manual actions require actor and review timestamps.
- **Security:** Person and Activity can contain personal data. The physical design must classify columns, minimize replication, restrict raw payload access, apply retention/deletion policy, and audit privileged reads.
- **Idempotency:** future ingestion keys on source identity and fingerprints; future workflows key on `idempotency_key` and create linked retry attempts.
- **Failure recovery:** unresolved and failed records remain quarantinable by status without blocking valid records. Reprocessing must preserve original SourceRecord identity.
- **Extensibility:** new source object types do not require new canonical person tables. New polymorphic target types require a versioned contract change and downstream impact review.

## 9. Deferred decisions

P1.1 intentionally does not select UUID/ULID/sequence identifiers, matching thresholds, survivorship precedence, merge/unmerge procedures, authoritative customer-status rules, event-history granularity, warehouse types, or enforcement pattern for polymorphic references. The immediate next phase is **P1.2 — Canonical ID Generation & External-ID Crosswalk Strategy**.
