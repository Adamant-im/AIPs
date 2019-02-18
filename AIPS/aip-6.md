---
aip: 6
title: Signal Messages
author: Dmitriy Soloduhin (@zyuhel)
discussions-to: https://github.com/Adamant-im/AIPs/issues/9
status: Accepted
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

Signal messages should be sent to `recipientId` that process this of message.

## Syntax

Structure of transaction `asset`:
````
chat: {
  message: "HEXIFIED_ENCRYPTED_MESSAGE"
  own_message: "HEXIFIED_NONCE"
  type: 3
}
````

### Examples

````
{
"transaction":{
	"type": 8,
	"amount": 0,
	"senderId": "U15677078342684640219",
	"senderPublicKey": "e16e624fd0a5123294b448c21f30a07a043...",
	"asset":{
		"chat":{
		"message": "70cbd07ff2ceaf0fc38a01ef9...",
		"own_message": "e98794eaedf47e...",
		"type": 3}
		},
	"recipientId": "U10629337621822775991",
	"timestamp": 46116887,
	"signature": "8fc2a54604109a6fcdccec2..."}
}

````

Subscribes user iOS device for Push notifications. U10629337621822775991 is general iOS [Push Notification Service](https://github.com/Adamant-im/adamant-notificationService) address.

## Rationale
Some sort of client-service communication is needed for internal processes, such as Push server registration. This process should take place on ADAMANT network itself, instead of relying of some third party transfer medium. This type of messages should follow all security guidelines, and must not distrurb users with technic data. 

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
