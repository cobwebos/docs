---
title: Azure 事件中心的托管服务标识预览版 | Microsoft Docs
description: 将托管服务标识与 Azure 事件中心一起使用
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2017
ms.author: sethm
ms.openlocfilehash: dd50e4f6ebc5fdf5496a5127fde20bd052087b59
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783327"
---
# <a name="managed-service-identity-preview"></a>托管服务标识（预览版）

托管服务标识 (MSI) 是跨整个 Azure 的一项功能，可以用来创建与在其中运行应用程序代码的部署关联的安全标识。 然后可以将该标识与访问控制角色进行关联，后者授予访问应用程序需要的特定 Azure 资源时所需的自定义权限。 

Azure 平台借助 MSI 来管理此运行时标识。 对于标识本身和你需要访问的资源，你都不需要在应用程序代码或配置中存储和保护访问密钥。 在启用了 MSI 支持的 Azure 应用服务应用程序内或虚拟机中运行的事件中心客户端应用不需要处理 SAS 规则和密钥，也不需要处理任何其他访问令牌。 客户端应用只需要事件中心命名空间的终结点地址。 当应用进行连接时，事件中心通过一个操作将 MSI 上下文绑定到该客户端，本文后面的一个示例展示了该操作。

与托管服务标识关联后，事件中心客户端可以执行所有经授权的操作。 授权是通过将 MSI 与事件中心角色相关联来授予的。 

## <a name="event-hubs-roles-and-permissions"></a>事件中心角色和权限

对于初始的公共预览版本，只能将托管服务标识添加到某个事件中心命名空间的“所有者”或“参与者”角色，这将向该标识授予对该命名空间中所有实体的完全控制权限。 但是，最初只能通过 Azure 资源管理器来支持对命名空间拓扑进行更改的管理操作，不能通过本机事件中心 REST 管理接口来支持。 此支持还意味着无法在托管服务标识内使用 .NET Framework 客户端 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 对象。 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>将事件中心与托管服务标识一起使用

下面的部分介绍了创建和部署在托管服务标识下运行的示例应用程序时所需的步骤，如何向该标识授予对事件中心命名空间的访问权限，以及该应用程序如何使用该标识与事件中心进行交互。

此介绍描述的是 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中托管的 Web 应用程序。 VM 托管的应用程序所需的步骤与之类似。

### <a name="create-an-app-service-web-application"></a>创建应用服务 Web 应用程序

第一步是创建应用服务 ASP.NET 应用程序。 如果不熟悉如何在 Azure 中执行此操作，请按照[此操作说明指南](../app-service/app-service-web-get-started-dotnet-framework.md)进行操作。 不过，不是像此教程中所示的那样创建 MVC 应用程序，而是要创建 Web 窗体应用程序。

### <a name="set-up-the-managed-service-identity"></a>设置托管服务标识

创建应用程序后，在 Azure 门户中导航到新创建的 Web 应用（也显示在操作说明中），然后导航到“托管服务标识”页面并启用此功能： 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
启用此功能后，会在 Azure Active Directory 中创建一个新的服务标识并将其配置到应用服务主机中。

### <a name="create-a-new-event-hubs-namespace"></a>创建新的事件中心命名空间

接下来，在支持 MSI 预览版的 Azure 区域（**美国东部**、**美国东部 2** 或**西欧**）之一中[创建事件中心命名空间](event-hubs-create.md)。 

在门户上导航到命名空间“访问控制(标识和访问管理)”页面，然后单击“添加”将托管服务标识添加到“所有者”角色。 为此，请在“添加权限”面板的“选择”字段中搜索 Web 应用程序的名称，然后单击该条目。 然后单击“保存”。

![](./media/event-hubs-managed-service-identity/msi2.png)
 
Web 应用程序的托管服务标识现在已具有对事件中心命名空间和对之前创建的事件中心的访问权限。 

### <a name="run-the-app"></a>运行应用程序

现在，修改你创建的 ASP.NET 应用程序的默认页面。 还可以使用[此 GitHub 存储库](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp)中的 Web 应用程序代码。 

启动你的应用后，将浏览器指向 EventHubsMSIDemo.aspx。 或者，将其设置为起始页。 可以在 EventHubsMSIDemo.aspx.cs 文件中找到代码。 结果是一个最小的 Web 应用程序，其中包含几个输入字段以及用来连接到事件中心以发送或接收消息的 **send** 和 **receive** 按钮。 

注意 [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 对象是如何初始化的。 该代码通过 `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` 调用为托管服务标识创建令牌提供程序，而不是使用共享访问令牌 (SAS) 令牌提供程序。 因此，不需要保留和使用任何机密。 从托管服务标识上下文到事件中心的流以及授权握手都是由令牌提供程序自动处理的，该令牌提供程序是一个比使用 SAS 更简单的模型。

进行这些更改后，发布并运行应用程序。 获取正确的发布数据的一种简单方法是下载发布配置文件并将其导入到 Visual Studio 中：

![](./media/event-hubs-managed-service-identity/msi3.png)
 
若要发送或接收消息，请输入你创建的命名空间和实体的名称，然后单击 **send** 或 **receive**。 
 
请注意，托管服务标识仅在 Azure 环境内工作，并且仅在其中配置了它的应用服务部署中工作。 另请注意，目前，托管服务标识不能与应用服务部署槽位一起工作。

## <a name="next-steps"></a>后续步骤

有关事件中心的详细信息，请访问以下链接：

* 使用 [事件中心教程](event-hubs-dotnet-standard-getstarted-send.md)
* [事件中心常见问题](event-hubs-faq.md)
* [事件中心定价详细信息](https://azure.microsoft.com/pricing/details/event-hubs/)
* [使用事件中心的示例应用程序](https://github.com/Azure/azure-event-hubs/tree/master/samples)