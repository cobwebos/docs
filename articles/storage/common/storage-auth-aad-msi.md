---
title: 使用 Azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限-Azure 存储
description: Azure Blob 和队列存储支持使用 Azure 资源的 Azure Active Directory 和托管标识授权访问资源。 可以使用 Azure 资源的托管标识, 通过 Azure 虚拟机中运行的应用程序、函数应用、虚拟机规模集和其他资源来授予对 blob 和队列的访问权限。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2d0697567bfd2fcf95a1fe6ebf246646af5650c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564894"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>使用 Azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限

Azure Blob 和队列存储支持使用 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以使用 Azure 虚拟机 (Vm)、函数应用、虚拟机规模集和其他服务中运行的应用程序的 Azure AD 凭据, 授权对 blob 和队列数据的访问。 通过将 Azure 资源的托管标识与 Azure AD authentication 一起使用, 你可以避免将凭据存储在云中运行的应用程序。  

本文介绍如何使用 Azure 虚拟机中的托管标识授权访问 blob 或队列数据。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识

必须先为 VM 上的 Azure 资源启用托管标识, 才能使用 Azure 资源的托管标识授予对 VM 中的 blob 和队列的访问权限。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>向 Azure AD 托管标识授予权限

若要从 Azure 存储应用程序中的托管标识对 Blob 或队列服务的请求进行授权, 请首先为该托管标识配置基于角色的访问控制 (RBAC) 设置。 Azure 存储空间定义包含对 blob 和队列数据的权限的 RBAC 角色。 将 RBAC 角色分配给托管标识后, 托管标识将被授予适当范围内的 blob 或队列数据权限。

有关分配 RBAC 角色的详细信息, 请参阅以下文章之一:

- [在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-portal.md)
- [在 Azure CLI 中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-cli.md)
- [通过 PowerShell 使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>Azure 存储资源 ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 代码示例：创建块 Blob

此代码示例演示如何从 Azure AD 获取 OAuth 2.0 令牌, 并使用它来授权创建块 blob 的请求。 若要使此示例正常工作, 请首先按照前面几节中所述的步骤进行操作。

适用于 .NET 的[Microsoft Azure 应用程序身份验证](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)客户端库 (预览版) 可简化从代码中获取和续订令牌的过程。 应用身份验证客户端库自动管理身份验证。 库使用开发人员的凭据在本地开发期间进行身份验证。 在本地开发期间使用开发人员凭据更安全，因为不需创建 Azure AD 凭据，或者不需在开发人员之间共享凭据。 稍后将解决方案部署到 Azure 后, 库会自动切换到使用应用程序凭据。

### <a name="install-packages"></a>安装包

若要使用 Azure 存储应用程序中的应用身份验证库, 请从[Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)安装最新的预览版包, 并安装适用于 .Net 和 azure Blob 存储客户端库的最新[azure 存储空间客户端](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)库[适用于 .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)。 将以下**using**语句添加到代码中:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

### <a name="add-the-callback-method"></a>添加回调方法

回调方法会检查令牌的过期时间, 并根据需要进行续订:

```csharp
private static async Task<NewTokenAndFrequency> TokenRenewerAsync(Object state, CancellationToken cancellationToken)
{
    // Specify the resource ID for requesting Azure AD tokens for Azure Storage.
    // Note that you can also specify the root URI for your storage account as the resource ID.
    const string StorageResource = "https://storage.azure.com/";  

    // Use the same token provider to request a new token.
    var authResult = await ((AzureServiceTokenProvider)state).GetAuthenticationResultAsync(StorageResource);

    // Renew the token 5 minutes before it expires.
    var next = (authResult.ExpiresOn - DateTimeOffset.UtcNow) - TimeSpan.FromMinutes(5);
    if (next.Ticks < 0)
    {
        next = default(TimeSpan);
        Console.WriteLine("Renewing token...");
    }

    // Return the new token and the next refresh time.
    return new NewTokenAndFrequency(authResult.AccessToken, next);
}
```

### <a name="get-a-token-and-create-a-block-blob"></a>获取令牌并创建块 blob

应用身份验证库提供了**AzureServiceTokenProvider**类。 此类的实例可以传递到回调以获取令牌, 然后在令牌过期之前续订令牌。

下面的示例将获取一个令牌, 并使用它来创建新的 blob, 然后使用同一个令牌读取 blob。

```csharp
const string blobName = "https://storagesamples.blob.core.windows.net/sample-container/blob1.txt";

// Get the initial access token and the interval at which to refresh it.
AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
var tokenAndFrequency = await TokenRenewerAsync(azureServiceTokenProvider,CancellationToken.None);

// Create storage credentials using the initial token, and connect the callback function
// to renew the token just before it expires
TokenCredential tokenCredential = new TokenCredential(tokenAndFrequency.Token,
                                                        TokenRenewerAsync,
                                                        azureServiceTokenProvider,
                                                        tokenAndFrequency.Frequency.Value);

StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a blob using the storage credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName),
                                            storageCredentials);

// Upload text to the blob.
await blob.UploadTextAsync(string.Format("This is a blob named {0}", blob.Name));

// Continue to make requests against Azure Storage.
// The token is automatically refreshed as needed in the background.
do
{
    // Read blob contents
    Console.WriteLine("Time accessed: {0} Blob Content: {1}",
                        DateTimeOffset.UtcNow,
                        await blob.DownloadTextAsync());

    // Sleep for ten seconds, then read the contents of the blob again.
    Thread.Sleep(TimeSpan.FromSeconds(10));
} while (true);
```

有关应用程序身份验证库的详细信息, 请参阅[使用 .net Azure Key Vault 的服务到服务身份验证](../../key-vault/service-to-service-authentication.md)。

若要了解有关如何获取访问令牌的详细信息, 请参阅[如何在 azure 虚拟机上使用 azure 资源的托管标识获取访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

> [!NOTE]
> 若要使用 Azure AD 对 blob 或队列数据的请求进行授权, 必须对这些请求使用 HTTPS。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[使用 RBAC 管理存储数据的访问权限](storage-auth-aad-rbac.md)。
- 若要了解如何从存储应用程序内授予容器和队列访问权限，请参阅[将 Azure AD 与存储应用程序配合使用](storage-auth-aad-app.md)。
- 若要了解如何使用 Azure AD 凭据运行 Azure CLI 和 PowerShell 命令, 请参阅[使用 Azure AD 凭据运行 Azure CLI 或 powershell 命令以访问 blob 或队列数据](storage-auth-aad-script.md)。
