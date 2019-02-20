---
aip: 12
title: Non-ADM crypto transfer messages
author: Aleksei Lebedev (@adamant-al)
discussions-to: https://github.com/Adamant-im/AIPs/issues/29
requires: 5
extends: https://aips.adamant.im/AIPS/aip-5
status: Draft
type: Standards
category: ARC
created: 2019-02-20
---

## Simple Summary
Describes behaviour for transferring different tokens in chats.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Transferring external (non-ADM) tokens in chats is possible due to [public storage of wallet addresses in blockcahin, AIP 13](https://aips.adamant.im/AIPS/aip-13). 

As such transactions actually goes through own tokens' networks, special ADM messages needed to show them in chats.

## Motivation

To make sure all ADAMANT Messenger apps process in-chats crypto transfers same way, a standard needed.

## Specification

General way of making in-chat transfer:
- Send ADM rich message describing this transaction according to [AIP-5](https://aips.adamant.im/AIPS/aip-5). See syntax below.
- Send external token transaction in its own network
- Watch for external token transaction in its own network and update its status

Clients must support four types of transactions statuses both of incoming and outgoing transfers until final status reached:
- `Pending` — transaction is not confirmed yet
- `Confirmed` — transaction is confirmed on token's network. Final status.
- `Cancelled` — transaction is cancelled or not accepted on token's network. Final status.
- `Inconsistent` — information about transaction, fetched from rich message in ADAMANT newtwork, differs from information, fetched from token's network. It relies to amount, recepient and senders ids, and timestamp. Amount should be compared with meaningful accuracy (e. g., 0.00000001 Ether and 0.0000009998 Ether is the same). Timestamp should be compared with 24 hour accurancy. Final status.

Clients shouldn't render unsupported or unknown crypto transfers, instead they must show a message about unsupported rich message type and/or text fallback if it is available.

## Syntax

Field `message` must contain *encrypted* JSON of transaction info, including ticker symbol, amount, comments and transaction id.

Below shown structure of `message` object:

````
{
	type: String
	text_fallback: String
	amount: String
	comments: String
	hash: String
}
````

Object's fields as described:
- `type` represents token's network and looks like `tickerSymbol_transaction`, e.g., `ETH_transaction`. Mandatory.
- `amount` — transferred value in tokens of its network. Decimal separator is `.`. Mandatory.
- `comments` — optional, and may include comment for this transfer, shown to both recepient and sender.
- `hash` — transaction id in token's network. Used to check transaction status. Mandatory.
- `text_fallback` can be added to show explanation text messages on client apps that doesn't support specified `type`. 

### Examples

````
{
	type: "ETH_transaction"
	text_fallback: "OPTIONAL_FALLBACK_FOR_CLIENTS_NOT_SUPPORTING_THIS_MESSAGE_TYPE"
	amount: "0.002"
	comments: "I like to send it, send it"
	hash: "0xfa46d2b3c99878f1f9863fcbdb0bc27d220d7065c6528543cbb83ced84487deb"
}
````

Object `message` *before encryption*, sending 0.002 Ethers with comment "I like to send it, send it".

````
{
  "transaction": {
    "type": 8,
    "amount": 0,
    "senderId": "U15677078342684640219",
    "senderPublicKey": "e16e624fd0a5123294b448c21f30a07a0435533c693b146b14e66830e4e20404",
    "asset": {
      "chat": {
        "message": "70cbd07ff2ceaf0fc38a01ef9...",
        "own_message": "e98794eaedf47e...",
        "type": 2
      }
    },
    "recipientId": "U7972131227889954319",
    "timestamp": 46116887,
    "signature": "8fc2a54604109a6fcdccec2..."
  }
}
````

Sends Rich message describing In-Chat Ether transfer from U15677078342684640219 to U7972131227889954319.

## Rationale
Crypto transfers must be handled in same way between different clients. 

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
