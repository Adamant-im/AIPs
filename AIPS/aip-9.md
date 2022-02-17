---
aip: 9
title: URI Format for ADAMANT
author: Aleksei Lebedev
discussions-to: https://github.com/Adamant-im/AIPs/issues/19
status: Accepted
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

- `label` is a contact name as in [AIP-2](https://aips.adamant.im/AIPS/aip-2). If address has no name in KVS address book yet, app should name it.
- `amount` is amount in ADM to send
- `message` is a message which supposed to be sent to contact (or as a comment for a transfer)

It is up to application how to process parameters, it depends when user retrieves URL in the app. F. e., in In-Chat Send tokens screen, it can parse `amount` and `message` values.

### Examples

```
adm:U9821606738809290000?label=John+Doe&message=Just+say+hello
```

App adds chat with U9821606738809290000, name him as "John Doe" if he is not named yet, and put message "Just say hello" to input field ready to send.

```
adm:U9821606738809290000?label=John+Doe&amount=1.12&message=Buy+a+beer
```

If user read this string from QR in Send Tokens screen, app fills `to` field with U9821606738809290000, `amount` with 1.12 ADM, and `comment` with "Buy a beer".
If app processes this string that received from outside, it adds chat with U9821606738809290000, name him as "John Doe" if he is not named yet, and put message "Buy a beer".

## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
Parameters allows to extend possibilities included in URIs, like in QR codes.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
