---
aip: 16
title: Quote/reply messages
author: Aleksei Lebedev (@adamant-al)
discussions-to: https://github.com/Adamant-im/AIPs/issues/48
requires: 5
extends: https://aips.adamant.im/AIPS/aip-5
status: Accepted
type: Standards
category: ARC
created: 2022-02-18
---

## Simple Summary

Adds Quote/Reply-to-message possibility for ADAMANT Messenger apps.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
A message sent in reply to another message must be stored with the reply-to identifier.

## Motivation

A standard is needed to ensure all ADAMANT Messenger apps interpret Quote/Reply-to messages in the same way.

## Specification

When sending a message in reply to another message (quote), a client must send the ADM rich message as described in [AIP-5](https://aips.adamant.im/AIPS/aip-5) and include `replyto_id` field. See the syntax below.

A client that receives such a message can process it in different ways. A typical scenario:

- Decrypt a message (identified by `id`) and get `replyto_id`
- If `replyto_id` message (transaction) is not stored in a local database, fetch it from the blockchain and decrypt
- In chat, show message `id` with `replyto_id` message text as quoted
- Check if the local database stores all the message history since the `replyto_id` message, and fetch them, if not. It's important because there should be no gap between locally stored message and the `replyto_id` message.
- Click/tap quoted text: Scroll to `replyto_id` message
- Consider that the `replyto_id` message may not exist, or belongs th another sender, or is not decryptable
- Consider that the `replyto_id` message may be an ADM simple transfer

## Syntax

According to AIP-5, field `transaction.asset.chat.message` must contain *encrypted stringified* JSON. For quote/reply, this JSON includes `replyto_id` and the message itself.

Below is the structure of the `message` object:

````
{
  replyto_id: String
  reply_message: String
}
````

Object's fields as described:

- `replyto_id` — ADM transaction ID of a message to which a user replies. Mandatory.
- `reply_message` — Text of a reply. It may include nested JSON (i. e. Crypto transfer). Mandatory.

Reply transaction is always a message, type `8`. See [AIP-10](https://aips.adamant.im/AIPS/aip-10#transaction-types). In case of `amount` > `0`, a reply is in-chat ADM transfer with comment.

### Examples

Object `transaction.asset.chat.message` *before encryption*, sending "I've got it. Will be there in time." message in reply to 7452709338464950789 ADM transaction:

```` json
{
  "replyto_id": "7452709338464950789",
  "reply_message": "I've got it. Will be there in time."
}
````

Object `transaction.asset.chat.message` *before encryption*, sending 0.002 ETH in reply to 7452709338464950789 ADM transaction:

```` json
{
  "replyto_id": "7452709338464950789",
  "reply_message": {
    "type": "eth_transaction",
    "amount": "0.002",
    "comments": "I like to send it, send it",
    "hash": "0xfa46d2b3c99878f1f9863fcbdb0bc27d220d7065c6528543cbb83ced84487deb"
  },
}
````

Full transaction after encryption from U15677078342684640219 to U7972131227889954319 looks like:

```` json
{
  "transaction": {
    "type": 8,
    "amount": 0, // In case of amount > 0, reply is in-chat ADM transfer with comment
    "senderId": "U15677078342684640219",
    "senderPublicKey": "e16e624fd0a5123294b448c21f30a07a0435533c693b146b14e66830e4e20404",
    "asset": {
      "chat": {
        "message": "70cbd07ff2ceaf0fc38a01ef9...",
        "own_message": "e98794eaedf47e...",
        "type": 2 // 1 for Basic Encrypted Message
      }
    },
    "recipientId": "U7972131227889954319",
    "timestamp": 46116887,
    "signature": "8fc2a54604109a6fcdccec2..."
  }
}
````

## Rationale

Quote/Reply-to messages must be handled in the same way across different clients.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
