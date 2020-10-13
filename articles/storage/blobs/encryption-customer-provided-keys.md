---
title: 在对 Blob 存储的请求中提供加密密钥
titleSuffix: Azure Storage
description: 针对 Azure Blob 存储发出请求的客户端可以选择基于每个请求提供加密密钥。 在请求中包含加密密钥可以精细控制 Blob 存储操作的加密设置。
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: abdc83019205fc39e1e85a53da7e49f8a7d4f11c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618720"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>在对 Blob 存储的请求中提供加密密钥

针对 Azure Blob 存储发出请求的客户端可以选择基于每个请求提供加密密钥。 在请求中包含加密密钥可以精细控制 Blob 存储操作的加密设置。 客户提供的密钥可以存储在 Azure Key Vault 或其他密钥存储中。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="encrypting-read-and-write-operations"></a>加密读取和写入操作

当客户端应用程序在请求中提供加密密钥时，Azure 存储将在读取和写入 Blob 数据时以透明方式执行加密和解密。 Azure 存储会将加密密钥的 SHA-256 哈希与 Blob 的内容一起写入。 哈希用于验证对 Blob 的所有后续操作是否都使用相同的加密密钥。

Azure 存储不会存储或管理客户端连同请求一起发送的加密密钥。 加密或解密过程完成后，会立即以安全方式丢弃该密钥。

当客户端使用请求中客户提供的密钥创建或更新 Blob 时，针对该 Blob 的后续读取和写入请求也必须提供该密钥。 如果在针对已使用客户提供的密钥加密的 Blob 的请求中未提供该密钥，则请求将会失败并返回错误代码 409（冲突）。

如果客户端应用程序在请求中发送加密密钥，同时使用 Microsoft 托管密钥或客户托管密钥加密了存储帐户，则 Azure 存储将使用请求中提供的密钥进行加密和解密。

若要在请求中发送加密密钥，客户端必须使用 HTTPS 来与 Azure 存储建立安全连接。

每个 Blob 快照可以有自身的加密密钥。

## <a name="request-headers-for-specifying-customer-provided-keys"></a>用于指定客户提供的密钥的请求标头

对于 REST 调用，客户端可以使用以下标头在请求中向 Blob 存储安全传递加密密钥信息：

|请求标头 | 说明 |
|---------------|-------------|
|`x-ms-encryption-key` |对于写入和读取请求都是必需的。 Base64 编码的 AES-256 加密密钥值。 |
|`x-ms-encryption-key-sha256`| 对于写入和读取请求都是必需的。 加密密钥的 Base64 编码 SHA256。 |
|`x-ms-encryption-algorithm` | 对于写入请求是必需的，对于读取请求是可选的。 指定在通过给定密钥加密数据时要使用的算法。 必须是 AES256。 |

在请求中指定加密密钥是可选操作。 但是，如果为写入操作指定上面列出的标头之一，则必须指定所有这些标头。

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>支持客户提供的密钥的 Blob 存储操作

以下 Blob 存储操作支持在请求中发送客户提供的加密密钥：

- [放置 Blob](/rest/api/storageservices/put-blob)
- [放置块列表](/rest/api/storageservices/put-block-list)
- [放置块](/rest/api/storageservices/put-block)
- [从 URL 放置块](/rest/api/storageservices/put-block-from-url)
- [放置页](/rest/api/storageservices/put-page)
- [从 URL 放置页](/rest/api/storageservices/put-page-from-url)
- [追加块](/rest/api/storageservices/append-block)
- [设置 Blob 属性](/rest/api/storageservices/set-blob-properties)
- [设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)
- [获取 Blob](/rest/api/storageservices/get-blob)
- [获取 Blob 属性](/rest/api/storageservices/get-blob-properties)
- [获取 Blob 元数据](/rest/api/storageservices/get-blob-metadata)
- [快照 Blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>轮换客户提供的密钥

若要轮换用于加密 blob 的加密密钥，请下载该 Blob，并使用新的加密密钥重新上传该 Blob。

> [!IMPORTANT]
> 无法使用 Azure 门户来读取或写入通过请求中提供的密钥加密的容器或 Blob。
>
> 请务必在 Azure Key Vault 等安全密钥存储中，保护在对 Blob 存储发出的请求中提供的加密密钥。 如果你尝试在不使用加密密钥的情况下对容器或 Blob 执行写入操作，该操作将会失败，并且你会失去对象访问权限。

## <a name="next-steps"></a>后续步骤

- [使用 .NET 在对 Blob 存储的请求中指定客户提供的密钥](storage-blob-customer-provided-key.md)
- [静态数据的 Azure 存储加密](../common/storage-service-encryption.md)
