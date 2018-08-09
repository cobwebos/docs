---
title: 通过 Azure VM 托管服务标识使用 Azure AD 验证身份（预览版）| Microsoft Docs
description: 通过 Azure VM 托管服务标识使用 Azure AD 验证身份（预览版）。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 6ddae66ee6408a3cab905826cd0d7c0831607d33
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526379"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>通过 Azure 托管服务标识使用 Azure AD 验证身份（预览版）

Azure 存储支持使用[托管服务标识](../../active-directory/managed-service-identity/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。 托管服务标识 (MSI) 在 Azure Active Directory (Azure AD) 中提供自动托管的标识。 可使用 MSI 从在 Azure 虚拟机、函数应用、虚拟机规模集等中运行的应用程序向 Azure 存储进行身份验证。 通过使用 MSI 并利用 Azure AD 身份验证功能，可避免将凭据随在云中运行的应用程序一起存储。  

若要向存储容器或队列的托管服务标识授予权限，可将包含存储权限的 RBAC 角色分配给 MSI。 有关存储中的 RBAC 角色的详细信息，请参阅[使用 RBAC 管理存储数据访问权限（预览版）](storage-auth-aad-rbac.md)。 

> [!IMPORTANT]
> 此预览版仅用于非生产用途。 适用于 Azure 存储的 Azure AD 集成正式发布后，生产服务级别协议 (SLA) 方可使用。 如果你的方案尚不支持 Azure AD 集成，请继续使用应用程序中的共享密钥授权或 SAS 令牌。 有关该预览版的其他信息，请参阅[使用 Azure Active Directory进行 Azure 存储访问权限身份验证（预览版）](storage-auth-aad.md)。
>
> 预览期间，RBAC 角色分配可能需要长达五分钟的时间进行传播。

本文介绍如何从 Azure VM 使用 MSI 向 Azure 存储进行身份验证。  

## <a name="enable-msi-on-the-vm"></a>在 VM 上启用 MSI

在从 VM 使用 MSI 向 Azure 存储进行身份验证前，必须先在 VM 中启用 MSI。 若要了解如何启用 MSI，请参阅以下文章之一：

- [Azure 门户](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Azure 资源管理器模板](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>获取 MSI 访问令牌

若要使用 MSI 进行身份验证，应用程序或脚本必须获取 MSI 访问令牌。 若要了解如何获取访问令牌，请参阅[如何使用 Azure VM 托管服务标识 (MSI) 获取令牌](../../active-directory/managed-service-identity/how-to-use-vm-token.md)。

## <a name="net-code-example-create-a-block-blob"></a>.NET 代码示例：创建块 blob

代码示例假定你拥有 MSI 访问令牌。 访问令牌用于授权托管服务标识创建块 blob。

### <a name="add-references-and-using-statements"></a>添加引用和 using 语句  

在 Visual Studio 中，安装 Azure 存储客户端库的预览版本。 在“工具”菜单中，依次选择“NuGet 包管理器”和“包管理器控制台”。 在控制台中键入以下命令：

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

向代码添加以下 using 语句：

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>通过 MSI 访问令牌创建凭据

若要创建块 blob，请使用预览版程序包提供的 **TokenCredentials** 类。 构造新的 **TokenCredentials** 实例，传入之前获取的 MSI 访问令牌：

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Azure AD 与 Azure 存储集成要求用户使用 HTTPS 进行 Azure 存储操作。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[通过 RBAC 管理存储数据访问权限（预览）](storage-auth-aad-rbac.md)。
- 若要了解如何从存储应用程序内授予容器和队列访问权限，请参阅[将 Azure AD 与存储应用程序配合使用](storage-auth-aad-app.md)。
- 若要了解如何使用 Azure AD 标识登录 Azure CLI 和 PowerShell，请参阅[使用 Azure AD 标识通过 CLI 或 PowerShell 访问 Azure 存储（预览版）](storage-auth-aad-script.md)。
- 有关适用于 Azure Blob 和队列的 Azure AD 集成的其他信息，请参阅 Azure 存储团队博客文章[宣布推出适用于 Azure 存储的 Azure AD 身份验证预览版](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)。
