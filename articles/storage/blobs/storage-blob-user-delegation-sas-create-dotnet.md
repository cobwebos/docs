---
title: 使用 .NET 为容器或 blob 创建用户委托 SAS
titleSuffix: Azure Storage
description: 了解如何使用适用于 Azure 存储的 .NET 客户端库创建具有 Azure Active Directory 凭据的用户委托 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: fa0754505d887b77cc3d11ebde9c246d4a00eb59
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82883305"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>使用 .NET 为容器或 blob 创建用户委托 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文介绍如何使用 Azure Active Directory （Azure AD）凭据为包含适用于 .NET 版本12的 Azure 存储客户端库的容器或 blob 创建用户委托 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>分配用于访问数据的 RBAC 角色

当 Azure AD 安全主体尝试访问 blob 数据时，该安全主体必须具有对资源的权限。 无论安全主体是 Azure 中的托管标识还是在开发环境中运行代码的 Azure AD 用户帐户，都必须为安全主体分配允许访问 Azure 存储中的 blob 数据的 RBAC 角色。 若要了解如何通过 RBAC 分配权限，请参阅[使用 Azure Active Directory 授权访问 Azure Blob 和队列](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)中标题为“为访问权限分配 RBAC 角色”的部分。****

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

若要详细了解如何使用 Azure 存储中的 Azure 标识客户端库进行身份验证，请参阅[使用 Azure Active Directory 和 Azure 资源的托管标识授权访问 Blob 和队列](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)中标题为**使用 Azure 标识库进行身份验证**的部分。

## <a name="add-using-directives"></a>添加 using 指令

向代码添加以下 `using` 指令，以便使用 Azure 标识和 Azure 存储客户端库。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure;
using Azure.Identity;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>获取经过身份验证的令牌凭据

若要获取令牌凭据，以便代码用它来授权对 Azure 存储的请求，请创建 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 类的实例。

下面的代码段演示如何获取经过身份验证的令牌凭据，并使用它来创建 Blob 存储服务客户端：

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>获取用户委托密钥

每个 SAS 均使用密钥进行签名。 若要创建用户委托 SAS，必须首先请求用户委托密钥，然后使用该密钥对 SAS 进行签名。 用户委托密钥类似于用于签署服务 SAS 或帐户 SAS 的帐户密钥，只不过它依赖于你的 Azure AD 凭据。 当客户端使用 OAuth 2.0 令牌请求用户委托密钥时，Azure 存储将代表该用户返回用户委托密钥。

拥有用户委派密钥后，可以使用该密钥在密钥的生存期内创建任意数量的用户委派共享访问签名。 用户委托密钥独立于用于获取该密钥的 OAuth 2.0 令牌，因此只要密钥仍有效，就不必续订令牌。 你可以指定密钥在长达7天的时间内有效。

使用以下方法之一来请求用户委托密钥：

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

下面的代码段获取用户委托密钥并写出其属性：

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>创建 SAS 令牌

以下代码片段显示了如何创建新的[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) ，并提供了用户委托 SAS 的参数。 然后，该代码段调用[ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters)来获取 SAS 令牌字符串。 最后，该代码生成完整的 URI，包括资源地址和 SAS 令牌。

```csharp
// Create a SAS token that's valid for one hour.
BlobSasBuilder sasBuilder = new BlobSasBuilder()
{
    BlobContainerName = containerName,
    BlobName = blobName,
    Resource = "b",
    StartsOn = DateTimeOffset.UtcNow,
    ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
};

// Specify read permissions for the SAS.
sasBuilder.SetPermissions(BlobSasPermissions.Read);

// Use the key to get the SAS token.
string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

// Construct the full URI, including the SAS token.
UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>示例：获取用户委托 SAS

以下示例方法显示了用于对安全主体进行身份验证和创建用户委托 SAS 的完整代码：

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // You can use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow, 
                                                                        DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
    Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);
    Console.WriteLine();

    // Create a SAS token that's valid for one hour.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        BlobContainerName = containerName,
        BlobName = blobName,
        Resource = "b",
        StartsOn = DateTimeOffset.UtcNow,
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
    };

    // Specify read permissions for the SAS.
    sasBuilder.SetPermissions(BlobSasPermissions.Read);

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    Console.WriteLine();
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>示例：使用用户委托 SAS 读取 blob

下面的示例从模拟的客户端应用程序测试在上一个示例中创建的用户委托 SAS。 如果 SAS 有效，则客户端应用程序能够读取 blob 的内容。 如果 SAS 无效，例如，如果它已过期，Azure 存储将返回错误代码403（禁止访问）。

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (RequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid,
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另请参阅

- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的受限访问权限](../common/storage-sas-overview.md)
- [获取用户委派密钥操作](/rest/api/storageservices/get-user-delegation-key)
- [创建用户委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
