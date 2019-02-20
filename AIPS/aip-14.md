---
aip: 14
title: Chatrooms API 
author: Dmitriy Soloduhin (@zyuhel), Sergey Ushakov (@sergushakov)
discussions-to: https://github.com/Adamant-im/AIPs/issues/14
status: Draft
type: Standards
category: API
created: 2019-01-21
---

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the AIP.-->
To simplify access to chats and offer one common endpoint for chats and group chats in the future. Current node's REST API need to be extended.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Current API for retriving chats are the same as for general transactions. It leads to inffective work of messaging applications. To get full list of contacts, apps have to download all of transactions for that users. This behaviour is very slow for users with multiple chats. 

Effective solution is to create new chatroom endpoint that offers fetching contacts, chats and paginating through chatrooms.

## Motivation
<!--The motivation is critical for AIPs that want to change the protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the AIP solves. AIP submissions without sufficient motivation may be rejected outright.-->
Chatrooms will make messenger apps faster.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for different platforms.-->
New endpoint `/api/chatrooms` should be added to ADAMANT node. It uses REST notation. 

Let user address is U000000000000 and he communicate with U000000000001. To get user's U000000000000 chats, GET request to `/api/chatrooms/U000000000000` should be sent. To get chat between U000000000000 and U000000000001, GET request to `/api/chatrooms/U000000000000/U000000000001` should be sent.

API endpoint for `/api/chatrooms/U000000000000` returns list of general `chats` structures:

```
"chats": [{
	"lastTransaction": {
		"id": LAST_TRANSACTION_ID,
		"type": LAST_TRANSACTION_TRANSACTION_TYPE,
		"timestamp": LAST_TRANSACTION_TIMESTAMP,
		"senderId": LAST_TRANSACTION_SENDER, 
		"recipientId": LAST_TRANSACTION_RECIPIENT,
		"amount": LAST_TRANSACTION_AMOUNT,
		"asset": LAST_TRANSACTION_ASSET,
	},
	"participants": [ 
		{
			"address": "ADRESS_1",
			"publicKey": "PUBLIC_KEY_1"
		}, {
			"address": "ADDRESS_2",
			"publicKey": "PUBLIC_KEY_2"
		}, {...}
	]
}, {...}
]
```

//ARRAY WITH PARTICIPIANTS IN THIS DIALOG

Available arguments:

* `orderBy` - order by field, same as in transaction list API, by default = timestamp:desc&
* `offset` - offset, same as in transaction list API, by default = 0
* `limit` - limit field
* `withPayments` - should be added, if token transfer transaction should be returned



For specific dialog, list of all transactions in the chat should be returned in `messages` response field. Public keys and address of all participants should be available in `participants` response field. 

Available arguments:

* `orderBy` - order by field, same as in transaction list API, by default = timestamp:desc&
* `offset` - offset, same as in transaction list API, by default = 0
* `limit` - limit field
* `withPayments` - should be added, if token transfer transaction should be returned


## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
Chat clients need to get chats list faster, it will help to start messaging faster. Also to start using chats, clients need to get public keys of all participants, so to minimize number of request, it is better to return public key with chat lists. 
And because token transfers shown in some clients, for them to get benefits of using new API, withTransfers key was added


## Backwards Compatibility
<!--All AIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The AIP must explain how the author proposes to deal with these incompatibilities. AIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
This API improvement will add new API endpoint, and won't change backward compatibility for any previous clients.



## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
