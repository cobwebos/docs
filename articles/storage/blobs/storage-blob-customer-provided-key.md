---
title: 使用 .NET-Azure 存储为对 Blob 存储的请求指定客户提供的密钥
description: 了解如何使用 .NET 为对 Blob 存储的请求指定客户提供的密钥。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea8254f108aed9d40e6970a27409035b1e10ab41
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806994"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>使用 .NET 为 Blob 存储的请求指定客户提供的密钥

对 Azure Blob 存储发出请求的客户端可以选择为单个请求提供加密密钥。 在请求中包含加密密钥可对 Blob 存储操作的加密设置进行精细控制。 客户提供的密钥（预览版）可存储在 Azure Key Vault 或其他密钥存储中。

本文介绍如何使用 .NET 为请求指定客户提供的密钥。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

若要详细了解如何使用 azure 存储空间中的 Azure 标识客户端库进行身份验证，请**参阅在**[使用 azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)。

## <a name="example-use-a-customer-provided-key-to-upload-a-blob"></a>示例：使用客户提供的密钥上传 blob

下面的示例创建客户提供的密钥，并使用该密钥上传 blob。 代码上传块，然后提交块列表，将 blob 写入 Azure 存储。

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
- [使用 Azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限](../common/storage-auth-aad-msi.md)
