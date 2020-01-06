---
aip: 15
title: QR codes for ADAMANT addresses
author: Aleksei Lebedev (@adamant-al)
discussions-to: https://github.com/Adamant-im/AIPs/issues/33
status: Accepted
requires: 8, 9
type: Standards
category: ARC
created: 2019-02-20
---

<!--You can leave these HTML comments in your merged AIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new AIPs. Note that an AIP number will be assigned by an editor. When opening a pull request to submit your AIP, please use an abbreviated title in the filename, `aip-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
A standard way of generating ADAMANT QR codes with addresses.

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->

QR code is a convenient way for sharing ADAMANT address. General URI for addresses described in [AIP-8](https://aips.adamant.im/AIPS/aip-8) and [AIP-9](https://aips.adamant.im/AIPS/aip-9).

## Motivation
<!--The motivation is critical for AIPs that want to change the protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the AIP solves. AIP submissions without sufficient motivation may be rejected outright.-->
Well known view of ADAMANT QR codes with addresses helps users to understand if QR code includes ADAMANT address or not.

## Specification

QR code design must include informational part (URI) and ADAMANT logo in the center of QR.

### Examples

![QR code sample](../assets/aip-15/qr-code.png)

QR data contents: https://msg.adamant.im?address=U9821606738809290000&label=John+Doe&action=send_message&message=Just+say+hello

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
