---
aip: 10
title: General transaction structure
author: Aleksei Lebedev
discussions-to: https://github.com/Adamant-im/AIPs/issues/21
status: Draft
type: Standards
category: ARC
created: 2019-02-12
---

## Simple Summary
Describing general structure of any ADAMANT transaction.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->

Transaction is the only way to write data into ADAMANT blockcahin. This AIP aims to standartize transaction structure. Any specific transaction type must follow this standard.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for different platforms.-->

ADAMANT transaction must comply with JSON scheme:
```
{
"transaction":{
  "type": TX_TYPE,
  "amount": AMOUNT,
  "senderId": ADAMANT_ID,
  "senderPublicKey": PUBLIC_KEY,
  "asset": JSON_OBJECT,
  "timestamp": UNIXTIME,
  "signature": SIGNATURE}
}

```

Transaction must be sent to any live ADAMANT node for validation via node's API endpoint. Endpoint name may differs for different `type` values. Node version 0.5.0 allows any transaction to be sent to `/api/transactions` endpoint.

Node uses SHA-256 for Tx validation.

Transaction keys explained:
- `type` is a type of transaction. It says node how to interpret it. Integer, mandatory.
- `amount` is an integer value in 1/10^8 ADM for transferring. If transaction is not a trasfer, value `0` must be provided.
- `senderId` is ADAMANT address of Tx sender. String, mandatory.
- `senderPublicKey` is ADAMANT public key of Tx sender. String, mandatory.
- `asset` is data to be processed by node. Its structure depends on `type`. String, optional.
- `timestamp` is client's timestamp. Node compares its own and client's timestamp while validating Tx. Unixtime, mandatory.
- `signature` is digital signature which allows node to validate Tx sender. String, mandatory.

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
      "key":
      "eth:address",
      "value":"0xf4a2d5997eb0575b7ad7c10b0b178524c336f9e9",
      "type":0
      }
    },
  "timestamp":45603372,
  "signature":"86cbe525042bf83802..."}
}

```

This transaction writes public Ether address for U11977883563659338220.

## Rationale

JSON transaction structure with described keys offers blockchain nodes to process any type of transactions in unified way.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
