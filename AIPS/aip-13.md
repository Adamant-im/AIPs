---
aip: 13
title: Public wallet addresses
author: Aleksei Lebedev
discussions-to: https://github.com/Adamant-im/AIPs/issues/31
status: Draft
type: Standards
category: ARC
created: 2019-02-20
requires: 3
---

Storing public wallet addresses in the ADAMANT blockchain.

## Simple Summary

Define a way to store wallet addresses (i.e. Ether, DOGE) in the blockchain.

## Abstract

Transferring tokens in chats is one of essential ADAMANT features. It allows to send different external (not ADM) tokens to interlocutors without specifying any token addresses. To make storing and sending tokens secure, only user has control over private keys for these tokens. See [ADAMANT Messenger: Secure ETH Transfers](https://medium.com/adamant-im/adamant-messenger-secure-eth-transfers-b27984a3ce05) for details.

In chat payments are possible due to public wallet addresses, stored in blockchain, so any client app can fetch them and use as recepient in token's blockchain.

## Specification

Public wallet addresses are stored as JSON objects in the ADAMANT KVS as described in the [AIP-3](https://aips.adamant.im/AIPS/aip-3). This objects *must be public* and passed as `value` with `key` like `ticker:address`.

### Examples

```
{
"transaction":{
  "type": 9,
  "amount": 0,
  "senderId": "U11977883563659338220",
  "senderPublicKey":"d2cbc26c2ef6...",
  "asset":{
    "state":{
      "key": "eth:address",
      "value": "0xf4a2d5997eb0575b7ad7c10b0b178524c336f9e9",
      "type": 0
      }
    },
  "timestamp": 45603372,
  "signature": "86cbe525042bf83802..."}
}

```

This transaction write public Ether address for U11977883563659338220.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
