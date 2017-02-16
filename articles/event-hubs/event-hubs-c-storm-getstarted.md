---
title: "通过 C 和 Apache Storm 使用事件中心入门 | Microsoft Docs"
description: "遵循本教程开始使用 Azure 事件中心，以通过 C 发送事件，并在 Apache Storm 群集中接收这些事件。"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 3b15825e-c53c-471b-870c-686bff46885a
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: java
ms.topic: article
ms.date: 11/30/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 33a960ae39ab8fa89126201fd67f13e5536413ef
ms.openlocfilehash: 7bad4df26d10ba8edae311b41d0c07be2db374f4


---
# <a name="get-started-with-event-hubs"></a>事件中心入门
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>介绍
事件中心是一个具备高度可伸缩性的引入系统，每秒可收入大量事件，从而使应用程序能够处理和分析你连接的设备和应用程序所产生的海量数据。 将数据采集到事件中心后，你可以使用任何实时分析提供程序或存储群集来转换和存储数据。

有关详细信息，请参阅[事件中心概述]。

在本教程中，你将学习如何使用用 C 编写的控制台应用程序将消息引入事件中心，以及如何使用 Apache Storm 并行检索这些消息。

若要完成本教程，需要满足以下条件：

* C 语言开发环境。 对于本教程，我们假设 gcc 堆栈位于使用 Ubuntu 14.04 的 [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 上。 有关其他环境的说明，将在外部链接中提供。
* 一个 Java 开发环境，配置为运行 [Maven](http://maven.apache.org/)。 对于本教程，我们将采用 [Eclipse](https://www.eclipse.org/)。
* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>运行应用程序
现在，你已准备就绪，可以运行应用程序了。

1. 从 Eclipse 中运行 **LogTopology** 类，然后等待它为所有分区启动接收方。
2. 运行 **sender** 程序，然后就会看到事件出现在接收方窗口中。
   
   ![][23]

> [!NOTE]
> 在本教程中，只出于开发目的以本地模式使用 Storm。 请参阅 [HDInsight Storm 概述]和官方 [Apache Storm] 文档，了解 Storm 部署和模式的详细信息。

## <a name="next-steps"></a>后续步骤
以下资源可用于开发集成事件中心和 Storm 的应用程序。

* [使用 Storm 和 HDInsight 分析传感器数据][Analyzing sensor data with Storm and HDInsight]是一个完整方案教程，它用到了事件中心、Storm 和 HBase，以在 Hadoop 群集中引入传感器数据。
* [使用 SCP.NET 和 C# 在 Storm 和 HDInsight 上开发流式数据处理应用程序][Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight]是有关如何使用 C# 编写 Storm 管道的教程。

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[事件中心概述]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm 概述]: ../hdinsight/hdinsight-storm-overview.md
[Analyzing sensor data with Storm and HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Dec16_HO1-->


