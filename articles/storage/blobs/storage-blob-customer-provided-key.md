---
title: 使用 .NET 在对 Blob 存储的请求中指定客户提供的密钥 - Azure 存储
description: 了解如何使用 .NET 在对 Blob 存储的请求中指定客户提供的密钥。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/20/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 3f4ab13c893ec7125ae720cae63b0c1e45f6f3de
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091277"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>使用 .NET 在对 Blob 存储的请求中指定客户提供的密钥

对 Azure Blob 存储发出请求的客户端可以选择在单个请求中提供加密密钥。 在请求中包含加密密钥可以精细控制 Blob 存储操作的加密设置。 客户提供的密钥可以存储在 Azure Key Vault 或其他密钥存储中。

本文介绍如何使用 .NET 在请求中指定客户提供的密钥。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

若要详细了解如何使用 Azure 存储中的 Azure 标识客户端库进行身份验证，请参阅[使用 Azure Active Directory 和 Azure 资源的托管标识授权访问 Blob 和队列](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)中标题为**使用 Azure 标识库进行身份验证**的部分。

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>示例：使用客户提供的密钥上传 Blob

以下示例创建客户提供的密钥，并使用该密钥上传 Blob。 该代码将上传一个块，然后提交块列表以将 Blob 写入 Azure 存储。

```csharp
async static Task UploadBlobWithClientKey(string accountName, string containerName,
    string blobName, Stream data, byte[] key)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri accountUri = new Uri("https://" + accountName + blobServiceEndpointSuffix);

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = new CustomerProvidedKey(key)
    };

    // Create a client object for the Blob service, including options.
    BlobServiceClient serviceClient = new BlobServiceClient(accountUri, 
        new DefaultAzureCredential(), options);

    // Create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient = serviceClient.GetBlobContainerClient(containerName);

    // Create a new block blob client object.
    // The blob client retains the credential and client options.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>后续步骤

- [静态数据的 Azure 存储加密](../common/storage-service-encryption.md)
- [使用 Azure 资源的 Azure Active Directory 和托管标识授予对 Blob 和队列的访问权限](../common/storage-auth-aad-msi.md)
