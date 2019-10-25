---
title: 结合使用 Azure 资源的托管标识与 Azure 服务总线
description: 结合使用 Azure 资源的托管标识与 Azure 服务总线
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: 57c52640262854037420c1679804f611394230ef
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793153"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>使用 Azure Active Directory 验证托管标识以访问 Azure 服务总线资源
[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)是一项跨 Azure 功能，可便于用户创建与其中运行应用程序代码的部署关联的安全标识。 然后可以将该标识与访问控制角色进行关联，后者授予的自定义权限可用于访问应用程序需要的特定 Azure 资源。

借助托管标识，Azure 平台可管理此运行时标识。 对于标识本身和需要访问的资源，都不需要在应用程序代码或配置中存储和保护访问密钥。 如果服务总线客户端应用在 Azure 应用服务应用程序内或在虚拟机中运行，且启用了 Azure 资源的托管标识支持，则无需处理 SAS 规则和密钥或其他任何访问令牌。 客户端应用只需要服务总线消息传递命名空间的终结点地址。 当应用连接时，服务总线通过本文后面示例中展示的操作，将托管实体的上下文绑定到客户端。 与托管标识关联后，服务总线客户端便能执行所有授权操作。 授权是通过关联托管标识与服务总线角色进行授予。 

## <a name="overview"></a>概述
当安全主体（用户、组或应用程序）尝试访问服务总线实体时，必须对该请求进行授权。 使用 Azure AD，访问资源的过程分为两个步骤。 

 1. 首先，对安全主体的身份进行身份验证，并返回 OAuth 2.0 令牌。 请求令牌的资源名称是 `https://servicebus.azure.net`。
 1. 接下来，令牌作为请求的一部分传递到服务总线服务，以授权访问指定的资源。

身份验证步骤要求应用程序请求在运行时包含 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（如 Azure VM、虚拟机规模集或 Azure Function app）内运行，则它可以使用托管标识来访问资源。 

