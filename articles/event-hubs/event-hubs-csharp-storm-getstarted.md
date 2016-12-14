---
title: "使用 Apache Storm 通过 C# 使用事件中心入门 | Microsoft Docs"
description: "遵循本教程开始使用 Azure 事件中心，以通过 C# 发送事件，并在 Apache Storm 群集中接收这些事件。"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: f64cb2bd-2e38-4c78-8d0d-9af14c000d9f
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 26d0363d72147f1b72b77652ba9b638bfa260970


---
# <a name="get-started-with-event-hubs"></a>事件中心入门
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>介绍
事件中心是一个具备高度可伸缩性的引入系统，每秒可收入大量事件，从而使应用程序能够处理和分析你连接的设备和应用程序所产生的海量数据。 将数据采集到事件中心后，你可以使用任何实时分析提供程序或存储群集来转换和存储数据。

有关详细信息，请参阅[事件中心概述]。

在本教程中，你将学习如何使用用 C# 编写的控制台应用程序将消息引入事件中心，以及如何使用 Apache Storm 并行检索这些消息。

为了完成本教程，你将需要以下内容：

* [Microsoft Visual Studio](http://visualstudio.com)
* 一个 Java 开发环境，配置为运行 [Maven](http://maven.apache.org/)。 对于本教程，我们将采用 [Eclipse](https://www.eclipse.org/)
* 有效的 Azure 帐户。 <br/>如果没有帐户，只需花费几分钟就能创建一个免费帐户。 有关详细信息，请参阅 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 免费试用</a>。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>运行应用程序
现在，你已准备就绪，可以运行应用程序了。

1. 从 Eclipse 中运行 **LogTopology** 类，然后等待它为所有分区启动接收方。
2. 运行 **Sender** 项目，在控制台窗口中按 **Enter**，然后会看到事件出现在接收方窗口中。
   
   ![][22]

## <a name="next-steps"></a>后续步骤
现在，你已生成了一个可以创建事件中心以及发送和接收数据的有效应用程序，接下来请继续学习以下方案：

* 完整的[使用事件中心的完整示例应用程序][使用事件中心的完整示例应用程序]。
* [使用事件中心扩大事件处理][使用事件中心扩大事件处理]示例。

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure 经典门户]: https://manage.windowsazure.com/
[事件中心概述]: event-hubs-overview.md
[使用事件中心的完整示例应用程序]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[使用事件中心扩大事件处理]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Nov16_HO3-->


