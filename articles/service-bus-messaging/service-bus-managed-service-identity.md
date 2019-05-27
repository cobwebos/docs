---
title: 结合使用 Azure 资源的托管标识与 Azure 服务总线（预览版）| Microsoft Docs
description: 结合使用 Azure 资源的托管标识与 Azure 服务总线
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2018
ms.author: aschhab
ms.openlocfilehash: 8477ff8c8ff0bc1629ff4cdc61f7c28c6eed778c
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978799"
---
# <a name="managed-identities-for-azure-resources-with-service-bus"></a>结合使用 Azure 资源的托管标识与 Azure 服务总线 

[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)是一项跨 Azure 功能，可便于用户创建与其中运行应用程序代码的部署关联的安全标识。 然后可以将该标识与访问控制角色进行关联，后者授予的自定义权限可用于访问应用程序需要的特定 Azure 资源。

借助托管标识，Azure 平台可管理此运行时标识。 对于标识本身和需要访问的资源，都不需要在应用程序代码或配置中存储和保护访问密钥。 如果服务总线客户端应用在 Azure 应用服务应用程序内或在虚拟机中运行，且启用了 Azure 资源的托管标识支持，则无需处理 SAS 规则和密钥或其他任何访问令牌。 客户端应用只需要服务总线消息传递命名空间的终结点地址。 当应用连接时，服务总线通过本文后面示例中展示的操作，将托管实体的上下文绑定到客户端。 与托管标识关联后，服务总线客户端便能执行所有授权操作。 授权是通过关联托管标识与服务总线角色进行授予。 

## <a name="service-bus-roles-and-permissions"></a>服务总线角色和权限

对于服务总线命名空间的"服务总线数据所有者"角色可以托管的标识。 它会授予的标识，命名空间中的所有实体的完全控制 （适用于管理和数据操作）。

>[!IMPORTANT]
> 更早版本支持添加到托管的标识 **"所有者"** 或 **"参与者"** 角色。
>
> 但是，数据访问权限 **"所有者"** 并 **"参与者"** 角色将不再起作用。 如果已使用 **"所有者"** 或 **"参与者"** 角色，则那些需要进行修改才能利用 **"服务总线数据所有者"** 角色。

若要使用新的内置角色，请完成以下步骤-

1. 请继续执行[Azure 门户](https://portal.azure.com)
2. 导航到了当前安装的"所有者"或"参与者"角色的服务总线命名空间。
3. 单击"访问控制 （iam）"的左的窗格菜单。
4. 继续按如下所示添加新的角色分配

    ![](./media/service-bus-role-based-access-control/ServiceBus_RBAC_SBDataOwner.png)

5. 按"保存"以保存新的角色分配。

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>结合使用服务总线与 Azure 资源的托管标识

下面的部分介绍了创建和部署在托管标识下运行的示例应用程序所需的步骤、如何向相应标识授予对服务总线消息传递命名空间的访问权限，以及应用程序如何使用相应标识与服务总线实体进行交互。

此介绍描述的是 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中托管的 Web 应用程序。 VM 托管的应用程序所需的步骤与之类似。

### <a name="create-an-app-service-web-application"></a>创建应用服务 Web 应用程序

第一步是创建应用服务 ASP.NET 应用程序。 如果不熟悉如何在 Azure 中执行此操作，请按照[这篇操作指南](../app-service/app-service-web-get-started-dotnet-framework.md)操作。 不过，不是像此教程中所示的那样创建 MVC 应用程序，而是要创建 Web 窗体应用程序。

### <a name="set-up-the-managed-identity"></a>设置托管标识

创建应用程序后，在 Azure 门户中导航到新创建的 Web 应用（也显示在操作说明中），然后导航到“托管服务标识”页面并启用此功能： 

![](./media/service-bus-managed-service-identity/msi1.png)

启用此功能后，会在 Azure Active Directory 中创建一个新的服务标识并将其配置到应用服务主机中。

### <a name="create-a-new-service-bus-messaging-namespace"></a>创建新的服务总线消息传递命名空间

下一步，[创建服务总线消息传送命名空间](service-bus-create-namespace-portal.md)。 

在门户上导航到命名空间“访问控制(IAM)”页面，然后单击“添加角色分配”将托管标识添加到“所有者”角色。 为此，请在“添加权限”面板的“选择”字段中搜索 Web 应用程序的名称，然后单击该条目。 然后单击“保存”。

Web 应用程序的托管标识现已拥有对服务总线命名空间和之前创建的队列的访问权限。 

### <a name="run-the-app"></a>运行应用

现在，修改你创建的 ASP.NET 应用程序的默认页面。 可以使用[此 GitHub 存储库](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)中的 Web 应用程序代码。  

Default.aspx 页是登陆页面。 可以在 Default.aspx.cs 文件中找到代码。 结果是一个最小的 Web 应用程序，其中包含几个输入字段以及用来连接到服务总线以发送或接收消息的 **send** 和 **receive** 按钮。

注意 [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 对象是如何初始化的。 此代码通过 `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` 调用为托管标识创建令牌提供程序，而不是使用共享访问令牌 (SAS) 令牌提供程序。 因此，不需要保留和使用任何机密。 从托管标识上下文到服务总线的流以及授权握手都是由令牌提供程序自动处理。 这是比使用 SAS 更简单的模型。

进行这些更改后，发布并运行应用程序。 若要轻松获取正确的发布数据，可下载发布配置文件，并在 Visual Studio 中导入它：

![](./media/service-bus-managed-service-identity/msi3.png)
 
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
