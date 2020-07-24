---
title: 如何监视和降低限制 - Azure 时序见解 | Microsoft Docs
description: 了解如何监视、诊断并减少在 Azure 时序见解中导致延迟和限制的性能问题。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: 9fa47c81aede9de5d083f16f9e1705f687ad39a4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046431"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights-gen1"></a>监视和缓解限制以减少 Azure 时序见解中的延迟 Gen1

传入数据量超过环境配置时，Azure 时序见解中可能会出现延迟或限制。

可以根据要分析的数据量，适当地配置环境，从而避免延迟和限制。

以下情况最有可能出现延迟和限制：

- 添加一个包含旧数据的事件源，这些数据可能会超出分配的入口速率（Azure 时序见解将需要更新）。
- 将较多事件源添加到一个环境中，导致其他事件出现激增（可能超过环境容量）。
- 将大量历史事件推送到事件源，导致延迟（Azure 时序见解将需要更新）。
- 将引用数据和遥测结合，导致事件大小较大。 允许的最大数据包大小为 32 KB；大于 32 KB 的数据包会被截断。

## <a name="video"></a>视频

### <a name="learn-about-azure-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>了解 Azure 时序见解数据引入行为，以及如何对其进行规划。</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>使用警报监视延迟和限制

警报有助于诊断并缓解环境中出现的延迟问题。

1. 在 Azure 门户中，选择你的 Azure 时序见解环境。 然后选择“警报”  。

   [![向 Azure 时序见解环境添加警报](media/environment-mitigate-latency/mitigate-latency-add-alert.png)](media/environment-mitigate-latency/mitigate-latency-add-alert.png#lightbox)

1. 选择“+ 新建警报规则”。  然后将显示“创建规则”  面板。 在“条件”  下选择“添加”  。

   [![添加警报窗格](media/environment-mitigate-latency/mitigate-latency-add-pane.png)](media/environment-mitigate-latency/mitigate-latency-add-pane.png#lightbox)

1. 接下来，配置信号逻辑的确切条件。

   [![配置信号逻辑](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   在此处，可以使用以下一些条件配置警报：

   |指标  |说明  |
   |---------|---------|
   |入口收到的字节数      | 从事件源读取的原始字节数。 原始计数通常包括属性名称和值。  |  
   |入口收到的无效消息数      | 从所有 Azure 事件中心或 Azure IoT 中心事件源读取的无效消息的计数。      |
   |入口收到的消息数    | 从所有事件中心或 IoT 中心事件源读取的消息的计数。        |
   |入口存储的字节数      | 已存储且可用于查询的事件的总大小。 仅根据属性值计算大小。        |
   |**入口存储的事件数**     |   已存储并可供查询的平展事件计数。      |
   |**入口收到消息时间延迟**    |  消息在事件源中排队的时间与消息在入口中处理之间的时间差（以秒为单位）。      |
   |**入口收到消息计数延迟**    |  上次排队的消息在事件源分区中的序列号与在入口中进行处理的消息的序列号之间的差异。      |

   选择“完成”  。

1. 配置所需的信号逻辑后，直观地查看所选的警报规则。

   [![延迟视图和图表](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png)](media/environment-mitigate-latency/mitigate-latency-view-and-charting.png#lightbox)

## <a name="throttling-and-ingress-management"></a>限制和入口管理

* 如果受到限制，将注册*入口接收的消息时间滞后*值，通知你 Azure 时序见解环境后的秒数从消息到达事件源的实际时间（不包括 appx 的索引时间）。 30-60 秒）。  

   入口收到消息计数延迟也应该有一个值，用于确定你在消息数方面落后多少。  若要赶上来，最容易的方式是增加环境的容量，使之达到能够克服此差异的规模。  

  例如，如果 S1 环境显示有 5,000,000 条消息的延迟，那么你可以将环境的大小增加到 6 个单元，以便在大约一天的时间内赶上进度。  甚至可以增加更多，这样追赶速度会更快。 在一开始预配某个环境时，尤其是在将其连接到某个事件源，而该事件源中已经有事件时，或者在批量上传大量历史数据时，追赶期是常见的现象。

* 另一种方法是将“入口已存储事件”警报设置为在 2 小时的时间内 >= 略低于总环境容量的阈值  。  此警报有助于了解是否持续达到容量要求，指示很可能存在延迟。 

  例如，如果预配了三个 S1 单位（或每分钟入口容量为 2100 个事件），则可以将“入口存储的事件数”警报设置为 2 小时 >= 1900 个事件  。 如果因不断超过该阈值而触发警报，很可能是由于预配不足。  

* 如果怀疑受到限制，可以将“入口收到的消息数”和事件源的出口消息数相比较  。  如果进入事件中心的时间大于你的**入口接收的消息**，则可能会限制 Azure 时序见解。

## <a name="improving-performance"></a>改善性能

要减少限制和延迟，最佳的更正方法是增加环境容量。

可以根据要分析的数据量，适当地配置环境，从而避免延迟和限制。 有关如何为环境添加容量的更多信息，请阅读[缩放环境](time-series-insights-how-to-scale-your-environment.md)。

## <a name="next-steps"></a>后续步骤

- 阅读有关[在 Azure 时序见解环境中诊断并解决问题的](time-series-insights-diagnose-and-solve-problems.md)信息。

- 了解[如何缩放 Azure 时序见解环境](time-series-insights-how-to-scale-your-environment.md)。