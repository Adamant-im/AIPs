---
aip: 8
title: URI Format for ADAMANT
author: Aleksei Lebedev
discussions-to: https://github.com/Adamant-im/AIPs/issues/8
extends: https://aips.adamant.im/AIPS/aip-2
status: Draft
type: Standards
category: ARC
created: 2019-02-12
---

<!--You can leave these HTML comments in your merged AIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new AIPs. Note that an AIP number will be assigned by an editor. When opening a pull request to submit your AIP, please use an abbreviated title in the filename, `aip-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
How to create and process URIs, which includes ADAMANT contacts.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
URIs embedded in QR-codes, web-pages, emails or chat messages provide a way to create or open chat with specific contact and name it.

This AIP extends [AIP-2](https://aips.adamant.im/AIPS/aip-2). AIP-8 is a preferred URI format for ADAMANT addresses in QR codesAll types of ADAMANT apps should read both types of URI, but generate URI only as AIP-8 explains.

## Motivation
<!--The motivation is critical for AIPs that want to change the protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the AIP solves. AIP submissions without sufficient motivation may be rejected outright.-->
Current URI format [AIP-2](https://aips.adamant.im/AIPS/aip-2) is not suitable for people who are not familiar with ADAMANT. Using this extension helps new users to contact friends in ADAMANT, and existing users can share their ADAMANT address easily.

So if QR with URI is scanned in general QR scanner app, it will open web app, but if QR is scanned from ADAMANT iOS app in Send tokens window, ADAMANT address can be parsed.

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for different platforms.-->
### Syntax
ADAMANT URIs may contain `address` in their parameters as follows:
```
web app URI             = web link [?parameters]
parameters              = (argument=value[&])*
argument                = uri encoded string
value                   = uri encoded string
```

### Semantics

`parameters` is optional argument-value pairs and defines specific actions to be done with following address.

Commonly known parameters:
- `address` is a valid ADAMANT address, starts with U letter.

If `address` parameter included, other parameters can be used according to as [AIP-2](https://aips.adamant.im/AIPS/aip-2#semantics).

### Web app behaviour specifics
Supposed scenarios for web apps for processing URIs:
- If parameters can't be processed, ignore them
- User can be logged in Messenger, or not. Anyway, after log in or creating new ADAMANT account, URI should be processed.
- If `address` is an existing contact, go to chat window with him. If contact has no name in KVS address book yet, name it with `label` parameter.
- If `address` is a new contact, create chat with him and name him as `label`

### Other apps behaviour specifics
iOS and Anroid apps should only read parameters from URIs.

### Examples
```
https://msg.adamant.im?address=U9821606738809290000&label=John+Doe
```
Creates or open chat with U9821606738809290000. Name him as "John Doe" if contact is not named yet.

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
General web-links read by any QR code scanner and supported by any web browser. This allows to share ADM contacts easily.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
