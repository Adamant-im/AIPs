---
aip: 10
title: General transaction structure for API calls
author: Dmitriy Soloduhin (@zyuhel), Aleksei Lebedev (@adamant-al)
discussions-to: https://github.com/Adamant-im/AIPs/issues/21
status: Draft
type: Standards
category: ARC
created: 2019-02-12
---

## Simple Summary

Describing general structure of any ADAMANT transaction when communicating with API endpoints.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->

Transaction objects is the only way to write data into ADAMANT blockchain. This AIP aims to standartize transaction object structure. Any specific transaction type must follow this standard.

Transaction objects used when sending data to nodes' endpoints and also returned when quering ADAMANT nodes.

Since Node version [0.4.2](https://github.com/Adamant-im/adamant/releases/tag/v0.4.2) transactions can be sent to unified `/api/transaction` endpoint.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for different platforms.-->

ADAMANT transaction must comply with JSON scheme:

```
{
  "id": TX_ID,
  "type": TX_TYPE,
  "amount": AMOUNT,
  "fee": AMOUNT,
  "senderId": ADAMANT_ID,
  "recipientId": ADAMANT_ID,
  "senderPublicKey": PUBLIC_KEY,
  "asset": JSON_OBJECT,
  "timestamp": ADAMANT_TIMESTAMP,
  "signature": SIGNATURE,
  "confirmations": UNSIGNED INTEGER,
  "height": UNSIGNED INTEGER,
  "blockId": BLOCK_ID
}

```

Format types descriptions:

- `ADAMANT_ID` — ADAMANT address starting with `U`, string
- `ADAMANT_TIMESTAMP` — 32 bit integer epoch timestamp (in seconds starting from Sep 02 2017 17:00:00 GMT+0000)
- `AMOUNT` — tokens quantity in 64 bit integer, 8 decimal points (100000000 equals to 1 ADM)
- `PUBLIC_KEY` — 256 bit public key in hex, string
- `SIGNATURE` — ed25519 signature of SHA256 hash of transaction data in hex, string
- `TX_ID` — reversed first 8 bytes of SHA256 hash of transaction data and signature as integer
- `BLOCK_ID` — reversed first 8 bytes of SHA256 hash of signed block header
- `TX_TYPE` — integer representing [transaction type](#transaction-types)

Explanation of transaction fields:

- `type` — type of transaction. It says node how to interpret it, see below. Mandatory when posting a transaction to node.
- `amount` — amount to transfer. For non-transfer transactions must be `0`. Mandatory when posting a transaction to node.
- `fee` — fee for operation. Depends on `type` of transaction.
- `senderId` — ADAMANT address of sender. Mandatory when posting a transaction to node.
- `senderPublicKey` — public key of sender. Mandatory when posting a transaction to node.
- `recipientId` — ADAMANT address of recepient. Default is null. Mandatory for [chats and transfers (0,8)](#transaction-types).
- `asset` — transaction data specific for different [transaction types](#transaction-types). Used also in signature calculation. If omitted, it is set to empty object `{}`.
- `timestamp` — transaction timestamp. Nodes do not accept transactions stamped in future. Mandatory when posting a transaction to node.
- `signature` — transaction signature. Mandatory when posting a transaction to node.
- `confirmations` — number of confirmations. Must be omitted when posting a transaction to node.
- `height` — block height where transaction forged. Must be omitted when posting a transaction to node.
- `blockId` — block id where transaction forged. Must be omitted when posting a transaction to node.

### Transaction types

Here are reserved types of transactions:

- `0` is for sending tokens
- `1` is for second signature
- `2` is for delegate registration
- `3` is for voting
- `4` is for multisignature
- `8` is for messaging (including Rich content) and In-Chat token transfers
- `9` is for storing data (KVS)

### Examples

``` json
{
  "transaction": {
    "type": 9,
    "amount": 0,
    "senderId": "U11977883563659338220",
    "senderPublicKey": "d2cbc26c2ef6...",
    "asset": {
      "state": {
        "key": "eth:address",
        "value": "0xf4a2d5997eb0575b7ad7c10b0b178524c336f9e9",
        "type": 0
      }
    },
    "timestamp": 45603372,
    "signature": "86cbe525042bf83802..."
  }
}

```

This transaction writes public Ether address for U11977883563659338220 in blockchain.

``` json
{
  "id": "2119283124838898701",
  "height": 1895256,
  "blockId": "8715847094891653948",
  "type": 0,
  "timestamp": 16472909,
  "senderPublicKey": "cdab95b082b9774bd975677c868261618c7ce7bea97d02e0...",
  "senderId": "U15423595369615486571",
  "recipientId": "U11962225491348738609",
  "amount": 49000000,
  "fee": 50000000,
  "signature": "17132c3e62ce2671911e1ca...",
  "signatures": [],
  "confirmations": 5895508,
  "asset": {}
}
```

Transaction returned by ADAMANT node. Transfer of 0.49 ADM from U15423595369615486571 to U11962225491348738609.

## Rationale

Describing current transaction structure will help to develop alternative ADAMANT clients and nodes.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
