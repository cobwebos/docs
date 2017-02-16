---
title: "通过 C 和 C# 使用事件中心入门 | Microsoft Docs"
description: "遵循本教程开始使用 Azure 事件中心，以通过 C 发送事件，并使用 EventProcessorHost 通过 C# 接收事件。"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5170c138-39ec-4eea-9925-e6902e5c425a
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 11/30/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: fd7450985906dcf707a420d51b3a3f3b13786741
ms.openlocfilehash: 742bea46f7c4eba826523a49af4972dbb6db439b


---
# <a name="get-started-with-event-hubs"></a>事件中心入门
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>介绍
事件中心是一个具备高度可伸缩性的引入系统，每秒可收入大量事件，从而使应用程序能够处理和分析你连接的设备和应用程序所产生的海量数据。 将数据采集到事件中心后，你可以使用任何实时分析提供程序或存储群集来转换和存储数据。

有关详细信息，请参阅[事件中心概述][Event Hubs overview]。

在本教程中，你将学习如何使用用 C 编写的控制台应用程序将消息引入事件中心，以及如何使用 C# [事件处理程序主机][Event Processor Host]库并行检索这些消息。

若要完成本教程，需要满足以下条件：

* C 语言开发环境。 对于本教程，我们假设 gcc 堆栈位于使用 Ubuntu 14.04 的 [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 上。 有关其他环境的说明，将在外部链接中提供。
* Microsoft Visual Studio Express for Windows
* 有效的 Azure 帐户。 如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>运行应用程序
现在，你已准备就绪，可以运行应用程序了。

1. 从 Visual Studio 中，运行 **Receiver** 项目，然后等待它为所有分区启动接收方。
   
   ![][21]
2. 运行 **Sender** 程序，然后就会看到事件出现在接收方窗口中。
   
   ![][24]

## <a name="next-steps"></a>后续步骤
现在，你已生成了一个可以创建事件中心以及发送和接收数据的有效应用程序，接下来请继续学习以下方案：

* [使用事件中心的完整示例应用程序][sample application that uses Event Hubs]。
* [使用事件中心扩大事件处理][Scale out Event Processing with Event Hubs]示例。
* [事件中心概述][Event Hubs overview]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO1-->


