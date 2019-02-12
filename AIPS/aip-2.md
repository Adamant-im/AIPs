---
aip: 2
title: URI Format for ADAMANT addresses
author: Dmitriy Soloduhin (@zyuhel), Pavel Anokhov (@RealBonus), Aleksei Lebedev
discussions-to: https://github.com/Adamant-im/AIPs/issues/2
status: Active
type: Standards
category: ARC
created: 2018-05-02
---

<!--You can leave these HTML comments in your merged AIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new AIPs. Note that an AIP number will be assigned by an editor. When opening a pull request to submit your AIP, please use an abbreviated title in the filename, `aip-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
A standard way of creating ADAMANT URIs for various use-cases.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->
URIs embedded in QR-codes, hyperlinks in web-pages, emails or chat messages provide for robust cross-application signaling between very loosely coupled applications. A standardized URI format allows for instant invocation of the user’s preferred wallet application and simplifies usage.

## Motivation
<!--The motivation is critical for AIPs that want to change the protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the AIP solves. AIP submissions without sufficient motivation may be rejected outright.-->
Help integrating ADAMANT into the World. Standartise QR-codes between different ADAMANT Messenger realizations. 

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for different platforms.-->
### Syntax
ADAMANT URIs contain "adm" in their schema (protocol) part and are constructed as follows:
```
request                 = "adm" ":" address [?parameters]
address                 = string
parameters              = (argument=value[&])*
argument                = uri encoded string
value                   = uri encoded string
```

### Semantics
`address` is mandatory and starts with U letter. It should follow ADAMANT address format.

`parameters` is optional argument-value pairs and defines specific actions to be done with following address. Some specifications should follow in consecutive ARCs.

Commonly known parameters:
- `label` is a contact name. Often used on business cards.
- `anti_spam` is a string that contact use as anti-spam code
- `action` is action that supposed to perform
- `amount` is amount in ADM to send
- `message` is a message which supposed to be sent to contact (or as a comment to a transfer)

Supposed `action` values:
- `send_tokens` for sending tokens to address
- `send_message` for sending message to address

It is up to application how to process actions and it depends when user retrieves URL in the app. F. e., in Send tokens window, it can parse only `amount` and `message` values.

### Examples
```
adm:U9821606738809290000?label=John+Doe
```
Includes contact address and its name.

```
adm:U9821606738809290000?label=John+Doe&anti_spam=nospamers&action=send_message&message=Just+say+hello
```
Suppose user app open chat window with U9821606738809290000, name him as "John Doe" if he is not named yet, and put message "Just say hello" to input field ready to send. 

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
This ARC is needed to ensure that all ADAMANT Messengers realizations support same URI format.


## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
