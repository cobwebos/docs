---
title: "使用 Azure 事件中心通过 Java 发送事件，通过 C# 接收 | Microsoft Docs"
description: "遵循本教程开始使用 Azure 事件中心，以通过 Java 发送事件，并使用 EventProcessorHost 通过 C# 接收事件。"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 1c9988ca-1473-4287-a033-f99122c429bc
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: 2e16874e20b233b93370ec81f8e534475c7c562a


---
# <a name="get-started-with-event-hubs"></a>事件中心入门
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>介绍
事件中心是一个具备高度可伸缩性的引入系统，每秒可收入大量事件，从而使应用程序能够处理和分析你连接的设备和应用程序所产生的海量数据。 将数据采集到事件中心后，你可以使用任何实时分析提供程序或存储群集来转换和存储数据。

有关详细信息，请参阅[事件中心概述][Event Hubs overview]。

本教程介绍如何使用以 Java 编写的控制台应用程序将消息引入事件中心，以及如何使用 C# [事件处理程序主机][Event Processor Host]库并行检索这些消息。

若要完成本教程，你需要以下各项：

* Java 开发环境。 对于本教程，我们将采用 [Eclipse](https://www.eclipse.org/)。
* [Microsoft Visual Studio](http://visualstudio.com)
* 有效的 Azure 帐户。 <br/>如果没有帐户，只需花费几分钟就能创建一个免费帐户。 有关详细信息，请参阅 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免费试用</a>。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>运行应用程序
现在，你已准备就绪，可以运行应用程序了。

1. 从 Visual Studio 中运行 **Receiver** 项目，然后等待它为所有分区启动接收方。
   
   ![][21]
2. 运行 **Sender** 项目。
   
   ![][22]

## <a name="next-steps"></a>后续步骤
现在，你已生成了一个可以创建事件中心以及发送和接收数据的有效应用程序，接下来请继续学习以下方案：

* [使用事件中心的完整示例应用程序][sample application that uses Event Hubs]。
* [使用事件中心扩大事件处理][Scale out Event Processing with Event Hubs]示例。

有关详细信息，请参阅 [Java 开发人员中心](/develop/java/)。

<!-- Images. -->
[21]: ./media/event-hubs-java-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-java-ephcs-getstarted/java-send.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Jan17_HO4-->


