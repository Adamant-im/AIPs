---
aip: 18
title: File sending in chat 
author: Aleksei Lebedev (@adamant-al), Stanislav Jelezoglo (@StanislavDevIOS)
discussions-to: https://github.com/Adamant-im/AIPs/issues/52
requires: 5
extends: https://aips.adamant.im/AIPS/aip-5
status: Draft
type: Standards
category: ARC
created: 2024-03-21
---

## Simple Summary

This proposal introduces the ability to add files to chat in ADAMANT Messenger apps.

## Abstract

The ability to send files within the messaging platform enhances communication by providing users with a versatile and efficient means of sharing information. Sending files allows users to exchange various types of content, including documents, images, videos, and more, directly within the chat interface. This functionality promotes collaboration, facilitates the sharing of important documents, and enriches conversations by providing context and additional information. Whether it's sharing work-related documents, personal photos, or multimedia content, the feature enhances the utility and usefulness of the messaging platform, enabling seamless communication and information exchange between users.

## Motivation

To establish a consistent and standardized approach for implementing file sending in ADAMANT Messenger apps, a comprehensive specification is necessary.

## Specification

To enable file sharing within the chat feature, the client must send an ADM rich message (as described in [AIP-5](https://aips.adamant.im/AIPS/aip-5))) containing the necessary file information. The structure of the message object includes fields such as `file_id`, `file_type`, `file_size`, `preview_id`, `file_name`, `nonce`, and `preview_nonce`.

The file_id field represents the unique identifier of the file. Other fields provide additional information such as file type, size, preview ID, file name, and nonces for encryption.

## Syntax

According to [AIP-5](https://aips.adamant.im/AIPS/aip-5)), field `transaction.asset.chat.message` must contain *encrypted stringified* JSON

The structure of the file object is as follows:

````
{
  "files": [
    {
      "file_id": "String",
      "file_type": "String",
      "file_size": Int64,
      "preview_id": "String",
      "file_name": "String",
      "nonce": "String",
      "preview_nonce": "String",
      "file_resolution": [Float]
    }
  ],
  "storage": "String",
  "comment": "String"
}
````

Object fields:

`files`: An array containing information about the files.
`file_id`: Represents the unique identifier of the file.
`file_type`: Denotes the type of the file. Optional
`file_size`: Indicates the size of the file.
`preview_id`: Represents the unique identifier of the file's preview. Optional
`file_name`: Specifies the name of the file. Optional
`nonce`: Nonce for encryption.
`preview_nonce`: Nonce for the file's preview.  Optional
`storage`: Specifies the storage information for the file.
`comment`: Сomment associated with the file. Optional
`file_resolution`: File resolution represented as an array values, where the first value denotes the width and the second value denotes the height of the file. Optional

You can combine it with [AIP-16](https://aips.adamant.im/AIPS/aip-16)) to reply with a file. Just send the following structure in the `reply_message` field:

```` json
{
  "replyto_id": "7452709338464950789",
  "reply_message": {
    "files": [
      {
        "file_id": "example_file_id",
        "file_type": "JPG",
        "file_size": 1024,
        "preview_id": "example_preview_id",
        "file_name": "example_file.jpg",
        "nonce": "example_nonce",
        "preview_nonce": "example_preview_nonce"б
        "file_resolution": [1024, 1024]
      }
    ],
    "storage": "example_storage",
    "comment": "Great file!"
  }
}
````

### Examples

Object `transaction.asset.chat.message` *before encryption*, sending a file:

```` json
{
  "replyto_id": "7452709338464950789",
  "reply_message": {
    "files": [
      {
        "file_id": "hydji2id",
        "file_type": "JPG",
        "file_size": 1024,
        "preview_id": "ki1wj2dw",
        "file_name": "example.jpg",
        "nonce": "ne27iuh2fje34bfuih2feb2cikjc3bkd...",
        "preview_nonce": "c23ki24neiuhu43fheuf5heiufe...",
        "file_resolution": [1024, 1024]
      }
    ],
    "storage": "IPFS",
    "comment": "Great!"
  }
}
````

Full transaction after encryption from U15677078342684640219 to U7972131227889954319 looks like:

```` json
{
  "transaction": {
    "type": 8,
    "amount": 0, // Amount should be 0
    "senderId": "U15677078342684640219",
    "senderPublicKey": "e16e624fd0a5123294b448c21f30a07a0435533c693b146b14e66830e4e20404",
    "asset": {
      "chat": {
        "message": "70cbd07ff2ceaf0fc38a01ef9...", // files object encrypted
        "own_message": "e98794eaedf47e...",
        "type": 2 // Type should be 2 (Rich Content Message)
      }
    },
    "recipientId": "U7972131227889954319",
    "timestamp": 46116887,
    "signature": "8fc2a54604109a6fcdccec2..."
  }
}
````

## Rationale

Enabling file sharing within the chat feature enhances the overall functionality and user experience of the ADAMANT Messenger platform. By allowing users to exchange files directly within conversations, this feature promotes seamless collaboration and information sharing among users. Whether it's sharing work-related documents, personal photos, or multimedia content, the ability to send files enriches conversations and facilitates the exchange of diverse types of information. Additionally, integrating file sharing into the chat feature streamlines communication by eliminating the need for users to switch between different applications or platforms to share files, thereby enhancing user convenience and productivity. Overall, the introduction of file sharing functionality enhances the utility and versatility of the ADAMANT Messenger platform, making it a more comprehensive solution for communication and collaboration needs.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
