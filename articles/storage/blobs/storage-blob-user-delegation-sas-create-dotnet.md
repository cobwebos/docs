---
title: 使用 .NET （预览版）为容器或 blob 创建用户委托 SAS-Azure 存储
description: 了解如何使用 .NET 客户端库在 Azure 存储中使用 Azure Active Directory 凭据创建用户委托 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 59de768e75a88d7cfa5b68fa306d0e83f1aa0ba3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671324"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>使用 .NET （预览版）为容器或 blob 创建用户委托 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文介绍如何使用 Azure Active Directory （Azure AD）凭据为带有[用于 .net 的 Azure 存储客户端库](https://www.nuget.org/packages/Azure.Storage.Blobs)的容器或 blob 创建用户委托 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>安装预览包

本文中的示例使用适用于 Blob 存储的 Azure 存储客户端库的最新预览版本。 若要安装预览版包，请从 NuGet 包管理器控制台运行以下命令：

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

本文中的示例还使用[适用于 .net 的 Azure 标识客户端库](https://www.nuget.org/packages/Azure.Identity/)的最新预览版本通过 Azure AD 凭据进行身份验证。 Azure 标识客户端库对安全主体进行身份验证。 然后，经过身份验证的安全主体可创建用户委托 SAS。 有关 Azure 标识客户端库的详细信息，请参阅[适用于 .net 的 Azure 标识客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)。

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>创建服务主体

若要通过 Azure 标识客户端库对 Azure AD 凭据进行身份验证，请使用服务主体或托管标识作为安全主体，具体取决于代码的运行位置。 如果你的代码在开发环境中运行，请使用服务主体进行测试。 如果你的代码在 Azure 中运行，请使用托管标识。 本文假设您正在从开发环境运行代码，并演示如何使用服务主体来创建用户委托 SAS。

若要创建具有 Azure CLI 的服务主体并分配一个 RBAC 角色，请调用[az ad sp create for rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)命令。 提供要分配给新服务主体的 Azure 存储数据访问角色。 角色必须包含 storageAccounts/ **/blobServices/generateUserDelegationKey**操作。 有关为 Azure 存储提供的内置角色的详细信息，请参阅[azure 资源的内置角色](../../role-based-access-control/built-in-roles.md)。

此外，请提供角色分配的作用域。 服务主体将创建用户委托密钥，该密钥是在存储帐户级别执行的操作，因此角色分配的作用域应为存储帐户、资源组或订阅的级别。 有关创建用户委托 SAS 的 RBAC 权限的详细信息，请参阅[创建用户委托 sas （REST API）](/rest/api/storageservices/create-user-delegation-sas)中的**使用 rbac 分配权限**部分。

如果你没有足够的权限将角色分配给服务主体，你可能需要请求帐户所有者或管理员执行角色分配。

下面的示例使用 Azure CLI 创建新服务主体，并使用帐户范围将**存储 Blob 数据读取器**角色分配给它

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

@No__t-0 命令返回 JSON 格式的服务主体属性列表。 复制这些值，以便在下一步中使用它们来创建必要的环境变量。

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> 传播 RBAC 角色分配可能需要花费几分钟时间。

## <a name="set-environment-variables"></a>设置环境变量。

Azure 标识客户端库会在运行时读取三个环境变量中的值，以对服务主体进行身份验证。 下表介绍了为每个环境变量设置的值。

|环境变量|ReplTest1
|-|-
|`AZURE_CLIENT_ID`|服务主体的应用 ID
|`AZURE_TENANT_ID`|服务主体的 Azure AD 租户 ID
|`AZURE_CLIENT_SECRET`|为服务主体生成的密码

> [!IMPORTANT]
> 设置环境变量后，请关闭并重新打开控制台窗口。 如果你使用的是 Visual Studio 或其他开发环境，则可能需要重新启动开发环境才能注册新的环境变量。

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

## <a name="authenticate-the-service-principal"></a>对服务主体进行身份验证

若要对服务主体进行身份验证，请创建[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)类的实例。 @No__t-0 构造函数读取您之前创建的环境变量。

以下代码片段演示如何获取经过身份验证的凭据，并使用它来创建 Blob 存储服务客户端

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

## <a name="example-get-a-user-delegation-sas"></a>例如：获取用户委托 SAS

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

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>例如：使用用户委托 SAS 读取 blob

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

## <a name="see-also"></a>请参阅

- [获取用户委派密钥操作](/rest/api/storageservices/get-user-delegation-key)
- [创建用户委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
