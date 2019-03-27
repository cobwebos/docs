---
title: Azure 资源的 Azure 存储 blob 和队列与 Azure Active Directory 管理标识的访问进行身份验证 |Microsoft Docs
description: Azure Blob 和队列存储支持使用 Azure 资源的托管标识进行 Azure Active Directory 身份验证。 可以使用 Azure 资源的托管标识在应用程序中验证对 Blob 和队列的访问权限，此类应用程序可以运行在 Azure 虚拟机、函数应用、虚拟机规模集等位置中。 通过使用 Azure 资源的托管标识并利用 Azure AD 身份验证功能，可避免将凭据随在云中运行的应用程序一起存储。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 76bf36f63051f02d6c37261799342f424609f9b2
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58442303"
---
# <a name="authenticate-access-to-blobs-and-queues-with-managed-identities-for-azure-resources"></a>进行对 blob 和队列管理的标识访问 Azure 资源的身份验证

Azure Blob 和队列存储支持使用 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以从 Azure 虚拟机 (VM)、函数应用、虚拟机规模集等位置中运行的应用程序使用 Azure AD 凭据验证对 Blob 和队列的访问权限。 通过使用 Azure 资源的托管标识并利用 Azure AD 身份验证功能，可避免将凭据随在云中运行的应用程序一起存储。  

若要向 Blob 容器或队列的托管标识授予权限，请将基于角色的访问控制 (RBAC) 角色分配给托管标识，该标识包含的权限适用于适当范围的该资源。 有关在存储中的 RBAC 角色的详细信息，请参阅[存储的数据使用 RBAC 管理访问权限](storage-auth-aad-rbac.md)。 

本文介绍如何使用 Azure VM 中的托管标识向 Azure Blob 或队列存储进行身份验证。  

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识

在使用 Azure 资源的托管标识对 VM 中 Blob 和队列的访问权限进行验证之前，必须首先在 VM 上启用针对 Azure 资源的托管标识。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure 资源管理器模板](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="assign-an-rbac-role-to-an-azure-ad-managed-identity"></a>将 RBAC 角色分配给 Azure AD 托管标识

若要从 Azure 存储应用程序对托管标识进行身份验证，请先为该托管标识配置基于角色的访问控制 (RBAC) 设置。 Azure 存储定义包含容器和队列权限的 RBAC 角色。 将 RBAC 角色分配给托管标识时，将授予该托管标识对此资源的访问权限。 有关详细信息，请参阅[到 Azure Blob 和队列数据，使用 RBAC 管理访问权限](storage-auth-aad-rbac.md)。

## <a name="get-a-managed-identity-access-token"></a>获取托管标识访问令牌

若要使用托管标识进行身份验证，应用程序或脚本必须获取托管标识访问令牌。 若要了解如何获取访问令牌，请参阅[如何在 Azure VM 上使用 Azure 资源的托管标识获取访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

要使用 OAuth 令牌授权 blob 和队列操作，必须使用 HTTPS。

## <a name="net-code-example-create-a-block-blob"></a>.NET 代码示例：创建块 Blob

此代码示例假定你拥有托管标识访问令牌。 访问令牌用于授权托管标识创建块 Blob。

### <a name="add-references-and-using-statements"></a>添加引用和 using 语句  

在 Visual Studio 中，安装 Azure 存储客户端库。 在“工具”菜单中，依次选择“NuGet 包管理器”和“包管理器控制台”。 在控制台中键入以下命令：

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

向代码添加以下 using 语句：

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>通过托管标识访问令牌创建凭据

若要创建块 blob，请使用**TokenCredentials**类。 构造新的 **TokenCredentials** 实例，传入之前获取的托管标识访问令牌：

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Azure AD 与 Azure 存储集成要求用户使用 HTTPS 进行 Azure 存储操作。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 存储的 RBAC 角色的详细信息，请参阅[存储的数据使用 RBAC 管理访问权限](storage-auth-aad-rbac.md)。
- 若要了解如何从存储应用程序内授予容器和队列访问权限，请参阅[将 Azure AD 与存储应用程序配合使用](storage-auth-aad-app.md)。
- 若要了解如何使用 Azure AD 标识登录到 Azure CLI 和 PowerShell，请参阅[使用 Azure AD 标识访问 Azure 存储使用 CLI 或 PowerShell](storage-auth-aad-script.md)。
