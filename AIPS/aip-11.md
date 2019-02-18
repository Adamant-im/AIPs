---
aip: 11
title: Behavior for KVS data
author: Aleksei Lebedev
discussions-to: https://github.com/Adamant-im/AIPs/issues/27
requires: 3
extends: https://aips.adamant.im/AIPS/aip-3
status: Draft
type: Standards
category: ARC
created: 2019-02-18
---

## Simple Summary
Describe how clients should process stored data of different contents, allowing to write and read it faster.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
As [AIP-3](https://aips.adamant.im/AIPS/aip-3) stands, users save its public and private information in ADAMANT blockchain. To write and read it faster and cheaper, different types of records should be introduced, including full re-write and incremental.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for different platforms.-->

When sending KVS transaction, `type` field of `state` object must be as follows:
- `0` for full re-writing of contents by specified `key` value
- `1` for incremental appending contents

Client apps should decide wether to use `0` or `1` write type by themselves. It depends on content type `key`, and volume of updated information. For example, if user updates one's contact name, it is faster and cheaper to store only updated record with `1` type. When significant quantity of contact names updated, it is reasonable to re-write all of contacl list with single store transaction with `0` type.

Nodes API should process KVS properly. Values `type` and `key` are public, so node must filter and return KVS records on client's demand. This will reduce traffic and processing time. For every `key` value, node must return latest transactions with `0` type, following updates with `1` type.

Client app should process received KVS records in reasonable way.


### Examples


```
{
"transaction":{
  "type": 9,
  "amount": 0,
  "senderId": "U15677078342684640219",
  "senderPublicKey": "e16e624fd0...",
  "asset":{
    "state":{
      "key": "contact_list",
      "value":"{
        \"message\": \"6df8c172feef228d930130...\",
        \"nonce\": \"f6c7b76d55db945bb026cd221d5...\"}",
        "type": 0}
    },
  "timestamp": 45603645,
  "signature": "dbafce549f1..."}
}
```

Sends private (encrypted) full contact list for U15677078342684640219.

```
{
"transaction":{
  "type": 9,
  "amount": 0,
  "senderId": "U15677078342684640219",
  "senderPublicKey": "e16e624fd0...",
  "asset":{
    "state":{
      "key": "contact_list",
      "value":"{
        \"message\": \"6df8c172feef228d930130...\",
        \"nonce\": \"f6c7b76d55db945bb026cd221d5...\"}",
        "type": 1}
    },
  "timestamp": 45603645,
  "signature": "dbafce549f1..."}
}
```

Sends private (encrypted) updated contact records for U15677078342684640219.

## Rationale
Using `type` of storing and reading KVS offers perfonace upgrade for client apps.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

