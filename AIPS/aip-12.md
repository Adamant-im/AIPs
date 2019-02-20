---
aip: 12
title: Crypto transfers messages
author: Aleksei Lebedev
discussions-to: https://github.com/Adamant-im/AIPs/issues/29
requires: 5
extends: https://aips.adamant.im/AIPS/aip-5
status: Draft
type: Standards
category: ARC
created: 2019-02-20
---

## Simple Summary
Describes behaviour for transferryg different tokens in chats.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Transferring external (not ADM) tokens in chats is possible due to [public storage of wallet addresses in blockcahin, AIP 13](https://aips.adamant.im/AIPS/aip-13).

As such transactions actually goes through own tokens' networks, special ADM messages needed to show them in chats. To make sure all ADAMANT Messenger apps process in-chats crypto transfers same way, a standard needed.

## Specification

General way of making in-chat transfer:
- Send ADM rich message describing this transaction according to [AIP-5](https://aips.adamant.im/AIPS/aip-5). See syntax below.
- Send external token transaction in its own network
- Watch for external token transaction in its own network and update its status

Clients must support four types of transactions statuses both of incoming and outgoing transfers until final status reached:
- `Pending` — transaction is not confirmed yet
- `Confirmed` — transaction is confirmed on token's network. Final status.
- `Cancelled` — transaction is cancelled or not accepted on token's network. Final status.
- `Inconsistent` — ADAMANT message information about transaction differs from information, fetched from token's network. It relies to amount, recepient and senders ids, and timestamp. Amount should be compared with meaningful accurancy (e. g., 0.00000001 Ether and 0.0000009998 Ether is the same). Timestamp should be compared with 24 hour accurancy. Final status.

C



Clients shouldn't render unsupported or unknown message types, instead they must show a message about unsupported rich message type and/or text fallback if it is available.

## Syntax

Field `message` must contain *encrypted* JSON of transaction info, including ticker, amount and transaction id.

Optional field `text_fallback` can be added to show explanation text messages on clients that doesn't support this Message Type. 

Below shown a base structure of Rich Text JSON object.

````
{
  type: "RICH_MESSAGE_TYPE",
  text_fallback: "OPTIONAL_FALLBACK_FOR_CLIENTS_NOT_SUPPORTING_THIS_MESSAGE_TYPE"
}
````

### Examples

````
{
"transaction":{
	"type": 8,
	"amount": 0,
	"senderId": "U15677078342684640219",
	"senderPublicKey": "e16e624fd0a5123294b448c21f30a07a0435533c693b146b14e66830e4e20404",
	"asset":{
		"chat":{
		"message": "70cbd07ff2ceaf0fc38a01ef9...",
		"own_message": "e98794eaedf47e...",
		"type": 2}
		},
	"recipientId": "U7972131227889954319",
	"timestamp": 46116887,
	"signature": "8fc2a54604109a6fcdccec2..."}
}
````

Sends Rich message describing In-Chat Ether transfer.

## Rationale
Rich messages, such as different objects/ media / crypto transfers, should be handled in same way between different clients. 

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
