---
title: 使用 Azure Active Directory 和 Azure 资源的托管标识授权对 Blob 和队列的访问权限 - Azure 存储
description: Azure Blob 和队列存储支持使用 Azure Active Directory 和 Azure 资源的托管标识授权对资源的访问权限。 可以使用 Azure 资源的托管标识在应用程序中授权对 Blob 和队列的访问权限，此类应用程序可以运行在 Azure 虚拟机、函数应用、虚拟机规模集等位置中。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bed661873b195694c2fd9b30b1d98a3ecf1fc8a4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671106"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>使用 Azure Active Directory 和 Azure 资源的托管标识授权对 Blob 和队列的访问权限

Azure Blob 和队列存储支持使用 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以从 Azure 虚拟机 (VM)、函数应用、虚拟机规模集和其他服务中运行的应用程序使用 Azure AD 凭据授权对 Blob 和队列数据的访问权限。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随在云中运行的应用程序一起存储。  

本文介绍如何在 Azure VM 中使用托管标识授权对 Blob 或队列数据的访问权限。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识

在使用 Azure 资源的托管标识对 VM 中 Blob 和队列的访问权限进行授权之前，必须首先在 VM 上启用针对 Azure 资源的托管标识。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-an-azure-ad-managed-identity"></a>向 Azure AD 托管标识授予权限

若要通过 Azure 存储应用程序中的托管标识授权对 Blob 或队列服务的请求，请先为该托管标识配置基于角色的访问控制 (RBAC) 设置。 Azure 存储定义了包含 Blob 和队列数据权限的 RBAC 角色。 将 RBAC 角色分配到某个托管标识后，将在适当的范围授予该托管标识对 Blob 或队列数据的这些权限。

有关分配 RBAC 角色的详细信息，请参阅以下文章之一：

- [在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-portal.md)
- [在 Azure CLI 中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-cli.md)
- [通过 PowerShell 使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-powershell.md)

## <a name="azure-storage-resource-id"></a>Azure 存储资源 ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET 代码示例：创建块 Blob

此代码示例演示如何从 Azure AD 获取 OAuth 2.0 令牌，并使用它来授权创建块 Blob 的请求。 若要正常运行此示例，请先遵循前面部分所述的步骤。

[!INCLUDE [storage-app-auth-lib-include](../../../includes/storage-app-auth-lib-include.md)]

### <a name="add-the-callback-method"></a>添加回调方法

回调方法将检查令牌的过期时间，并根据需要续订令牌：

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

### <a name="get-a-token-and-create-a-block-blob"></a>获取令牌并创建块 Blob

应用身份验证库提供 **AzureServiceTokenProvider** 类。 可将此类的实例传递给某个回调，该回调将获取令牌，然后在令牌过期之前续订令牌。

以下示例将获取一个令牌，并使用它来创建新的 Blob，然后使用同一个令牌来读取 Blob。

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

有关应用身份验证库的详细信息，请参阅[使用 .NET 对 Azure Key Vault 进行服务到服务的身份验证](../../key-vault/service-to-service-authentication.md)。

若要详细了解如何获取访问令牌，请参阅[如何在 Azure VM 上使用 Azure 资源的托管标识获取访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

> [!NOTE]
> 若要使用 Azure AD 授权针对 Blob 或队列数据的请求，必须对这些请求使用 HTTPS。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[使用 RBAC 管理存储数据的访问权限](storage-auth-aad-rbac.md)。
- 若要了解如何从存储应用程序内授予容器和队列访问权限，请参阅[将 Azure AD 与存储应用程序配合使用](storage-auth-aad-app.md)。
- 若要了解如何使用 Azure AD 凭据运行 Azure CLI 和 PowerShell 命令，请参阅[使用 Azure AD 凭据运行 Azure CLI 或 PowerShell 命令以访问 Blob 或队列数据](storage-auth-aad-script.md)。
