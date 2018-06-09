---
aip: <to be assigned>
title: Rich Messages
author: Dmitriy Soloduhin (@zyuhel)
discussions-to: 
status: Draft
type: Standards
category: ARC
created: 2018-06-09
requires: 4
---

Exchanging Rich Messages

## Simple Summary
Extending messages with Flexible Rich Content.

## Abstract
Define a specification of Rich Content messages, so that various client have base specification for extension. 


## Specification
Rich messages are encrypted as in [AIP-4](https://github.com/Adamant-im/AIPs/blob/master/AIPS/aip-4.md). This document mostly covers internal structure of encrypted messages. Rich Message must be JSON object.
It required to have `type` field. To be approved in different messages, Rich Message Type string should be defined as new AIP describing it's internal structure. Optional field `text_fallback` can be added, for showing messages on clients that doesn't support this Message Type. Rich messages should be shown in chat stream as ordinary messages, but their content, and theirs rendering process will be defined by corresponding Rich Message Type.
Clients shouldn't render unsupported or unknown message types, instead they must show a message about unsupported rich message type and/or text fallback if it is available.

## Syntax

Structure of transaction asset
````
chat: {
  message: "HEXIFIED_ENCRYPTED_MESSAGE"
  own_message: "HEXIFIED_NONCE"
  type: 2
}
````

Base structure of JSON object. It can have any other fields, only type field is required.
````
{
  type: "RICH_MESSAGE_TYPE",
  text_fallback: "OPTIONAL_FALLBACK_FOR_CLIENTS_NOT_SUPPORTING_THIS_MESSAGE_TYPE"
}
````

## Rationale
Rich messages, such as different objects/media, should follow one standard between different clients, to make intercommunication possible. 


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
