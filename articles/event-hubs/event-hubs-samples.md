---
title: "Azure 事件中心示例 | Microsoft 文档"
description: "Azure 事件中心示例"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 0af3f6bc6e074fae4d830f163419d6437d04e2df
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2017
---
# <a name="event-hubs-samples"></a>事件中心示例 

Azure 事件中心集示例集演示了 [Azure 事件中心](/azure/event-hubs/)内的主要功能。 本文分类并介绍了可用的示例，每个示例均具有链接。

在撰写本文时，事件中心示例位于多个不同的位置：

- [MSDN 开发人员代码示例](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

有关 .NET Framework 的不同版本的详细信息，请参阅[框架和目标](/dotnet/articles/standard/frameworks)。

将随着时间的推移添加更多示例，因此请经常返回查看是否有更新。

## <a name="net-standard"></a>.NET Standard

下面的示例演示如何使用 [.NET Standard 库](/dotnet/articles/standard/library)的[事件中心客户端](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md)发送和接收事件。

### <a name="send-events"></a>发送事件 

[发送入门](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender)示例演示如何编写将事件发送到事件中心的 .NET Core 控制台应用程序。

### <a name="receive-events"></a>接收事件 

[使用事件处理器主机接收入门](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver)示例是一个使用事件处理器主机从事件中心接收消息的 .NET Core 控制台应用程序。

## <a name="net-framework"></a>.NET framework   

这些示例针对 [.NET Framework 库](/dotnet/framework/index)演示 Azure 事件中心的各种其他功能。
 
### <a name="notify-users-of-events-received"></a>通知用户已收到的事件

[AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) 示例通知用户已从传感器或其他系统收到的数据。

### <a name="get-started-with-event-hubs"></a>事件中心入门 

[事件中心入门](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097)示例演示事件中心的基本功能，例如如何创建事件中心、将事件发送到事件中心，以及使用[事件处理器主机](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)处理事件。

### <a name="scale-out-event-processing"></a>扩大事件处理 

[扩大事件处理](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3)示例演示如何使用[事件处理器主机](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)分配事件中心流消耗工作负荷。 它演示如何实现用于管理事件流的 **EventProcessor** 和 **EventProcessorFactory** 对象。 

###  <a name="pull-data-from-sql-into-an-event-hub"></a>将数据从 SQL 提取到事件中心

[提取 SQL 数据](https://github.com/Azure-Samples/event-hubs-dotnet-import-from-sql)示例演示如何从 SQL 表中提取数据并将其推送到事件中心，以用作下游分析应用程序的输入。

### <a name="pull-web-data-into-an-event-hub"></a>将 Web 数据提取到事件中心 

[从 Web 导入数据](https://github.com/Azure-Samples/event-hubs-dotnet-importfromweb)示例演示如何从公共源（例如运输部的流量信息源）提取数据，并将其推送到事件中心。

## <a name="next-steps"></a>后续步骤

访问以下链接了解有关 .NET Framework 版本的详细信息：

- [框架和目标](/dotnet/articles/standard/frameworks)
- [.NET Framework 4.6 和 4.5](/dotnet/framework/index)

参阅以下文章可以了解有关事件中心的详细信息：

- [事件中心概述](event-hubs-what-is-event-hubs.md)
- [创建事件中心](event-hubs-create.md)
- [事件中心常见问题](event-hubs-faq.md)