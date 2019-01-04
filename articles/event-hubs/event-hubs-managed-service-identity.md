---
title: Azure 资源的托管标识 - Azure 事件中心 | Microsoft Docs
description: 本文介绍如何将 Azure 资源托管标识与 Azure 事件中心结合使用
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 784d8c9280aeff7224f90ecee0b16c9c30381aeb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087722"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>具有事件中心的 Azure 资源托管标识

[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)是一项跨 Azure 功能，可便于用户创建与其中运行应用程序代码的部署关联的安全标识。 然后可以将该标识与访问控制角色进行关联，后者授予的自定义权限可用于访问应用程序需要的特定 Azure 资源。 

借助托管标识，Azure 平台可管理此运行时标识。 对于标识本身和需要访问的资源，都不需要在应用程序代码或配置中存储和保护访问密钥。 在启用了 Azure 资源托管标识支持的 Azure 应用服务应用程序内或虚拟机中运行的事件中心客户端应用不需要处理 SAS 规则和密钥，也不需要处理任何其他访问令牌。 客户端应用只需要事件中心命名空间的终结点地址。 当应用进行连接时，事件中心通过一个操作将托管标识的上下文绑定到该客户端，本文后面的一个示例展示了该操作。

与托管标识关联后，事件中心客户端可以执行所有经授权的操作。 授权是通过将托管标识与事件中心角色相关联来授予的。 

## <a name="event-hubs-roles-and-permissions"></a>事件中心角色和权限

可以仅将托管标识添加到某个事件中心命名空间的“所有者”或“参与者”角色，这将向该标识授予对该命名空间中所有实体的完全控制权限。 不过，最初只能通过 Azure 资源管理器来支持对命名空间拓扑进行更改的管理操作。 不通过本机事件中心 REST 管理接口来支持。 此支持还意味着，无法在托管标识内使用 .NET Framework 客户端 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 对象。 
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>将事件中心与 Azure 资源托管标识结合使用

后续部分介绍了以下步骤：

1. 创建和部署在托管标识下运行的示例应用程序。
2. 授予该标识对事件中心命名空间的访问权限。
3. 应用程序如何与使用该标识的事件中心交互。

此介绍描述的是 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中托管的 Web 应用程序。 VM 托管的应用程序所需的步骤与之类似。

### <a name="create-an-app-service-web-application"></a>创建应用服务 Web 应用程序

第一步是创建应用服务 ASP.NET 应用程序。 如果不熟悉如何在 Azure 中执行此操作，请遵循[此操作指南](../app-service/app-service-web-get-started-dotnet-framework.md)。 不过，不是像此教程中所示的那样创建 MVC 应用程序，而是要创建 Web 窗体应用程序。

### <a name="set-up-the-managed-identity"></a>设置托管标识

创建应用程序后，在 Azure 门户中导航到新创建的 Web 应用（也显示在操作说明中），然后导航到“托管服务标识”页面并启用此功能： 

![托管服务标识页面](./media/event-hubs-managed-service-identity/msi1.png)
 
启用此功能后，会在 Azure Active Directory 中创建一个新的服务标识并将其配置到应用服务主机中。

### <a name="create-a-new-event-hubs-namespace"></a>创建新的事件中心命名空间

接下来，在以下某个支持 Azure 资源托管标识预览版的 Azure 区域中[创建事件中心命名空间](event-hubs-create.md)：**美国东部**、**美国东部 2** 或**西欧**。 

转到门户上的命名空间“访问控制(标识和访问管理)”页，再单击“添加角色分配”，向“所有者”角色添加托管标识。 为此，请在“添加权限”面板的“选择”字段中搜索 Web 应用程序的名称，然后单击该条目。 然后单击“保存”。 Web 应用程序的托管标识现在已具有对事件中心命名空间和对之前创建的事件中心的访问权限。 

### <a name="run-the-app"></a>运行应用

现在，修改所创建的 ASP.NET 应用程序的默认页面。 还可以使用[此 GitHub 存储库](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp)中的 Web 应用程序代码。 

启动应用后，将浏览器指向 EventHubsMSIDemo.aspx。 还可以将其设置为起始页。 可以在 EventHubsMSIDemo.aspx.cs 文件中找到代码。 结果是一个最小的 Web 应用程序，其中包含几个输入字段以及用来连接到事件中心以发送或接收事件的“发送”和“接收”按钮。 

注意 [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 对象是如何初始化的。 此代码通过 `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` 调用为托管标识创建令牌提供程序，而不是使用共享访问令牌 (SAS) 令牌提供程序。 因此，不需要保存和使用任何机密。 从托管标识上下文到事件中心的流以及授权握手都是由令牌提供程序自动处理的，该令牌提供程序是一个比使用 SAS 更简单的模型。

进行这些更改后，发布并运行应用程序。 你可以通过下载发布配置文件然后将其导入到 Visual Studio 来获取正确的发布数据：

![导入发布配置文件](./media/event-hubs-managed-service-identity/msi3.png)
 
若要发送或接收消息，请输入所创建的命名空间和实体的名称，然后单击 **send** 或 **receive**。 
 
托管标识仅在 Azure 环境中工作，并且仅在配置它时所在的应用服务部署中工作。 目前，托管标识不能与应用服务部署槽位一起工作。

## <a name="next-steps"></a>后续步骤

有关事件中心的详细信息，请访问以下链接：

* 使用 [事件中心教程](event-hubs-dotnet-standard-getstarted-send.md)
* [事件中心常见问题解答](event-hubs-faq.md)
* [事件中心定价详细信息](https://azure.microsoft.com/pricing/details/event-hubs/)
* [使用事件中心的示例应用程序](https://github.com/Azure/azure-event-hubs/tree/master/samples)