授权步骤要求向安全主体分配一个或多个 RBAC 角色。 Azure 服务总线提供了包含服务总线资源的权限集的 RBAC 角色。 分配给安全主体的角色确定主体将具有的权限。 若要详细了解如何将 RBAC 角色分配到 Azure 服务总线，请参阅[Azure 服务总线的内置 RBAC 角色](#built-in-rbac-roles-for-azure-service-bus)。 

向服务总线发出请求的本机应用程序和 web 应用程序还可以授权 Azure AD。 本文介绍如何请求访问令牌，并使用它对服务总线资源的请求进行授权。 


## <a name="assigning-rbac-roles-for-access-rights"></a>为访问权限分配 RBAC 角色
Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 服务总线定义一组内置 RBAC 角色，其中包含用于访问服务总线实体的常用权限集，还可以定义用于访问数据的自定义角色。

将 RBAC 角色分配到 Azure AD 安全主体时，Azure 会向该安全主体授予对这些资源的访问权限。 访问的范围可以是订阅、资源组或服务总线命名空间的级别。 Azure AD 安全主体可以是用户、组、应用程序服务主体或 Azure 资源的托管标识。

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Azure 服务总线的内置 RBAC 角色
对于 Azure 服务总线，通过 Azure 门户和 Azure 资源管理 API 对命名空间和所有相关资源的管理已使用*基于角色的访问控制* (RBAC) 模型进行了保护。 Azure 提供以下内置 RBAC 角色，用于授权访问服务总线命名空间：

- [Azure 服务总线数据所有者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)：允许对服务总线命名空间及其实体（队列、主题、订阅和筛选器）进行数据访问
- [Azure 服务总线数据发送方](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)：使用此角色授予对服务总线命名空间及其实体的发送访问权限。
- [Azure 服务总线数据接收方](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)：使用此角色授予对服务总线命名空间及其实体的接收访问权限。 

## <a name="resource-scope"></a>资源范围 
向安全主体分配 RBAC 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定，始终最好只授予最小的可能范围。

以下列表描述了从最窄的范围开始，可以将对服务总线资源的访问范围限定为的级别：

- **队列**、**主题**或**订阅**：角色分配适用于特定的服务总线实体。 目前，Azure 门户不支持在订阅级别将用户/组/托管标识分配到服务总线 RBAC 角色。 下面是使用 Azure CLI 命令的示例： [az-role-create-create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) To a SERVICE Bus RBAC role： 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **服务总线命名空间**：角色分配跨越命名空间和与其关联的使用者组的整个服务总线拓扑。
- **资源组**：角色分配适用于资源组下的所有服务总线资源。
- **订阅**：角色分配适用于订阅中所有资源组中的所有服务总线资源。

> [!NOTE]
> 请记住，RBAC 角色分配可能需要长达五分钟才能传播。 

有关如何定义内置角色的详细信息，请参阅[了解角色定义](../role-based-access-control/role-definitions.md#management-and-data-operations)。 有关创建自定义 RBAC 角色的详细信息，请参阅[为 Azure 基于角色的访问控制创建自定义角色](../role-based-access-control/custom-roles.md)。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识
必须先为 VM 上的 Azure 资源启用托管标识，然后才能使用 Azure 资源的托管标识来授权 VM 中的服务总线资源。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>向中的托管标识授予权限 Azure AD
若要从应用程序中的托管标识对服务总线服务的请求进行授权，请首先为该托管标识配置基于角色的访问控制（RBAC）设置。 Azure 服务总线定义了 RBAC 角色，其中包含用于从服务总线进行发送和读取的权限。 将 RBAC 角色分配给托管标识后，会向托管标识授予对相应范围内的服务总线实体的访问权限。

有关分配 RBAC 角色的详细信息，请参阅[使用 Azure Active Directory 进行身份验证和授权，以访问服务总线资源](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus)。

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>结合使用服务总线与 Azure 资源的托管标识
若要将服务总线用于托管标识，需要为该角色分配角色和相应的作用域。 本节中的过程使用一个在托管标识下运行的简单应用程序，并访问服务总线资源。

这里我们使用的是[Azure App Service](https://azure.microsoft.com/services/app-service/)中托管的示例 web 应用程序。 有关创建 web 应用程序的分步说明，请参阅[在 Azure 中创建 ASP.NET Core web 应用](../app-service/app-service-web-get-started-dotnet.md)

创建应用程序后，请执行以下步骤： 

1. 请参阅 "**设置**" 并选择 "**标识**"。 
1. 选择要**启用**的**状态**。 
1. 选择“保存”，保存设置。 

    ![Web 应用的托管标识](./media/service-bus-managed-service-identity/identity-web-app.png)

启用此设置后，将在 Azure Active Directory （Azure AD）中创建新的服务标识并将其配置到应用服务主机中。

现在，请将此服务标识分配到服务总线资源的所需作用域中的角色。

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 门户分配 RBAC 角色
若要将角色分配到服务总线命名空间，请导航到 Azure 门户中的命名空间。 显示资源的 "访问控制（IAM）" 设置，并按照这些说明来管理角色分配：

> [!NOTE]
> 以下步骤将服务标识角色分配到服务总线命名空间。 可以按照相同的步骤在其他支持的作用域（资源组和订阅）上分配角色。 
> 
> 如果没有[服务总线消息传递命名空间](service-bus-create-namespace-portal.md)，请创建一个。 

1. 在 Azure 门户中，导航到服务总线命名空间，并显示该命名空间的**概述**。 
1. 选择左侧菜单中的 "**访问控制（IAM）** "，显示服务总线命名空间的访问控制设置。
1.  选择“角色分配”选项卡以查看角色分配列表。
3.  选择 "**添加**" 以添加新角色。
4.  在 "**添加角色分配**" 页上，选择要分配的 Azure 服务总线角色。 然后搜索以查找已注册的服务标识以分配角色。
    
    !["添加角色分配" 页](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  选择“保存”。 分配有该角色的标识列出在该角色下。 例如，下图显示服务标识具有 Azure 服务总线数据所有者。
    
    ![分配给角色的标识](./media/service-bus-managed-service-identity/role-assigned.png)

分配角色后，web 应用程序将有权访问定义的作用域下的服务总线实体。 

### <a name="run-the-app"></a>运行应用

现在，修改你创建的 ASP.NET 应用程序的默认页面。 可以使用[此 GitHub 存储库](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)中的 Web 应用程序代码。  

Default.aspx 页是登陆页面。 可以在 Default.aspx.cs 文件中找到代码。 结果是一个最小的 Web 应用程序，其中包含几个输入字段以及用来连接到服务总线以发送或接收消息的 **send** 和 **receive** 按钮。

注意 [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 对象是如何初始化的。 此代码通过 `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` 调用为托管标识创建令牌提供程序，而不是使用共享访问令牌 (SAS) 令牌提供程序。 因此，不需要保留和使用任何机密。 从托管标识上下文到服务总线的流以及授权握手都是由令牌提供程序自动处理。 这是比使用 SAS 更简单的模型。

进行这些更改后，发布并运行应用程序。 若要轻松获取正确的发布数据，可下载发布配置文件，并在 Visual Studio 中导入它：

![获取发布配置文件](./media/service-bus-managed-service-identity/msi3.png)
 
若要发送或接收消息，请输入所创建的命名空间和实体的名称。 然后，单击“发送”或“接收”。


> [!NOTE]
> - 托管标识仅适用于 Azure 环境、应用服务、Azure VM 和规模集。 对于 .NET 应用程序，Microsoft.Azure.Services.AppAuthentication 库（由服务总线 NuGet 包使用）提供此协议的摘要并支持本地开发体验。 此库还允许通过 Visual Studio、Azure CLI 2.0 或 Active Directory 集成身份验证使用用户帐户，在开发计算机上对代码进行本地测试。 有关此库的本地开发选项的详细信息，请参阅[使用 .NET 向 Azure Key Vault 进行服务到服务身份验证](../key-vault/service-to-service-authentication.md)。  
> 
> - 目前，托管标识无法用于应用服务部署槽位。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
