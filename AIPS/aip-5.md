---
aip: 5
title: Rich Content Messages
author: Dmitriy Soloduhin (@zyuhel)
discussions-to: https://github.com/Adamant-im/AIPs/issues/8
status: Accepted
type: Standards
category: ARC
created: 2018-06-09
requires: 4
---

Exchanging Rich Content Messages across different clients.

## Simple Summary
Extending messages with Flexible Rich Content.

## Abstract
Define a specification of Rich Content messages, so that various clients can handle them. 


## Specification
Rich messages are encrypted as specified in [AIP-4](https://github.com/Adamant-im/AIPs/blob/master/AIPS/aip-4.md).

To mark message as Rich text, put `2` value in `type` field of transaction asset.

Rich Message must be a JSON object, sent within `message` field of transaction asset. It required to have `type` field with string value, describing how to handle it. Additional AIPs should be created for different Rich Text `type` handlers. Optional field `text_fallback` can be added to show explanation text messages on clients that doesn't support this Message Type. 

Rich messages should be shown in chat streams as ordinary messages, but their content, and theirs rendering process will be defined by corresponding Rich Message `type`.

Clients shouldn't render unsupported or unknown message types, instead they must show a message about unsupported rich message type and/or text fallback if it is available.

## Syntax

Structure of transaction asset:
````
chat: {
  message: "HEXIFIED_ENCRYPTED_MESSAGE"
  own_message: "HEXIFIED_NONCE"
  type: 2
}
````

Field `message` should contain encrypted JSON of Rich Text. Below shown a base structure of Rich Text JSON object. It may have any other fields, only `type` field is required.
````
{
  type: "RICH_MESSAGE_TYPE",
  text_fallback: "OPTIONAL_FALLBACK_FOR_CLIENTS_NOT_SUPPORTING_THIS_MESSAGE_TYPE"
}
````

## Rationale
Rich messages, such as different objects/ media / crypto transfers, should be handled in same way between different clients. 


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
