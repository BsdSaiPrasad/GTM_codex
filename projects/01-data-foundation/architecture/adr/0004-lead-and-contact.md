# ADR-0004: Model Lead and Contact as Source Records

- **Decision ID:** D004
- **Status:** Accepted
- **Scope:** P1 person modeling

## 1. The Problem

Salesforce and HubSpot use Lead and Contact objects for operational lifecycle stages. One human can have several such records or be converted from one type to another.

## 2. Real-World Example

Synthetic **Sarah Kim** first appears as Salesforce Lead `00Q-17`. After qualification, Salesforce creates Contact `003-82`. HubSpot also has a contact for Sarah. These are three source representations of one human.

## 3. Options We Considered

- Create canonical Lead and Contact entities. This copies CRM lifecycle concepts into the shared identity model.
- Replace the Lead record when conversion occurs. This loses source and conversion history.
- Preserve both as source records and link them to one Person when resolved.

## 4. Our Decision

Lead and Contact are `SourceRecord.source_object_type` values, not canonical Person subtypes. They map to `Person` through `ExternalIdentityCrosswalk`.

## 5. How It Works Technically

- Each source object receives its own `source_record_id`.
- External IDs and conversion lineage remain source evidence.
- Confirmed mappings can point several source records to Sarah's one `person_id`.
- Operational Lead or Contact fields stay with source data rather than Person.

## 6. Why We Chose It

A CRM lifecycle change does not create a new human. Keeping source objects separate preserves operational detail without fragmenting canonical identity.

## 7. Tradeoffs and Limitations

Consumers that need Lead- or Contact-specific fields must join source lineage. Later ingestion and identity phases must preserve conversion events and handle duplicate source records safely.

## 8. Interview Explanation

“I treated Lead and Contact as CRM representations, not different people. Both remain auditable source records and can resolve to the same canonical Person.”
