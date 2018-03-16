---
title: "Azure 服务总线的托管服务标识预览版 | Microsoft Docs"
description: "将托管服务标识与 Azure 服务总线配合使用"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 7b9901ee3478cb193c808b65d2dbbcf8b596a3c1
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="managed-service-identity-preview"></a>托管服务标识（预览）

托管服务标识 (MSI) 是跨整个 Azure 的一项功能，可以用来创建与部署（在其中运行应用程序代码）相关联的安全标识。 然后可以将该标识与访问控制角色进行关联，后者授予的自定义权限可用于访问应用程序需要的特定 Azure 资源。

Azure 平台借助 MSI 来管理此运行时标识。 对于标识本身和需要访问的资源，都不需要在应用程序代码或配置中存储和保护访问密钥。 在启用了 MSI 支持的 Azure 应用服务应用程序内或虚拟机中运行的服务总线客户端应用不需要处理 SAS 规则和密钥，也不需要处理任何其他访问令牌。 客户端应用只需要服务总线消息传递命名空间的终结点地址。 当应用进行连接时，服务总线通过一个操作将 MSI 上下文绑定到该客户端，本文后面的一个示例展示了该操作。 

与托管服务标识关联后，服务总线客户端可以执行所有经授权的操作。 授权是通过将 MSI 与服务总线角色相关联来授予的。 

## <a name="service-bus-roles-and-permissions"></a>服务总线角色和权限

对于初始的公共预览版本，只能将托管服务标识添加到某个服务总线命名空间的“所有者”或“参与者”角色，这将向该标识授予对该命名空间中所有实体的完全控制权限。 但是，最初只能通过 Azure 资源管理器来支持对命名空间拓扑进行更改的管理操作，不能通过本机服务总线 REST 管理接口来支持。 此支持还意味着无法在托管服务标识内使用 .NET Framework 客户端 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 对象。

## <a name="use-service-bus-with-a-managed-service-identity"></a>将服务总线与托管服务标识配合使用

下面的部分介绍了创建和部署在托管服务标识下运行的示例应用程序时所需的步骤、如何向该标识授予对服务总线消息传递命名空间的访问权限，以及该应用程序如何使用该标识与服务总线实体进行交互。

此介绍描述的是 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中托管的 Web 应用程序。 VM 托管的应用程序所需的步骤与之类似。

### <a name="create-an-app-service-web-application"></a>创建应用服务 Web 应用程序

第一步是创建应用服务 ASP.NET 应用程序。 如果不熟悉如何在 Azure 中执行此操作，请遵循[此操作指南](../app-service/app-service-web-get-started-dotnet-framework.md)。 不过，不是像此教程中所示的那样创建 MVC 应用程序，而是要创建 Web 窗体应用程序。

### <a name="set-up-the-managed-service-identity"></a>设置托管服务标识

创建应用程序后，在 Azure 门户中导航到新创建的 Web 应用（也显示在操作说明中），然后导航到“托管服务标识”页面并启用此功能： 

![](./media/service-bus-managed-service-identity/msi1.png)

启用此功能后，会在 Azure Active Directory 中创建一个新的服务标识并将其配置到应用服务主机中。

### <a name="create-a-new-service-bus-messaging-namespace"></a>创建新的服务总线消息传递命名空间

接下来，在支持 RBAC 预览版的 Azure 区域（**美国东部**、**美国东部 2** 或**西欧**）之一中[创建服务总线消息传递命名空间](service-bus-create-namespace-portal.md)。 

在门户上导航到命名空间“访问控制(标识和访问管理)”页面，然后单击“添加”将托管服务标识添加到“所有者”角色。 为此，请在“添加权限”面板的“选择”字段中搜索 Web 应用程序的名称，然后单击该条目。 然后单击“保存”。

![](./media/service-bus-managed-service-identity/msi2.png)
 
Web 应用程序的托管服务标识现在已具有对服务总线命名空间和对之前创建的队列的访问权限。 

### <a name="run-the-app"></a>运行应用程序

现在，修改所创建的 ASP.NET 应用程序的默认页面。 还可以使用[此 GitHub 存储库](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ManagedServiceIdentity)中的 Web 应用程序代码。

Default.aspx 页是登陆页面。 可以在 Default.aspx.cs 文件中找到代码。 结果是一个最小的 Web 应用程序，其中包含几个输入字段以及用来连接到服务总线以发送或接收消息的 **send** 和 **receive** 按钮。

注意 [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 对象是如何初始化的。 该代码通过 `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` 调用为托管服务标识创建令牌提供程序，而不是使用共享访问令牌 (SAS) 令牌提供程序。 因此，不需要保留和使用任何机密。 从托管服务标识上下文到服务总线的流以及授权握手都是由令牌提供程序自动处理的，该令牌提供程序是一个比使用 SAS 更简单的模型。

进行这些更改后，发布并运行应用程序。 获取正确的发布数据的一种简单方法是下载发布配置文件并将其导入到 Visual Studio 中：

![](./media/service-bus-managed-service-identity/msi3.png)
 
若要发送或接收消息，请输入所创建的命名空间和实体的名称，然后单击 **send** 或 **receive**。
 
请注意，托管服务标识仅在 Azure 环境中工作，并且仅在配置它时所在的应用服务部署中工作。 另请注意，目前，托管服务标识不能与应用服务部署槽位一起工作。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题。

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)