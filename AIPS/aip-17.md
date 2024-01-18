---
aip: 17
title: Reactions to Messages
author: Aleksei Lebedev (@adamant-al), Stanislav Jelezoglo (@StanislavDevIOS)
discussions-to: https://github.com/Adamant-im/AIPs/issues/52
requires: 5
extends: https://aips.adamant.im/AIPS/aip-5
status: Accepted
type: Standards
category: ARC
created: 2023-05-24
---

## Simple Summary

This proposal introduces the ability to add reactions to messages in ADAMANT Messenger apps.

## Abstract

Reactions are represented as emoji-based responses to specific messages, enhancing the interactive and expressive capabilities of the messaging platform.

## Motivation

To establish a consistent and standardized approach for implementing message reactions in ADAMANT Messenger apps, a comprehensive specification is necessary.

## Specification

When a user wishes to react to a specific message, the client must send the ADM rich message as described in [AIP-5](https://aips.adamant.im/AIPS/aip-5) and include a new field called `reactto_id` in the message structure. See the syntax below.

## Syntax

According to AIP-5, the field `transaction.asset.chat.message` must contain *encrypted stringified* JSON. For reactions, this JSON includes `reactto_id` and `react_message`, representing the chosen emoji reaction.

Below is the structure of the `message` object:

````
{
  reactto_id: String
  react_message: String
}
````

Object's fields as described:

- `reactto_id` — Represents the ADM transaction ID of the message to which the user is reacting. This field is mandatory.
- `react_message` — Denotes the chosen emoji reaction. This field is also mandatory.

Users can attach multiple reactions to a single message, *with each new reaction replacing the previous one*. A user's reaction to a specific message will be `react_message` with the latest tx `timestamp`. To remove a reaction, users can send an empty value for the `react_message` field:

```` json
{
  "reactto_id": "7452709338464950789",
  "react_message": ""
}
````

In a dialog, both participants can react to the same message, and an app will show both of them. How to display them is at the discretion of the application.

Reaction transaction is always a message, type `8`. See [AIP-10](https://aips.adamant.im/AIPS/aip-10#transaction-types)

### Examples

Object `transaction.asset.chat.message` *before encryption*, sending reaction to a message:

```` json
{
  "reactto_id": "7452709338464950789",
  "react_message": "👍"
}
````

Example of removing a reaction:

```` json
{
  "reactto_id": "7452709338464950789",
  "react_message": ""
}
````

Full transaction after encryption from U15677078342684640219 to U7972131227889954319 looks like:

```` json
{
  "transaction": {
    "type": 8,
    "amount": 0, // Amount should be 0
    "senderId": "U15677078342684640219",
    "senderPublicKey": "e16e624fd0a5123294b448c21f30a07a0435533c693b146b14e66830e4e20404",
    "asset": {
      "chat": {
        "message": "70cbd07ff2ceaf0fc38a01ef9...", // `reactto_id` and `react_message` encrypted
        "own_message": "e98794eaedf47e...",
        "type": 2 // Type should be 2 (Rich Content Message)
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
