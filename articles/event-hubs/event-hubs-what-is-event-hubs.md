---
title: "什么是 Azure 事件中心？ | Microsoft Docs"
description: "Azure 事件中心概述和说明"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 4391d750-5bbe-456d-9091-b416127bc754
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/30/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 05ca343cfdfc602759eb3ea30a7186a0bb47bd74
ms.openlocfilehash: eddc3e7c4914936a8a83a042dc0f7d528b91f059


---
# <a name="what-is-azure-event-hubs"></a>什么是 Azure 事件中心？
事件中心是一个高度可缩放的引入服务，每秒可以引入数百万的事件，从而使你能够处理和分析连接设备和应用程序生成的海量数据。 事件中心充当事件管道“前门”，将数据收集到事件中心后，可以使用任何实时分析提供程序或批处理/存储适配器来转换和存储这些数据。 事件中心可将事件流的生成与这些事件的使用分离开来，因此，事件使用者可以根据自己的计划访问事件。 有关详细信息和技术详细信息，请参阅 [事件中心概述](event-hubs-overview.md)。

## <a name="event-hubs-capabilities"></a>事件中心的功能
事件中心是一种事件处理服务，用于提供大规模的事件与遥测处理，并且具有较低的延迟和较高的可靠性。 此服务特别适合用于：

* 应用程序检测
* 用户体验或工作流处理
* 物联网 (IoT) 方案

事件中心的其他一些主要功能包括移动应用中的行为跟踪、从 Web 场中采集流量信息、控制台游戏中的游戏内事件捕获，或者从工业机器或互联汽车中收集遥测数据。

## <a name="next-steps"></a>后续步骤
有关事件中心的详细信息，请参阅以下主题。

* [事件中心概述](event-hubs-overview.md)
* [事件中心编程指南](event-hubs-programming-guide.md)
* [事件中心可用性和支持常见问题](event-hubs-availability-and-support-faq.md)
* 使用[事件中心教程][Event Hubs tutorial]入门
* [使用事件中心的完整示例应用程序][sample application that uses Event Hubs]

[Event Hubs tutorial]: event-hubs-csharp-ephcs-getstarted.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097



<!--HONumber=Dec16_HO1-->


