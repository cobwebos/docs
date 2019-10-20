---
title: 使用 .NET （预览版）为容器或 blob 创建用户委托 SAS-Azure 存储
description: 了解如何使用 .NET 客户端库在 Azure 存储中使用 Azure Active Directory 凭据创建用户委托 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c75a13a20c1dbb222db69145e24838deb111fb66
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595218"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>使用 .NET （预览版）为容器或 blob 创建用户委托 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文介绍如何使用 Azure Active Directory （Azure AD）凭据为带有用于 .NET 的 Azure 存储客户端库的容器或 blob 创建用户委托 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library-preview"></a>用 Azure 标识库进行身份验证（预览版）

适用于 .NET 的 Azure 标识客户端库（预览版）对安全主体进行身份验证。 当你的代码在 Azure 中运行时，安全主体是 Azure 资源的托管标识。

当代码在开发环境中运行时，可以自动处理身份验证，或者可能需要浏览器登录，具体取决于所使用的工具。 Microsoft Visual Studio 支持单一登录（SSO），以便 active Azure AD 用户帐户自动用于身份验证。 有关 SSO 的详细信息，请参阅[对应用程序的单一登录](../../active-directory/manage-apps/what-is-single-sign-on.md)。

其他开发工具可能会提示您通过 web 浏览器登录。 你还可以使用服务主体从开发环境进行身份验证。 有关详细信息，请参阅[在门户中为 Azure 应用创建标识](../../active-directory/develop/howto-create-service-principal-portal.md)。

进行身份验证后，Azure 标识客户端库将获取令牌凭据。 然后，在创建的服务客户端对象中封装此令牌凭据，以对 Azure 存储空间执行操作。 库通过获取适当的令牌凭据，无缝地处理这种情况。

有关 Azure 标识客户端库的详细信息，请参阅[适用于 .net 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)。

## <a name="assign-rbac-roles-for-access-to-data"></a>为访问数据分配 RBAC 角色

当 Azure AD 安全主体尝试访问 blob 数据时，该安全主体必须具有对资源的权限。 无论安全主体是 Azure 中的托管标识还是在开发环境中运行代码的 Azure AD 用户帐户，都必须为安全主体分配允许访问 Azure 存储中的 blob 数据的 RBAC 角色。 有关通过 RBAC 分配权限的信息，请参阅[使用 Azure Active Directory 授予对 Azure blob 和队列的访问](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)权限中标题为**访问权限分配 RBAC 角色**的部分。

## <a name="install-the-preview-packages"></a>安装预览包

本文中的示例使用[适用于 Blob 存储的 Azure 存储客户端库](https://www.nuget.org/packages/Azure.Storage.Blobs)的最新预览版本。 若要安装预览版包，请从 NuGet 包管理器控制台运行以下命令：

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

本文中的示例还使用[适用于 .net 的 Azure 标识客户端库](https://www.nuget.org/packages/Azure.Identity/)的最新预览版本通过 Azure AD 凭据进行身份验证。 若要安装预览版包，请从 NuGet 包管理器控制台运行以下命令：

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="add-using-directives"></a>添加 using 指令

将以下 `using` 指令添加到你的代码，以使用 Azure 标识和 Azure 存储客户端库的预览版本。

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>获取经过身份验证的令牌凭据

若要获取代码可用于向 Azure 存储授权请求的令牌凭据，请创建[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)类的实例。

下面的代码段演示如何获取经过身份验证的令牌凭据，并使用它来创建 Blob 存储服务客户端：

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

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
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>创建 SAS 令牌

以下代码片段显示了如何创建新的[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) ，并提供了用户委托 SAS 的参数。 然后，该代码段调用[ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters)来获取 SAS 令牌字符串。 最后，该代码生成完整的 URI，包括资源地址和 SAS 令牌。

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

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
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

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
    catch (StorageRequestFailedException e)
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

- [获取用户委派密钥操作](/rest/api/storageservices/get-user-delegation-key)
- [创建用户委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
