---
aip: 18
title: Decentralized File Transfer
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

This proposal introduces a decentralized file-transfer feature in ADAMANT Messenger apps.

## Abstract

The ability to send files within a messaging platform enhances communication by providing users with a versatile and efficient means of sharing information. Sending files lets users exchange various types of content—documents, images, videos, and more—directly within the chat interface. This functionality promotes collaboration, facilitates the sharing of important documents, and enriches conversations by adding context and additional information.

The key distinction of file transfer in ADAMANT is its privacy-first approach, which relies on decentralized storage solutions such as the [InterPlanetary File System (IPFS)](https://en.wikipedia.org/wiki/InterPlanetary_File_System) and storage blockchains. This aligns with ADAMANT’s principles of privacy and censorship resistance.

## Motivation

A comprehensive specification is necessary to establish a consistent and standardized approach to implementing decentralized file transfer in ADAMANT Messenger apps.

## Specification

At a high level, decentralized file transfer consists of the following steps:

- Encrypting the file using the end-to-end encryption defined in [AIP-4](https://aips.adamant.im/AIPS/aip-4)
- Generating a file ID
- Uploading the encrypted file to a decentralized-storage node
- Sending that ID to the recipient in an ADM rich-text message
- Later, the recipient decrypts the ADM message, downloads the file (from any decentralized-storage node), and decrypts it.

While the encryption and decryption steps are covered in other AIPs, the process of embedding file IDs in messages requires its own specification.

Once a client has the file ID, it sends an ADM rich-text message, as described in [AIP-5](https://aips.adamant.im/AIPS/aip-5), containing both storage and file information. The `id` field represents the unique identifier of the file; other fields convey the file type, size, preview ID, file name, and nonces used for encryption.

## Syntax

According to [AIP-5](https://aips.adamant.im/AIPS/aip-5), the field `transaction.asset.chat.message` must contain **encrypted, stringified** JSON that includes the attached-file information.

The structure of the file-transfer object is as follows:

```json
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
```

Object fields:

- `files`: Array containing metadata for each attached encrypted file
- `id`: Unique file identifier within `storage`
- `mimeType`: MIME type of the file (optional)
- `extension`: File-extension string (optional)
- `size`: Size of the *encrypted* file in bytes
- `preview`: Preview-image data (optional)
- `name`: Original file name (optional)
- `nonce`: Nonce used for encryption
- `storage`: Describes the decentralized storage in which the file resides
- `comment`: Caption or comment associated with the file (optional)
- `resolution`: [width, height] in pixels; useful for images and videos (optional)
- `duration`: Duration of a video or audio file in seconds (optional)

### Examples

Object `transaction.asset.chat.message` *before encryption*, which includes a single file:

``` json
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
```

The entire transaction after encryption from U15677078342684640219 to U7972131227889954319 looks like this:

``` json
{
  "transaction": {
    "type": 8,
    "amount": 0, // Amount should be 0
    "senderId": "U15677078342684640219",
    "senderPublicKey": "e16e624fd0a5123294b448c21f30a07a0435533c693b146b14e66830e4e20404",
    "asset": {
      "chat": {
        "message": "70cbd07ff2ceaf0fc38a01ef9...", // encrypted files object
        "own_message": "e98794eaedf47e...",
        "type": 2 // Type should be 2 (Rich Content Message)
      }
    },
    "recipientId": "U7972131227889954319",
    "timestamp": 46116887,
    "signature": "8fc2a54604109a6fcdccec2..."
  }
}
```

You can combine file sending with [AIP-16](https://aips.adamant.im/AIPS/aip-16) to reply to a message that has an attachment. Supply the following structure in the `reply_message` field:

``` json
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
        "mimeType": "video/quicktime",
        "extension": "mov",
        "size": 150998,
        "duration": 129.3
      }
    ],
    "comment": "Hello!"
  }
}
```

## Transfer Scenario

### Sender’s client

1. Create a placeholder in the chat for the file transfer
2. Display the file(s) inside the placeholder with a spinner or progress indicator; the user can cancel the transfer at any time.
3. Encrypt the file(s).
4. Upload the encrypted file(s) to a storage node
5. Wait until the upload completes and obtain the file ID(s). If a network issue occurs, let the user retry or cancel. Verify that the file(s) have been uploaded successfully.
6. Send the ADM rich-text message. If a network issue occurs, let the user retry or cancel.

### Recipient’s client

1. Fetch and decrypt the ADM message
2. Create a placeholder for the file(s) in the chat with a spinner
3. *(Optional)* Fetch the preview image if *auto-preview* is enabled
4. *(Optional)* Fetch the full file if *auto-download* is enabled
5. Decrypt the file(s)
6. Replace the placeholder with the final content

As with in-chat crypto transfers, retry logic and timeouts depend on the message age; very recent transfers may need extra time before the file becomes available on the node to which the recipient is connected.

## Assumptions and Limitations

Although ADAMANT’s privacy-first design is a core strength, it also introduces several limitations:

- Mandatory end-to-end encryption may introduce a slight UX delay and demands additional client-side resources
- Forwarding attachments requires downloading the content and re-encrypting it with the new recipient’s public key
- Decentralized storage networks can exhibit propagation delays between nodes
- Most decentralized-storage solutions cannot guarantee file deletion; any node may retain a file indefinitely

## Rationale

This AIP adds a file-sharing feature that follows the decentralized ethos of ADAMANT, providing strong privacy and censorship resistance. A standardized approach guarantees consistent behavior across all ADAMANT Messenger clients.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
