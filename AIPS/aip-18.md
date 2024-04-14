---
aip: 18
title: Decentralized file transfer
author: Aleksei Lebedev (@adamant-al), Stanislav Jelezoglo (@StanislavDevIOS)
discussions-to: https://github.com/Adamant-im/AIPs/issues/55
requires: 5
extends: https://aips.adamant.im/AIPS/aip-5
status: Draft
type: Standards
category: ARC
created: 2024-03-21
---

## Simple Summary

This proposal introduces a decentralized file transfer feature in ADAMANT Messenger apps.

## Abstract

The ability to send files within the messaging platform enhances communication by providing users with a versatile and efficient means of sharing information. Sending files allows users to exchange various types of content, including documents, images, videos, and more, directly within the chat interface. This functionality promotes collaboration, facilitates the sharing of important documents, and enriches conversations by providing context and additional information.

The critical difference of file transfer in ADAMANT is sticking to a privacy approach, utilizing decentralized file storages such as [InterPlanetary File System (IPFS)](https://en.wikipedia.org/wiki/InterPlanetary_File_System) and different storage blockchains. It follows the concept of privacy and censorship resistance appropriate to ADAMANT.

## Motivation

A comprehensive specification is necessary to establish a consistent and standardized approach for implementing decentralized file transfer in ADAMANT Messenger apps.

## Specification

In the overview, decentralized file transfer includes:

- Encrypting a file utilizing the same end-to-end encryption as specified in [AIP-4](https://aips.adamant.im/AIPS/aip-4)
- Uploading an encrypted file to a decentralized storage node and receiving its ID
- Sending this ID to a recipient in the ADM rich text message
- Whenever later, a recipient decrypts the ADM messages, downloads a file (from any node of decentralized storage), and decrypts it

While encrypting and receiving/decrypting steps are described in other AIPs, sending file IDs in messages requires a specification.

After a client has a file ID, it sends an ADM rich message as described in [AIP-5](https://aips.adamant.im/AIPS/aip-5)), containing storage and file information. The `file_id` field represents the unique identifier of a file. Other fields provide additional information such as file type, size, preview ID, file name, and nonces for encryption.

## Syntax

According to [AIP-5](https://aips.adamant.im/AIPS/aip-5)), field `transaction.asset.chat.message` must contain *encrypted stringified* JSON with attached file information.

The structure of the file transfer object is as follows:

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
      "file_resolution": [Float, Float]
    }
  ],
  "storage": "String",
  "comment": "String"
}
````

Object fields:

- `files`: An array containing information about the attached encrypted files
- `file_id`: Represents the unique file identifier in the `storage`
- `file_type`: Denotes the type of the file after decryption. Optional.
- `file_size`: Indicates the size of the encrypted file in bytes
- `preview_id`: Represents the unique file identifier of an image preview. Optional.
- `file_name`: Specifies the name of a file. Optional.
- `nonce`: Nonce used for encryption
- `preview_nonce`: Nonce used for encryption of a file's preview. Optional.
- `storage`: Specifies a decentralized storage information where a file is stored
- `comment`: A comment/caption associated with a file. Optional.
- `file_resolution`: File resolution as an array of float values, where the first value denotes the width and the second value denotes the height of a file. Useful for images and videos. Optional.

### Examples

Object `transaction.asset.chat.message` *before encryption*, which includes a single file:

```` json
{
  "files": [
    {
      "file_id": "hydji2id",
      "file_type": "JPG",
      "file_size": 1024100,
      "preview_id": "ki1wj2dw",
      "file_name": "example.jpg",
      "nonce": "ne27iuh2fje34bfuih2feb2cikjc3bkd...",
      "preview_nonce": "c23ki24neiuhu43fheuf5heiufe...",
      "file_resolution": [2048, 1000]
    }
  ],
  "storage": "IPFS",
  "comment": "Great!"
}
````

The entire transaction after encryption from U15677078342684640219 to U7972131227889954319 looks like this:

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

You can combine sending files with [AIP-16](https://aips.adamant.im/AIPS/aip-16)) to reply to a message with a file attached. Send the following structure in the `reply_message` field:

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
        "preview_nonce": "example_preview_nonce",
        "file_resolution": [2048, 1000]
      }
    ],
    "storage": "IPFS",
    "comment": "Great file!"
  }
}
````

## Assumptions and limitations

While the adherence to a privacy approach is a base advantage of ADAMANT, it spawns some limitations:

- Obligatory end-to-end file encryption may lead to a little delay in the UX and requires more hardware resources on a client device
- Forwarding attachments in chats requires downloading content and re-encryption with a public key of the new recipient
- Decentralized storage may condition a little delay in file synchronization between storage nodes
- Usually, decentralized file storages don't guarantee deletion of files. Any node can store a file for unlimited time.

## Rationale

Enabling file sharing within the chat feature enhances the overall functionality and user experience of the ADAMANT Messenger platform. By allowing users to exchange files directly within conversations, this feature promotes seamless collaboration and information sharing among users. Whether it's sharing work-related documents, personal photos, or multimedia content, the ability to send files enriches conversations and facilitates the exchange of diverse types of information. Additionally, integrating file sharing into the chat feature streamlines communication by eliminating the need for users to switch between different applications or platforms to share files, thereby enhancing user convenience and productivity. Overall, the introduction of file sharing functionality enhances the utility and versatility of the ADAMANT Messenger platform, making it a more comprehensive solution for communication and collaboration needs.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
