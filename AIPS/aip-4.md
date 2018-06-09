---
aip: 4
title: Basic Encrypted Messages
author: Dmitriy Soloduhin (@zyuhel)
discussions-to: https://github.com/Adamant-im/AIPs/issues/7
status: Draft
type: Standards
category: ARC
created: 2018-06-09
---

Structure of basic encrypted messages in ADAMANT 

## Simple Summary
Describing current structure of basic encrypted ADAMANT messages, so that extension or alternative clients can rely on it.


## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
To standartize message exchange between different ADAMANT clients we need to describe how messages are stored and encrypted/decrypted.


## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for different platforms.-->
Secure Message exchange is fundament of ADAMANT Messaging system. 
Messages are encrypted using curve25519xsalsa20poly1305 ([NaCl box](https://nacl.cr.yp.to/box.html)) algorithm. Box created using senders private key and recipients public key. Recipient public key is retrieved from network. private 
Because private and public keys of account are instances of Ed25519 signing keys they need to be converted into Curve25519 Diffie-Hellman keys first.

## Syntax
Here is transaction asset contents syntax 
````
chat: {
  message: HEXIFIED_ENCRYPTED_MESSAGE
  own_message: HEXIFIED_NONCE
  type: 1
}
````

## Rationale
Currently used mechanisms should also be documented, to make process of extending and changing more clear. 


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
