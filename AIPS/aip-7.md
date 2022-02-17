---
aip: 7
title: Contacts List
author: Alexander Kiselev (@MaaKut)
discussions-to: https://github.com/Adamant-im/AIPs/issues/11
status: Accepted
type: Standards
category: ARC
created: 2018-07-30
requires: 3
---

Storing Contacts list in the ADAMANT blockchain.

## Simple Summary

Define a way to store Contacts (i.e. user-defined aliases for the ADAMANT addresses) in the blockchain.

## Abstract

In order to improve user experience, ADAMANT clients should provide a way to assign aliases to the addresses user interacts with. These aliases needed to be stored in the ADAMANT blockchain in order to be accessible from any ADAMANT client.

## Specification

Contacts list is stored as a JSON object in the ADAMANT KVS as described in the [AIP-3](https://aips.adamant.im/AIPS/aip-3). This object *must be encrypted* and passed as `value` with `key` = `contact_list`.

The keys of the above JSON are ADAMANT UIDs, values are objects, where aliases are stored in the `displayName` property.

Example of `value` object for storing Contact list:

``` json
{
  "U9821606738809290000": {
    "displayName": "John Doe"
  },
  "U9821606738809290001": {
    "displayName": "Jane Doe"
  }
}
```

Other properties besides `displayName` may be added to values to store various contact details.

Note, that contacts lists may contain sensitive info and, therefore, must be encrypted (see [AIP-3](https://aips.adamant.im/AIPS/aip-3)).

### Examples

``` json
{
  "transaction": {
    "type": 9,
    "amount": 0,
    "senderId": "U15677078342684640219",
    "senderPublicKey": "e16e624fd0...",
    "asset": {
      "state": {
        "key": "contact_list",
        "value":"{
          \"message\": \"6df8c172feef228d930130...\",
          \"nonce\": \"f6c7b76d55db945bb026cd221d5...\"}",
        "type": 0
      }
    },
    "timestamp": 45603645,
    "signature": "dbafce549f1..."
  }
}
```

Sends private (encrypted) contact_list records for U15677078342684640219.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
