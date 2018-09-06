---
aip: 6
title: Signal Messages
author: Dmitriy Soloduhin (@zyuhel)
discussions-to: https://github.com/Adamant-im/AIPs/issues/9
status: Draft
type: Standards
category: ARC
created: 2018-06-09
requires: 4

---

Hidden (system) messages for different services.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Sometimes it is needed to send encrypted messages, that shouldn't appear in chats (hidden). This system messages will be called Signals. Signals should be used for User-Service or Service-Service communications. Example is [Register for Push notifications message](https://medium.com/adamant-im/adamant-is-working-on-blockchain-messaging-platform-and-push-notifications-service-765972cce50e).


## Specification

Signal messages follows encryption algorithm defined in [AIP-4](https://aips.adamant.im/AIPS/aip-4). 
Chat `type` of signal messages is `3`. This chat type should not be returned on chat endpoints on any compliant node, unless request for this chat type is forced by corresponding filter.

This type of messages should not be visible in dialogs. 


## Syntax

Structure of transaction asset:
````
chat: {
  message: "HEXIFIED_ENCRYPTED_MESSAGE"
  own_message: "HEXIFIED_NONCE"
  type: 3
}
````

## Rationale
Some sort of client-service communication is needed for internal processes, such as Push server registration. This process should take place on ADAMANT network itself, instead of relying of some third party transfer medium. This type of messages should follow all security guidelines, and must not distrurb users with technic data. 


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
