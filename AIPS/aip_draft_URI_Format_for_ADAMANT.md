---
aip: draft
title: URI Format for ADAMANT
author: Dmitriy Soloduhin (@zyuhel), Pavel Anokhov (@RealBonus), Aleksei Lebedev
discussions-to: https://github.com/Adamant-im/AIPs/issues/2
status: Draft
extends: 2
type: Standards
category: ARC
created: 2019-02-12
---

<!--You can leave these HTML comments in your merged AIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new AIPs. Note that an AIP number will be assigned by an editor. When opening a pull request to submit your AIP, please use an abbreviated title in the filename, `aip-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
A standard way of creating ADAMANT URIs for various use-cases.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
This AIP extends [AIP-2](https://aips.adamant.im/AIPS/aip-2) and adds new parameters.

## Motivation
<!--The motivation is critical for AIPs that want to change the protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the AIP solves. AIP submissions without sufficient motivation may be rejected outright.-->
Propose parameters to include in URI actions and their parameters. Standartise QR-codes between different ADAMANT Messenger realizations.

## Specification

URI can contain optional `parameters`:
- `label` is a contact name. If address has no name in KVS address book yet, app should name it.
- `action` is action that supposed to perform
- `amount` is amount in ADM to send
- `message` is a message which supposed to be sent to contact (or as a comment for a transfer)

Supposed `action` values:
- `send_tokens` for sending tokens to address. Should open Send tokens screen. If dialog with address exists, app should send In-Chat. 
- `send_message` for sending message to address. Should open or create chat with address.

It is up to application how to process actions and it depends when user retrieves URL in the app. F. e., in Send tokens window, it can parse only `amount` and `message` values.

### Examples

```
adm:U9821606738809290000?label=John+Doe&action=send_message&message=Just+say+hello
```
Suppose user app open chat window with U9821606738809290000, name him as "John Doe" if he is not named yet, and put message "Just say hello" to input field ready to send. 

```
adm:U9821606738809290000?label=John+Doe&action=send_tokens&amount=1.12&message=For+beer
```
Opens Send tokens screen to U9821606738809290000 with amount of 1.12 ADM and comment "For beer". 

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
Parameters allows to extend possibilities included in URIs, like in QR codes.


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
