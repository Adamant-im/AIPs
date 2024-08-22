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
- Generating file ID
- Uploading an encrypted file to a decentralized storage node
- Sending this ID to a recipient in the ADM rich text message
- Whenever later, a recipient decrypts the ADM messages, downloads a file (from any node of decentralized storage), and decrypts it

While encrypting and receiving/decrypting steps are described in other AIPs, sending file IDs in messages requires a specification.

After a client has the file ID, it sends an ADM rich message as described in [AIP-5](https://aips.adamant.im/AIPS/aip-5)), containing storage and file information. The `file_id` field represents the unique identifier of a file. Other fields provide additional information such as file type, size, preview ID, file name, and nonces for encryption.

## Syntax

According to [AIP-5](https://aips.adamant.im/AIPS/aip-5)), field `transaction.asset.chat.message` must contain *encrypted stringified* JSON with attached file information.

The structure of the file transfer object is as follows:

````
{
  "files": [
    {
      "preview": {
        "id": "String",
        "nonce": "String",
        "extension": "String"
      },
      "id": "String",
      "mimeType": "String",
      "extension": "String",
      "size": Int64,
      "name": "String",
      "nonce": "String",
      "resolution": [Float, Float],
      "duration": Float
    }
  ],
  "storage": { "id": "String" },
  "comment": "String"
}
````

Object fields:

- `files`: An array containing information about the attached encrypted files
- `id`: Represents the unique file identifier in the `storage`
- `mimeType`: Specifies the MIME type of the file. Optional.
- `extension`: Indicates the file extension. Optional.
- `size`: Indicates the size of the encrypted file in bytes
- `preview`: Image preview. Optional.
- `name`: Specifies the name of a file. Optional.
- `nonce`: Nonce used for encryption
- `storage`: Specifies a decentralized storage information where a file is stored
- `comment`: A comment/caption associated with a file. Optional.
- `resolution`: File resolution as an array of float values, where the first value denotes the width and the second value denotes the height of a file. Useful for images and videos. Optional.
- `duration`: Video duration in seconds. Optional

### Examples

Object `transaction.asset.chat.message` *before encryption*, which includes a single file:

```` json
{
  "storage": {
    "id": "ipfs"
  },
  "files": [
    {
      "id": "bafkreifxs2buan4anxwhfnt...v25rewteppsh6u4s4x5hi4o4du",
      "name": "IMG_308",
      "resolution": [
        1179,
        2556
      ],
      "preview": {
        "id": "bafkreidqpnqmqocqp...if4xb3bi76gl6g4kmtg62cmpw5ufu",
        "nonce": "297d443aa29aa...485309f9f5ae159df9784d",
        "extension": "jpeg"
      },
      "nonce": "030aa714943dc1dd...1c7091920d9d",
      "mimeType": "image/jpeg",
      "extension": "jpeg",
      "size": 150998
    }
  ],
  "comment": "Hello!"
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

You can combine sending files with [AIP-16](https://aips.adamant.im/AIPS/aip-16) to reply to a message with a file attached. Send the following structure in the `reply_message` field:

```` json
{
  "replyto_id": "7452709338464950789",
  "reply_message": {
    "storage": {
      "id": "ipfs"
    },
    "files": [
      {
        "id": "bafkreifxs2buan4anxwhfnt...v25rewteppsh6u4s4x5hi4o4du",
        "name": "IMG_308",
        "resolution": [
          1179,
          2556
        ],
        "preview": {
          "id": "bafkreidqpnqmqocqp...if4xb3bi76gl6g4kmtg62cmpw5ufu",
          "nonce": "297d443aa29aa...485309f9f5ae159df9784d",
          "extension": "jpeg"
        },
        "nonce": "030aa714943dc1dd...1c7091920d9d",
        "mimeType": "video/qiucktime",
        "extension": "mov",
        "size": 150998,
        "duration": 129.3
      }
    ],
    "comment": "Hello!"
  }
}
````

## Transfer scenario

To ensure seamless UX, a sender's client app follows the sequencing:

- Encrypts a file
- Creates a placeholder for the file transfer in a chat
- Shows sent files in the placeholder with a spinner
- Initiates file uploading to a node
- Wait for the files to be uploaded and get the files ID
- Sends an ADM rich text message
- In case of a network issue, restores the uploading
- Verifies the file is uploaded

The recipient's app:

- Fetches an ADM message and decrypts it
- Creates a placeholder in a chat with a spinner
- Tries to fetch preview (if enabled auto preview in the app settings)
- Tries to fetch the entire file (if enabled auto download in the app settings)
- Decrypts files
- Fills the placeholder

Like in-chat crypto transfers, the fetching algorithm and re-tries count depend on message time. Recent transfers may require more time to reflect on a storage node to which the recipient is connected.

## Assumptions and limitations

While the adherence to a privacy approach is a base advantage of ADAMANT, it spawns some limitations:

- Obligatory end-to-end file encryption may lead to a little delay in the UX and requires more hardware resources on a client device
- Forwarding attachments in chats requires downloading content and re-encryption with a public key of the new recipient
- Decentralized storage may condition a little delay in file synchronization between storage nodes
- Usually, decentralized file storages don't guarantee deletion of files. Any node can store a file for unlimited time.

## Rationale

This AIP introduces a file sharing feature according to the decentralized concept, offering a layer of privacy and censorship resistance. A standardized approach ensures consistent behavior across different ADAMANT Messenger apps.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
