---
aip: 14
title: Chatrooms API 
author: Dmitriy Soloduhin (@zyuhel), Sergey Ushakov (@sergushakov), Aleksei Lebedev (@adamant-al)
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
		"id": TX_ID,
		"type": TX_TYPE,
		"timestamp": ADAMANT_TIMESTAMP,
		"senderPublicKey": PUBLIC_KEY,
		"senderId": ADAMANT_ID, 
		"recipientId": ADAMANT_ID,
		"recipientPublicKey": PUBLIC_KEY,
		"amount": AMOUNT,
		"fee": AMOUNT,
		"signatures": [array of SIGNATURE],
  		"confirmations": UNSIGNED INTEGER,
		"asset": JSON_OBJECT,
	},
	"participants": [ 
		{
			"address": "ADAMANT_ID",
			"publicKey": "PUBLIC_KEY"
		}, {
			"address": "ADAMANT_ID",
			"publicKey": "PUBLIC_KEY"
		}, {...}
	]
}, {...}
]
```

Format types and fields described in [AIP-10: General transaction structure for API calls](https://aips.adamant.im/AIPS/aip-10#specification). Array `participants` includes both sender's and recepient's addresses and public keys for chat dialog. Object `lastTransaction` contains information about last transaction with specific participant, including `asset` data.

Along with `chats` array, endpoint returns `count` of chats integer value.

List of all transactions in chat with specific recipient `/api/chatrooms/U000000000000/U000000000001` is returned in `messages` response field.

Available arguments:

* `orderBy` — order by field, same as in [transactions list API](https://github.com/Adamant-im/adamant-console/wiki/Available-Commands#transactions), default is `timestamp:desc&`
* `offset` — offset, same as in transactions list API, default is `0`
* `limit` — limit field, same as in transactions list API, default `HUI_HUI`
* `withoutPayments` — should be added, if transfer type `0` should not be returned

## Examples
```
{
	"success": true,
	"nodeTimestamp": 46531132,
	"chats": [{
		"id": "7452709338464950789",
		"type": 8,
		"timestamp": 25726425,
		"senderPublicKey": "ce67b26e33391dd88ea2bc0173256b9c68a038888fc3de5b8b9887581ddd3239",
		"senderId": "U10879844458425463425",
		"recipientId": "U7972131227889954319",
		"recipientPublicKey": "d2885bc8d4aa68f0f4c919077c1edcb9c9020a715f20cb6db7578cd6f68055bb",
		"amount": 0,
		"fee": 100000,
		"signatures": [],
		"confirmations": null,
		"stored_value": null,
		"stored_key": null,
		"asset": {
			"chat": {
				"message": "7b1e6e21f1f8e4be646133d5814b2ca5c60c0a9bac",
				"own_message": "2a6b950be00ac83d289853251491486d0d403c81e5ce1213",
				"type": 1
			}
		},
		"participants": [{
			"address": "U10879844458425463425",
			"publicKey": "ce67b26e33391dd88ea2bc0173256b9c68a038888fc3de5b8b9887581ddd3239"
		}, {
			"address": "U7972131227889954319",
			"publicKey": "d2885bc8d4aa68f0f4c919077c1edcb9c9020a715f20cb6db7578cd6f68055bb"
		}]
	}, {...}
	],
	"count": "63"
}
```

Result of `api/chatrooms/U7972131227889954319` request.

```

{
	"success": true,
	"nodeTimestamp": 46534772,
	"messages": [{
		"id": "10194689778297980241",
		"height": 840833,
		"blockId": "18442279251222439707",
		"type": 8,
		"timestamp": 11049579,
		"senderPublicKey": "d2885bc8d4aa68f0f4c919077c1edcb9c9020a715f20cb6db7578cd6f68055bb",
		"requesterPublicKey": null,
		"senderId": "U7972131227889954319",
		"recipientId": "U15677078342684640219",
		"recipientPublicKey": "e16e624fd0a5123294b448c21f30a07a0435533c693b146b14e66830e4e20404",
		"amount": 0,
		"fee": 500000,
		"signature": "0e5651909e77adb0bef384884e1f09c006b9403c1c9abeb36832df5c1fedeca6b5f2771bf84b273d27580e440acd823e6fbb3d2a55bf033512adc25711832501",
		"signSignature": null,
		"signatures": [],
		"confirmations": null,
		"stored_value": null,
		"stored_key": null,
		"asset": {
			"chat": {
				"message": "579636a8943c5f766e3bbbfaf75528d8a9e9b89878c0551c7f887f35c51034f32ba8bad35495e3f2c95af977c9b08d7b86c160c97456d1e426",
				"own_message": "a13976c2635255df4d96ad412f60630478e146b9d6fe82db",
				"type": 0
			}
		}
	}, {...}
	],
	"participants": [{
		"address": "U7972131227889954319",
		"publicKey": "d2885bc8d4aa68f0f4c919077c1edcb9c9020a715f20cb6db7578cd6f68055bb"
	}, {
		"address": "U15677078342684640219",
		"publicKey": "e16e624fd0a5123294b448c21f30a07a0435533c693b146b14e66830e4e20404"
	}],
	"count": "341"
}	
```

Result of `api/chatrooms/U7972131227889954319/U15677078342684640219` request.


## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
Messenger clients need to get chats faster. As clients need to know public keys of all chats participants, it is rational to return them in requests.  Token transfers may be hidden in some clients, so `withoutPayments` parameter added. `offset` and `limit` parameters allows to fetch data by parts and make client apps even faster.

## Backwards Compatibility
<!--All AIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The AIP must explain how the author proposes to deal with these incompatibilities. AIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
This improvement adds new API endpoint, and won't change backward compatibility for any previous clients.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
