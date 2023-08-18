---
aip: 17
title: Reactions to Messages
author: Aleksei Lebedev (@adamant-al)
discussions-to: 
requires: 5
extends: https://aips.adamant.im/AIPS/aip-5
status: Draft
type: Standards
category: ARC
created: 2022-06-24
---

## Simple Summary

This proposal introduces the ability to add reactions to messages in ADAMANT Messenger apps.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
This AIP outlines the specification for enabling users to react to messages within the ADAMANT Messenger app. Reactions are represented as emoji-based responses to specific messages, enhancing the interactive and expressive capabilities of the messaging platform.

## Motivation

To establish a consistent and standardized approach for implementing message reactions in ADAMANT Messenger apps, a comprehensive specification is necessary.

## Specification

This AIP introduces the concept of message reactions in ADAMANT Messenger apps. Reactions allow users to respond to specific messages using emoji-based expressions.

When a user wishes to react to a specific message, the client must send ADM rich message as described in [AIP-5](https://aips.adamant.im/AIPS/aip-5) and include a new field called `reactto_id` in the message structure. See syntax below.

## Syntax

According to AIP-5, field `transaction.asset.chat.message` must contain *encrypted stringified* JSON. For reactions, this JSON includes `reactto_id` and `react_message`, representing the chosen emoji reaction.

Below shown structure of `message` object:

````
{
  reactto_id: String
  react_message: String
}
````

Object's fields as described:

- `reactto_id` - Represents the ADM transaction ID of the message to which the user is reacting. This field is mandatory.
- `react_message` - Denotes the chosen emoji reaction. This field is also mandatory.


Users can attach multiple reactions to a single message, with each new reaction replacing the previous one. To remove a reaction, users can send an empty value for the `react_message` field:

````
{
  "reactto_id": "Transaction_ID",
  "react_message": ""
}
````

Reaction transaction is always a message, type `8`. See [AIP-10](https://aips.adamant.im/AIPS/aip-10#transaction-types)

### Examples

Object `transaction.asset.chat.message` *before encryption*, sending reaction to a message:

```` json
{
  "reactto_id": "Transaction_ID",
  "react_message": "👍"
}
````

Example of removing a reaction:

```` json
{
  "reactto_id": "Transaction_ID",
  "react_message": ""
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

The introduction of reactions adds a layer of interactivity and engagement to ADAMANT Messenger, allowing users to express sentiments quickly without the need for extensive text-based replies. A standardized approach ensures consistent behavior across different ADAMANT Messenger apps.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
