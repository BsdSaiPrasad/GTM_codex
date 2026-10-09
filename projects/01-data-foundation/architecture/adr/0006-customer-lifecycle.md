# ADR-0006: Customer as Account Lifecycle State

- **Decision ID:** D006
- **Status:** Accepted
- **Scope:** P1 commercial identity

## Context

Creating a separate Customer company record duplicates Account identity, while agreements and entitlements have lifecycles that must not be collapsed into a company label.

## Decision

Model customer status on Account. Keep Contract and Subscription as independent entities with their own durable identities and histories.

## Consequences

All organization facts share one Account identity and commercial history remains explicit. The authoritative evidence and timing rules that derive customer status remain a governed future decision.
