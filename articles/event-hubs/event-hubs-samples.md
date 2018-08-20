---
title: Azure 事件中心示例 | Microsoft 文档
description: Azure 事件中心示例
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2018
ms.author: shvija
ms.openlocfilehash: fbde6e5a5ed053d6c151b3af25535c397a496ef4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005328"
---
# <a name="event-hubs-samples"></a>事件中心示例 
可以在 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples) 上找到事件中心示例。 这些示例演示了 [Azure 事件中心](/azure/event-hubs/)的主要功能。 本文分类并介绍了可用的示例，每个示例均具有链接。

## <a name="net-samples"></a>.NET 示例

| 示例名称 | Description | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | 此示例演示如何编写将一组事件发送到事件中心的 .NET Core 控制台应用程序。 |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | 此示例介绍如何编写 .NET Core 控制台应用程序，以使用事件处理程序主机库从事件中心接收事件。  | 

## <a name="java-samples"></a>Java 示例

| 示例名称 | Description | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | 此示例说明了如何将批量事件引入到事件中心。 | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | 此示例说明了如何将事件引入到事件中心。 |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | 此示例说明了可用于事件中心以引入事件的选项。 |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | 此示例说明了如何使用特定的日期时间偏移量从事件中心分区接收事件。 |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | 此示例说明了如何使用特定的数据偏移量从事件中心分区接收事件。 |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | 此示例说明了如何使用序列号从事件中心分区进行接收。 |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |此示例说明了如何使用事件处理程序主机从事件中心接收事件，其中该主机提供跨多个并发接收器的自动分区选择和故障转移功能。 | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | 此示例说明了事件中心如何自动在高负载中纵向扩展。 该示例将以刚好超过事件中心配置速率的速率发送事件，使导致事件中心纵向扩展。 | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | 此示例允许测量入口速率。 | 

## <a name="next-steps"></a>后续步骤
参阅以下文章可以了解有关事件中心的详细信息：

- [事件中心概述](event-hubs-what-is-event-hubs.md)
- [事件中心功能](event-hubs-features.md)
- [事件中心常见问题解答](event-hubs-faq.md)