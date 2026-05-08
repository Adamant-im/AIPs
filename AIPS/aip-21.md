---
aip: 21
title: Transaction Millisecond Timestamp
author: ADAMANT contributors
discussions-to: https://github.com/Adamant-im/adamant/issues/209
status: Draft
type: Standards
category: Core
created: 2026-05-07
---

## Simple Summary

Add an optional `timestampMs` field to ADAMANT transactions so clients and nodes can preserve millisecond-level creation time without changing transaction IDs or signatures.

## Abstract

ADAMANT transactions currently include `timestamp`, measured in seconds since the ADAMANT epoch. This value is part of transaction signing and cannot be adjusted by a node after a client creates a transaction. In fast messaging flows, several messages can share the same second-level timestamp, so clients and APIs cannot always reconstruct the intended ordering from confirmed transaction data.

This AIP proposes a protocol upgrade that allows transactions to include `timestampMs`, measured in Unix milliseconds. The field is not included in transaction byte serialization, signature calculation, transaction IDs, or hashes. After activation, nodes validate that an included `timestampMs` belongs to the same ADAMANT timestamp second, store it, and return it in transaction API and socket responses. Before activation, nodes must ignore the field on consensus-sensitive paths.

## Motivation

Fast chat conversations can create multiple message transactions within the same second. Sorting by the existing `timestamp` field alone is not precise enough to keep the sender's intended order.

Changing `timestamp` itself, transaction byte serialization, or signature semantics would be a much larger consensus change. It would also risk historical replay compatibility. A soft companion field gives clients better ordering data while preserving existing transaction identity and signature rules.

## Specification

### Field Definition

Transactions MAY include:

```json
{
  "timestampMs": 1724939434123
}
```

`timestampMs` is a Unix timestamp in milliseconds. It describes the client-side transaction creation time.

### Serialization and Identity

`timestampMs` MUST NOT be included in transaction byte serialization.

`timestampMs` MUST NOT affect:

- transaction signatures
- second signatures
- multisignature checks
- transaction IDs
- transaction hashes
- block IDs or block hashes

### Activation

This change MUST be gated by an explicit consensus activation height.

Before activation:

- nodes MUST ignore `timestampMs` during transaction object normalization
- nodes MUST NOT store `timestampMs` from newly processed blocks
- nodes MUST remain compatible with peers and clients that include or omit the field

At and after activation:

- nodes MUST preserve `timestampMs` during transaction object normalization
- nodes MUST validate `timestampMs` when it is present
- nodes MUST store `timestampMs` in transaction storage
- nodes MUST expose `timestampMs` in transaction API responses and socket transaction payloads when the stored value exists

Activation checks MUST be based on the height of the block or transaction validation context being processed. Implementations MUST NOT decide activation for historical block processing solely from the node's current tip height.

### Validation

After activation, when a transaction includes `timestampMs`, the node MUST verify that it belongs to the same ADAMANT timestamp second as `timestamp`.

Let:

- `epochMs` be the ADAMANT epoch in Unix milliseconds
- `timestampMsFromAdamant = epochMs + timestamp * 1000`
- `deltaMs = timestampMs - timestampMsFromAdamant`

The transaction is valid only when:

```text
0 <= deltaMs < 1000
```

This rule preserves consistency between the signed second-level timestamp and the unsigned millisecond companion field.

Nodes MAY keep public API admission checks that compare a newly submitted transaction timestamp with local node time. Such checks are node admission policy and MUST NOT be used for deterministic block replay validity.

### Sorting

When a transaction listing, chat listing, state listing, or unconfirmed transaction merge sorts by `timestamp`, implementations SHOULD sort by:

1. `timestampMs`, when present
2. `timestamp * 1000`, when `timestampMs` is not present

Implementations SHOULD keep the existing `timestamp` sort direction as a secondary tie-breaker.

### Client Behavior

Clients SHOULD include `timestampMs` with every newly created transaction after they know the network supports this AIP.

Clients SHOULD verify that `timestampMs` belongs to the same ADAMANT timestamp second as `timestamp` before trusting transaction data for local ordering.

Clients SHOULD prefer `timestampMs` over `timestamp` when sorting messages and transactions. If `timestampMs` is missing, clients MUST fall back to `timestamp`.

## Rationale

The proposal deliberately keeps `timestampMs` outside transaction signatures and IDs. This avoids changing transaction identity, block identity, and historical replay semantics.

The field is still activation-gated because storing and validating additional transaction data during block processing affects deterministic node behavior. The activation boundary makes upgraded nodes compatible with old chain history and makes the new rule explicit for all implementations.

The validation rule uses the same second as the signed `timestamp` instead of a wider tolerance. This prevents a node or relay from attaching an arbitrary millisecond value that would materially change ordering while keeping the signed second unchanged.

Current-time freshness checks are intentionally left out of consensus validation. A check such as "not more than N seconds in the past" depends on wall-clock time and is not replay-stable. Nodes may use such checks only when accepting new transactions from public APIs.

## Backwards Compatibility

Before activation, upgraded nodes ignore `timestampMs` in consensus-sensitive transaction normalization. This allows old and upgraded nodes to process existing history and pre-activation blocks consistently.

After activation, nodes that have not upgraded will not understand the new validation and storage rules. The activation height therefore requires normal network coordination.

Transactions without `timestampMs` remain valid after activation. This keeps older clients and tools functional, while clients that need millisecond ordering can opt in by including the field.

API consumers must treat `timestampMs` as optional because historical transactions and transactions from older clients may not contain it.

## Test Cases

Implementations of this AIP MUST include tests for:

- transaction byte serialization, signature, ID, and hash stability when `timestampMs` changes
- pre-activation transaction normalization removing `timestampMs`
- post-activation transaction normalization preserving `timestampMs`
- post-activation validation accepting `0 <= deltaMs < 1000`
- post-activation validation rejecting negative or at least 1000 ms deltas
- block processing at the activation boundary using the validated block height
- database storage and API response projection of `timestampMs`
- transaction and chat sorting by `timestampMs` with fallback to `timestamp`
- replay or synchronization of pre-activation history

## Implementation

Reference implementation work is tracked in:

- Node issue: https://github.com/Adamant-im/adamant/issues/209
- Initial node implementation: https://github.com/Adamant-im/adamant/pull/93

Companion updates may be required in:

- ADAMANT API schema
- ADAMANT documentation
- ADAMANT client applications and libraries

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
