---
title: "通过 C# 使用事件中心入门 | Microsoft Docs"
description: "遵循本教程开始使用以 C# 编写的 Azure 事件中心和事件处理程序主机。"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 2ec2378a-34f7-44c3-b976-cc444c98c338
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/07/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 015dbab81f807cd05d1fedb97f5fc62a012f2084
ms.openlocfilehash: 54aaf68864fc83501b860ecc3daef1c0216c0140


---
# <a name="get-started-with-event-hubs"></a>事件中心入门
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>介绍
事件中心是一个服务，可用于处理来自连接设备和应用程序的大量事件数据（遥测）。 将数据采集到事件中心后，可以使用任何实时分析提供程序或存储群集来转换和存储数据。 这种大规模事件收集和处理功能是现代应用程序体系结构（包括物联网 (IoT)）的重要组件。

本教程说明如何使用 Azure 门户创建事件中心。 此外，还将说明如何使用以 C# 编写的控制台应用程序将消息收集到事件中心，以及如何使用 C# [事件处理程序主机][Event Processor Host]库并行检索这些消息。

若要完成本教程，你需要以下各项：

* [Microsoft Visual Studio](http://visualstudio.com)
* 有效的 Azure 帐户。 如果没有帐户，只需几分钟的时间就能创建一个免费帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/free/)。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>运行应用程序
现在，你已准备就绪，可以运行应用程序了。

1. 从 Visual Studio 中，打开先前创建的 **Receiver** 项目。
2. 右键单击“Receiver”解决方案，单击“添加”，然后单击“现有项目”。
3. 找到现有的 Sender.csproj 文件，然后双击该文件以将其添加到解决方案中。
4. 再次右键单击“Receiver”解决方案，然后单击“属性”。 这将显示“Receiver”  属性页。
5. 单击“启动项目”，然后单击“多个启动项目”按钮。 将“Receiver”和“Sender”项目的“操作”框设置为“启动”。
   
    ![][19]
6. 单击“项目依赖项” 。 在“项目”框中，单击“Sender”。 在“依赖于”框中，确保选中“Receiver”。
   
    ![][20]
7. 单击“确定”，关闭“属性”对话框。
8. 从 Visual Studio 中，按 F5 运行“Receiver”  项目，然后等待它为所有分区启动接收方。
   
   ![][21]
9. “Sender”项目将自动运行。 在控制台窗口中按 **Enter**，便会看到事件出现在接收方窗口中。
   
   ![][22]

在“Sender”窗口中按“Ctrl+C”结束 Sender 应用程序，然后在“Receiver”窗口中按 **Enter** 关闭该应用程序。

## <a name="next-steps"></a>后续步骤
现在，你已生成了一个可以创建事件中心以及发送和接收数据的有效应用程序，接下来请继续学习以下方案：

* [使用事件中心的完整示例应用程序][sample application that uses Event Hubs]。
* [事件处理程序主机](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [使用事件中心扩大事件处理][Scale out Event Processing with Event Hubs]示例。
* [事件中心概述][Event Hubs overview]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md




<!--HONumber=Dec16_HO2-->


