---
aip: 7
title: Contacts List
author: Alexander Kiselev (@MaaKut)
discussions-to: https://github.com/Adamant-im/AIPs/issues/11
status: Draft
type: Standards
category: ARC
created: 2018-07-30
requires: 3
---

Storing contacts list in the ADAMANT blockchain.

## Simple Summary

Define a way to store contacts list (i.e. user-defined aliases for the ADAMANT addresses) in the blockchain.

## Abstract

In order to improve user experience, ADAMANT clients should provide a way to assign aliases to the addresses user interacts with. These aliases need to be stored in the ADAMANT blockchain in order to be accessible from any ADAMANT client.

## Specification

Contacts list is stored as a JSON-object in the ADAMANT KVS as described in the [AIP-3](https://aips.adamant.im/AIPS/aip-3). The KVS key is `contact_list`.

The keys of the above JSON are ADAMANT UIDs, values are objects, where alias is stored in the `displayName` property.

Example:

```json
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

Note, that contact list may contain sensitive info and, therefore, needs to be encrypted (see [AIP-3](https://aips.adamant.im/AIPS/aip-3)).

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
