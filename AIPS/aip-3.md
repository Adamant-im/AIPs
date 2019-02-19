---
aip: 3
title: Storing data in chain (KVS)
author: Dmitriy Soloduhin (@zyuhel), Aleksei Lebedev(@adamant-al)
discussions-to: https://github.com/Adamant-im/AIPs/issues/5
requires: 10
status: Accepted
type: Standards
category: ARC
created: 2018-05-19
---

Storing sensitive (encrypted) and shared (unencrypted) data in ADAMANT blockchain.

## Simple Summary
Define ways of storing data in blockchain, so that different clients has understanding of how to handle it.


## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Clients need to save different data across different devices. A standartized way of saving and retrieving data will help enchance user experience. Explanation and example of usage: [What is ‘Key-Value Store’ in ADAMANT and How is it used to Store Contact Names?](https://medium.com/adamant-im/what-is-key-value-store-in-adamant-and-how-is-it-used-to-store-contact-names-4ee5f82ab77f)


## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for different platforms.-->
Data storage should use State storing (KVS) functionality added in [v0.2.0 node release](https://github.com/Adamant-im/adamant/releases/tag/v0.2.0).
Selected key should be choosen according usage. If some key is going to be reused across different applications, corresponding AIP should be created, to ensure that all application will handle it similarly.

Unencrypted values are stored as-is, no additional actions are needed.

If a value needs to be encrypted, the following logic is to be applied:
1. Wrap the value into JSON: ```{ "payload": <your value> }```
2. Convert the above JSON to string; prefix and suffix the stringified JSON with a random string (alphanumeric ASCII-chars recommended; do not use `{` or `}`)
3. Encrypt the resulting string using [NaCL.secretbox](https://nacl.cr.yp.to/secretbox.html). Secret key is a SHA-256 hash of the ADAMANT private key.
4. Resulting nonce and encrypted message are wrapped into JSON: ```{ "message": <encrypted message>, "nonce": <nonce> }```, which is then saved into the KVS.

Any KVS value that is a valid JSON and has both `nonce` and `message` fields should be treated as encrypted according to the above algorithm.

## Syntax

Transaction sytax is based on [AIP-10: General transaction structure for API calls](https://aips.adamant.im/AIPS/aip-10). KVS transaction type is 9.

Transaction `asset` object  must include single `state` object, which represents key-value pairs and store type:

"asset":{
    "state":{
      "key": KEY_NAME,
      "value": VALUE,
      "type": KEY_TYPE
    }
}

Contents explained:
- `key` describes contents of KVS record. Possible values must be described in future AIPs. String, mandatory.
- `value` is data for `key`. Mandatory, type depends on contents.
- `type` points how to process KVS records for this `key`. It may be incremental or full re-write of previous values. Must be described in future AIPs. Integer, mandatory. Default `0`.

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

Sends private (encrypted) contact_list records for U15677078342684640219.

## Rationale
Using two different systems for separate storage of public and sensitive data can be considered as not rationale. It should be clear that clients should distinguish between secret and public data, to know that data should be decoded. Public data could have some identifiers that are not efficient to store as JSON, because they are just plain strings or some data that can be written in string format. So ability to store strings and JSON objects should be considered as right way. Nodes shouldn't interfere with data stored in objects. So all data handling should be done on client.

Regarding encryption, most of KVS stored data is needed for use of one user, holder of the private/secret key of an account. So using NaCL public-key authenticated encryption (box) has no sense. To not introduce new encryption libraries, we can use Secret-key authenticated encryption (secretbox), using SHA-256 of private key as secret key.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
