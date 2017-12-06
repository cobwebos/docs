---
title: "如何在 Azure 时序见解中监视和降低限制 | Microsoft Docs"
description: "本文介绍如何监视、诊断并减少导致 Azure 时序见解延迟和限制的性能问题。"
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 11/27/2017
ms.openlocfilehash: ec16f20723e4a613c953363da6cf6b463de829a9
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>监视并缩减限制，以减少 Azure 时序见解中的延迟
传入数据量超过环境配置时，Azure 时序见解中可能会出现延迟或限制。

可以根据要分析的数据量，适当地配置环境，从而避免延迟和限制。

以下情况最有可能出现延迟和限制：

- 添加包含超出所分配入口速率（时序见解需要追赶）的旧数据的事件源。
- 将较多事件源添加到一个环境中，导致其他事件出现激增（可能超过环境容量）。
- 将大量历史事件推送到一个事件源，导致延迟（时序见解需要追赶）。
- 将引用数据和遥测结合，导致事件大小较大。  从限制的角度看，数据包大小为 32 KB 的入口数据包视为 32 个事件，每个事件大小为 1 KB。 允许的最大事件大小为 32 KB；大于 32 KB 的数据包会被截断。


## <a name="monitor-latency-and-throttling-with-alerts"></a>使用警报监视延迟和限制

警报有助于诊断并缓解环境导致的延迟问题。 

1. 在 Azure 门户中，单击“指标”。 

   ![度量值](media/environment-mitigate-latency/add-metrics.png)

2. 单击“添加指标警报”。  

    ![添加指标警报](media/environment-mitigate-latency/add-metric-alert.png)

在此处，可以使用以下指标配置警报：

|指标  |说明  |
|---------|---------|
|入口收到的字节数     | 从事件源读取的原始字节数。 原始计数通常包括属性名称和值。  |  
|入口收到的无效消息数     | 从所有 Azure 事件中心或 Azure IoT 中心事件源读取的无效消息的计数。      |
|入口收到的消息数   | 从所有事件中心或 IoT 中心事件源读取的消息的计数。        |
|入口存储的字节数     | 已存储且可用于查询的事件的总大小。 仅根据属性值计算大小。        |
|入口存储的事件数     |   已存储并可供查询的平展事件计数。      |

![延迟](media/environment-mitigate-latency/latency.png)

一种方法是将“入口已存储事件“警报设置为在 2 小时的时间内 >= 略低于总环境容量的阈值。  此警报有助于了解是否持续达到容量要求，指示很可能存在延迟。  

例如，如果预配了三个 S1 单位（或每分钟入口容量为 2100 个事件），则可以将“入口存储的事件数”警报设置为 2 小时 >= 1900 个事件。 如果因不断超过该阈值而触发警报，很可能是由于预配不足。  

此外，如果怀疑受到限制，可以将“入口收到的消息数”和事件源的出口消息数相比较。  如果传入事件中心的消息数大于“入口收到的消息数”，时序见解很可能受到了限制。

## <a name="improving-performance"></a>改善性能 
要减少限制和延迟，最佳的更正方法是增加环境容量。 

可以根据要分析的数据量，适当地配置环境，从而避免延迟和限制。 有关如何向环境增加容量的更多信息，请参阅[缩放环境](time-series-insights-how-to-scale-your-environment.md)。

## <a name="next-steps"></a>后续步骤
- 有关其他疑难解答步骤，请参阅[在时序见解环境中诊断并解决问题](time-series-insights-diagnose-and-solve-problems.md)。
- 有关其他帮助，请在 [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights)或 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights) 中展开交流。 也可以联系 [Azure 支持](https://azure.microsoft.com/support/options/)，获取辅助支持选项。
