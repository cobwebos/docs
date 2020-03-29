---
title: 使用 .NET 为容器或 Blob 创建用户委派 SAS
titleSuffix: Azure Storage
description: 了解如何使用 .NET 客户端库进行 Azure 存储，使用 Azure 活动目录凭据创建用户委派 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 385d2c3b88bc2e4d653dae2dc9670cb9e9388faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371830"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>为具有 .NET 的容器或 blob 创建用户委派 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文演示如何使用 Azure 活动目录 （Azure AD） 凭据为容器或 Blob 创建用户委派 SAS 的容器或 Blob 的 Azure 存储客户端库为 .NET。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-rbac-roles-for-access-to-data"></a>分配用于访问数据的 RBAC 角色

当 Azure AD 安全主体尝试访问 Blob 数据时，该安全主体必须具有对资源的权限。 无论安全主体是 Azure 中的托管标识还是开发环境中运行代码的 Azure AD 用户帐户，都必须为安全主体分配一个 RBAC 角色，该角色授予对 Azure 存储中的 Blob 数据的访问。 若要了解如何通过 RBAC 分配权限，请参阅[使用 Azure Active Directory 授权访问 Azure Blob 和队列](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)中标题为“为访问权限分配 RBAC 角色”的部分。****

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

以下代码段演示如何获取经过身份验证的令牌凭据，并使用它为 Blob 存储创建服务客户端：

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>获取用户委派密钥

每个 SAS 均使用密钥进行签名。 要创建用户委派 SAS，必须首先请求用户委派密钥，然后用于对 SAS 进行签名。 用户委派密钥类似于用于对服务 SAS 或帐户 SAS 进行签名的帐户密钥，只不过它依赖于 Azure AD 凭据。 当客户端使用 OAuth 2.0 令牌请求用户委派密钥时，Azure 存储会代表用户返回用户委派密钥。

获得用户委派密钥后，可以使用该密钥在密钥的生存期内创建任意数量的用户委派共享访问签名。 用户委派密钥独立于用于获取它的 OAuth 2.0 令牌，因此只要密钥仍然有效，就不需要续订令牌。 您可以指定密钥的有效期最长为 7 天。

使用以下方法之一请求用户委派密钥：

- [获取用户委派密钥](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [获取用户授权键同步](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

以下代码段获取用户委派密钥并写入其属性：

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

以下代码段显示创建新的[BlobSasBuilder，](/dotnet/api/azure.storage.sas.blobsasbuilder)并为用户委派 SAS 提供参数。 然后，代码段调用[ToSasQuery 参数](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters)来获取 SAS 令牌字符串。 最后，代码生成完整的 URI，包括资源地址和 SAS 令牌。

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

## <a name="example-get-a-user-delegation-sas"></a>示例：获取用户委派 SAS

下面的示例方法显示了用于验证安全主体和创建用户委派 SAS 的完整代码：

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

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>示例：使用用户委派 SAS 读取 Blob

以下示例测试从模拟客户端应用程序在上一个示例中创建的用户委派 SAS。 如果 SAS 有效，则客户端应用程序能够读取 blob 的内容。 如果 SAS 无效（例如，如果已过期），Azure 存储将返回错误代码 403（禁止）。

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

## <a name="see-also"></a>请参阅

- [使用共享访问签名 (SAS) 授予对 Azure 存储资源的受限访问权限](../common/storage-sas-overview.md)
- [获取用户委派密钥操作](/rest/api/storageservices/get-user-delegation-key)
- [创建用户委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
