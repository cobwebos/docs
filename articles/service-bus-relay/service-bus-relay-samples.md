---
title: "Azure 服务总线中继示例概述 | Microsoft Docs"
description: "分类并介绍相互链接的服务总线中继示例。"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9237a9a2-f126-4d3f-9f9b-604ee6b32153
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/13/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 6027c973f0104fff9705a70f7812f62562165c1e
ms.openlocfilehash: d830770c49caeacf762382ce1c6113d9196a5994
ms.lasthandoff: 02/14/2017


---
# <a name="service-bus-relay-samples"></a>服务总线中继示例
服务总线中继示例演示[服务总线中继](https://azure.microsoft.com/services/service-bus/)中的关键功能。 本文分类并介绍了可用的示例，每个示例均具有链接。

> [!NOTE]
> 服务总线示例未随 Azure SDK 安装。 若要获取这些示例，请访问 [Azure SDK 示例页](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5)。
> 
> 此外，[此处](https://github.com/Azure-Samples/azure-servicebus-relay-samples)还有一组更新的服务总线中继示例。  
> 
> 

有关服务总线消息传送的示例，请参阅[服务总线消息传送示例](../service-bus-messaging/service-bus-samples.md)。

## <a name="azure-service-bus-relay"></a>Azure 服务总线中继
下面的示例说明了如何编写使用 Azure 中继服务的应用程序。

请注意，中继示例需要连接字符串才能访问中继命名空间。

### <a name="to-obtain-a-connection-string-for-azure-relay"></a>获取 Azure 中继的连接字符串
1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 在“所有资源”窗格中单击展开命名空间列表。
3. 在列表中，单击中继命名空间的名称。
4. 在命名空间边栏选项卡中，单击“共享访问策略”。
5. 在“共享访问策略”边栏选项卡中，单击“RootManageSharedAccessKey”。
6. 将连接字符串复制到剪贴板。

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>获取 Windows Server 服务总线的连接字符串
1. 运行以下 Azure Powershell cmdlet：
   
    ```powershell
    get-sbClientConfiguration
    ```
2. 将连接字符串粘贴到示例的 App.config 文件中。

## <a name="azure-relay"></a>Azure 中继
演示 Azure 中继的示例。

### <a name="getting-started"></a>入门
| 示例名称 | 说明 | 最低 SDK 版本 | 可用性 |
| --- | --- | --- | --- |
| [WCF 中继消息传送：Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3) |演示如何在 Azure 上运行服务总线客户端和服务。 本示例以编程方式配置服务总线。 唯一的环境和安全信息存储在配置文件中。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送身份验证：共享密钥](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02) |演示如何使用颁发者名称和颁发者密码对服务总线进行身份验证。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送身份验证：WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831) |演示如何公开不需要客户端用户身份验证的 HTTP 服务。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送绑定：WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0) |演示如何使用 **WebHttpRelayBinding** 绑定返回使用 Web 编程模型的二进制数据。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送绑定：NetTcp 中继](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692) |演示如何使用 **NetTcpRelayBinding** 绑定。 |1.8 |Microsoft Azure Service Bus |

### <a name="exploring-features"></a>探索功能
演示各种服务总线中继功能的示例。

| 示例名称 | 说明 | 最低 SDK 版本 | 可用性 |
| --- | --- | --- | --- |
| [WCF 中继消息传送身份验证：简单的 WebToken](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392) |演示如何使用简单的 Web 令牌凭据进行服务总线身份验证。 该示例类似于 Echo 示例，但具有一些更改。 具体而言，此示例在 ServiceHost（服务）和 ChannelFactory （客户端）应用程序中添加了一个动作。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送：负载平衡](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8) |演示如何使用 Microsoft Azure 服务总线将消息路由到多个接收方。 它介绍了通过 **NetTcpRelayBinding** 绑定与客户端进行的简单服务通信的多个实例 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送绑定：Net 事件](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977) |演示如何使用 Microsoft Azure 服务总线上的 **NetEventRelayBinding** 绑定。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送绑定：WS2007Http 会话](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb) |演示如何使用启用了可靠会话的 **WS2007HttpRelayBinding** 绑定。 还演示如何在配置文件中而不是以编程方式指定服务总线凭据。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送绑定：WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5) |演示如何使用具有消息安全的 **WS2007HttpRelayBinding** 绑定来确保端到端消息安全，并且仍要求客户端对服务总线进行身份验证。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送：元数据交换](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e) |演示如何公开使用中继绑定的元数据终结点。 以下中继绑定支持 **MetadataExchange**：**NetTcpRelayBinding**、**NetOnewayRelayBinding**、**BasicHttpRelayBinding** 和 **WS2007HttpRelayBinding**。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送绑定：NetTcp Direct](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161) |演示如何将 **NetTcpRelayBinding** 绑定配置为支持**混合**连接模式，这种连接模式首先建立一个中继连接，可能的话，会自动切换到客户端和服务之间的直接连接。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送绑定：NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392) |演示如何使用具有消息安全的 **NetTcpRelayBinding** 绑定。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送绑定：Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a) |演示如何使用 **NetOnewayRelayBinding** 绑定公开和使用服务终结点。 |1.8 |Microsoft Azure Service Bus |
| [WCF 中继消息传送绑定：WS2007Http Simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a) |演示如何使用 **WS2007HttpRelayBinding** 绑定。 演示了不使用安全选项且不需要客户端进行身份验证的简单服务。 |1.8 |Microsoft Azure Service Bus |

## <a name="next-steps"></a>后续步骤
请参阅以下主题了解服务总线的概念性概述。

* [Azure 中继概述](relay-what-is-it.md)
* [服务总线体系结构](../service-bus-messaging/service-bus-architecture.md)
* [服务总线基础知识](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)


