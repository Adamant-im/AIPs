---
aip: <to be assigned>
title: Storing data in chain
author: Dmitriy Soloduhin (@zyuhel)
discussions-to: https://github.com/Adamant-im/AIPs/issues/2
status: Draft
type: Standards
category: ARC
created: 2018-05-19
---

Storing sensitive and shared data in ADAMANT blockchain.

## Simple Summary
Define ways of storing data in blockchain, so that different clients has understanding of how to handle it.


## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
Clients need to save different data across different devices. A standartized way of saving and retrieving data will help enchance user experience.


## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for different platforms.-->
Data storage should use State storing (KVS) functionality added in [v0.2.0 node release](https://github.com/Adamant-im/adamant/releases/tag/v0.2.0).
Selected key should be choosen according usage. If some key is going to be reused across different applications, corresponding AIP should be created, to ensure that all application will handle it similarly.

Value can be stored as plain string or JSON, all encrypted values should be stored as JSON. If value is stored in plainstring, it mustn't begin with symbol {, because in that case it will be treated as JSON. Plain string values should be unencrypted and can be used to store some public identifiers.
JSON values can store different objects. If JSON object has *message* and *nonce* keys, it should show that this object holds sensitive encrypted data.

For compatibility between clients encrypted data may be encrypted using [NaCL.secretbox](https://nacl.cr.yp.to/secretbox.html) using SHA-256 hash of private key as secret key. But this is recommendation, and not requirement.


## Rationale
Using two different systems for separate storage of public and sensitive data can be considered not rationale. It should be clear, that clients should distinguish between secret and public data, to know that data should be decoded. Public data could have some identifiers that are not efficient to store as json, because they are just plain strings or some data that can be written in string format. So ability to store strings and json objects should be considered as right way. Nodes shouldn't interfere with data stored in objects. So all data handling should be done on client.
Regarding encryption, most of KVS stored data is needed for use of one user, holder of the private/secret key of an account. So using NaCL public-key authenticated encryption (box) has no sense. To not introduce new encryption libraries, we can use Secret-key authenticated encryption (secretbox), using SHA-256 of private key as secret key.



## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
