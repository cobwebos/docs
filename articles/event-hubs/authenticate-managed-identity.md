---
title: 使用 Azure Active Directory 身份验证托管标识
description: 本文提供了有关使用 Azure Active Directory 访问 Azure 事件中心资源对托管标识进行身份验证的信息
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cbd7de7d526e1954aaad60f7d71e5cdf202f6a29
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992830"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>使用 Azure Active Directory 验证托管标识, 以访问事件中心资源
Azure 事件中心支持[对 Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以使用 Azure 虚拟机 (Vm)、函数应用、虚拟机规模集和其他服务中运行的应用程序的 Azure AD 凭据, 授权访问事件中心资源。 通过将 Azure 资源的托管标识与 Azure AD authentication 一起使用, 你可以避免将凭据存储在云中运行的应用程序。

本文介绍如何使用 Azure VM 中的托管标识授予对事件中心的访问权限。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识
必须先为 VM 上的 Azure 资源启用托管标识, 然后才能使用 Azure 资源的托管标识来授权 VM 的事件中心资源。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>向中的托管标识授予权限 Azure AD
若要从应用程序中的托管标识向事件中心服务请求授权, 请首先为该托管标识配置基于角色的访问控制 (RBAC) 设置。 Azure 事件中心定义了 RBAC 角色, 其中包含用于从事件中心进行发送和读取的权限。 将 RBAC 角色分配到托管标识后, 将向托管标识授予适当范围内的事件中心数据的访问权限。

有关分配 RBAC 角色的详细信息, 请参阅[使用 Azure Active Directory 进行身份验证以访问事件中心资源](authorize-access-azure-active-directory.md)。

## <a name="use-event-hubs-with-managed-identities"></a>使用具有托管标识的事件中心
若要将事件中心与托管标识一起使用, 需要为标识分配角色和适当的作用域。 本节中的过程使用一个在托管标识下运行的简单应用程序, 并访问事件中心资源。

这里我们使用的是[Azure App Service](https://azure.microsoft.com/services/app-service/)中托管的示例 web 应用程序。 有关创建 web 应用程序的分步说明, 请参阅[在 Azure 中创建 ASP.NET Core web 应用](../app-service/app-service-web-get-started-dotnet.md)

创建应用程序后, 请执行以下步骤: 

1. 请参阅 "**设置**" 并选择 "**标识**"。 
1. 选择要**启用**的**状态**。 
1. 选择“保存”，保存设置。 

    ![Web 应用的托管标识](./media/authenticate-managed-identity/identity-web-app.png)

启用此设置后, 将在 Azure Active Directory (Azure AD) 中创建新的服务标识并将其配置到应用服务主机中。

现在, 请将此服务标识分配到事件中心资源的所需作用域中的角色。

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 门户分配 RBAC 角色
若要将角色分配到事件中心资源, 请在 Azure 门户中导航到该资源。 显示资源的 "访问控制 (IAM)" 设置, 并按照这些说明来管理角色分配:

> [!NOTE]
> 以下步骤将服务标识角色分配到事件中心命名空间。 你可以按照相同的步骤来分配作用域为任何事件中心资源的角色。 

1. 在 Azure 门户中, 导航到事件中心命名空间并显示命名空间的**概述**。 
1. 选择左侧菜单中的 "**访问控制 (IAM)** ", 显示事件中心的访问控制设置。
1.  选择“角色分配”选项卡以查看角色分配列表。
3.  选择 "**添加**" 以添加新角色。
4.  在 "**添加角色分配**" 页上, 选择要分配的事件中心角色。 然后搜索以查找已注册的服务标识以分配角色。
    
    !["添加角色分配" 页](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  选择**保存**。 分配有该角色的标识列出在该角色下。 例如, 下图显示服务标识具有事件中心数据所有者。
    
    ![分配给角色的标识](./media/authenticate-managed-identity/role-assigned.png)

分配角色后, web 应用程序将有权访问定义范围下的事件中心资源。 

### <a name="test-the-web-application"></a>测试 Web 应用程序
你现在可以启动 web 应用程序, 并将浏览器指向示例 aspx 页。 可以在[GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)存储库中找到从事件中心资源发送和接收数据的示例 web 应用程序。

从[Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)安装最新包, 并使用 EventHubClient 开始向事件中心发送和接收数据, 如以下代码所示: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```

## <a name="next-steps"></a>后续步骤
- 从 GitHub 下载[示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)。
- 请参阅以下文章, 了解 Azure 资源的托管标识:[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)
- 请参阅以下相关文章:
    - [使用 Azure Active Directory 对应用程序中的 Azure 事件中心的请求进行身份验证](authenticate-application.md)
    - [使用共享访问签名对 Azure 事件中心的请求进行身份验证](authenticate-shared-access-signature.md)
    - [使用 Azure Active Directory 授权访问事件中心资源](authorize-access-azure-active-directory.md)
    - [使用共享访问签名授予对事件中心资源的访问权限](authorize-access-shared-access-signature.md)