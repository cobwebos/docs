---
title: 如何在 Azure 时序见解中监视和降低限制 | Microsoft Docs
description: 本文介绍如何监视、诊断并减少导致 Azure 时序见解延迟和限制的性能问题。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 389e1472e1e1fcbed6dd3b6c1d155199246d877f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332975"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>监视并缩减限制，以减少 Azure 时序见解中的延迟

传入数据量超过环境配置时，Azure 时序见解中可能会出现延迟或限制。

可以根据要分析的数据量，适当地配置环境，从而避免延迟和限制。

以下情况最有可能出现延迟和限制：

- 添加包含超出所分配入口速率（时序见解需要追赶）的旧数据的事件源。
- 将较多事件源添加到一个环境中，导致其他事件出现激增（可能超过环境容量）。
- 将大量历史事件推送到一个事件源，导致延迟（时序见解需要追赶）。
- 将引用数据和遥测结合，导致事件大小较大。  从限制的角度看，数据包大小为 32 KB 的入口数据包视为 32 个事件，每个事件大小为 1 KB。 允许的最大事件大小为 32 KB；大于 32 KB 的数据包会被截断。

## <a name="video"></a>视频

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>了解时序见解数据引入行为，以及如何对其进行规划。</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>使用警报监视延迟和限制

警报有助于诊断并缓解环境导致的延迟问题。

1. 在 Azure 门户中，选择 "**警报**"。

   [![Alerts](media/environment-mitigate-latency/add-alerts.png)](media/environment-mitigate-latency/add-alerts.png#lightbox)

1. 随后将显示 "**创建规则**" 面板。 在 "**条件**" 下选择 "**添加**"。

   [![Add 警报](media/environment-mitigate-latency/alert-pane.png)](media/environment-mitigate-latency/alert-pane.png#lightbox)

1. 接下来，配置信号逻辑的确切条件。

   [@no__t 1Configure 信号逻辑](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   在这里，你可以使用以下某些条件来配置警报：

   |指标  |描述  |
   |---------|---------|
   |入口收到的字节数     | 从事件源读取的原始字节数。 原始计数通常包括属性名称和值。  |  
   |入口收到的无效消息数     | 从所有 Azure 事件中心或 Azure IoT 中心事件源读取的无效消息的计数。      |
   |入口收到的消息数   | 从所有事件中心或 IoT 中心事件源读取的消息的计数。        |
   |入口存储的字节数     | 已存储且可用于查询的事件的总大小。 仅根据属性值计算大小。        |
   |**入口存储的事件数**     |   已存储并可供查询的平展事件计数。      |
   |**入口收到消息时间延迟**    |  消息在事件源中排队的时间与消息在入口中处理之间的时间差（以秒为单位）。      |
   |**入口收到消息计数延迟**    |  上次排队的消息在事件源分区中的序列号与在入口中进行处理的消息的序列号之间的差异。      |

   选择“完成”。

1. 配置所需的信号逻辑后，请查看所选的警报规则。

   [![Ingress](media/environment-mitigate-latency/ingress.png)](media/environment-mitigate-latency/ingress.png#lightbox)

## <a name="throttling-and-ingress-management"></a>限制和入口管理

* 如果受到限制，您将看到 "*入口接收的消息时间延迟*" 值，通知您时序见解环境后的秒数从消息到达事件源的实际时间（不包括索引时间）appx。 30-60 秒）。  

  入口收到消息计数延迟也应该有一个值，用于确定你在消息数方面落后多少。  若要赶上来，最容易的方式是增加环境的容量，使之达到能够克服此差异的规模。  

  例如，如果您看到 S1 环境显示了5000000消息的滞后时间，则可能会将您的环境的大小增加到六个单位，大约一整天就会开始。  甚至可以增加更多，这样追赶速度会更快。 在一开始预配某个环境时，尤其是在将其连接到某个事件源，而该事件源中已经有事件时，或者在批量上传大量历史数据时，追赶期是常见的现象。

* 另一种方法是将“入口已存储事件”警报设置为在 2 小时的时间内 >= 略低于总环境容量的阈值。  此警报有助于了解是否持续达到容量要求，指示很可能存在延迟。 

  例如，如果预配了三个 S1 单位（或每分钟入口容量为 2100 个事件），则可以将“入口存储的事件数”警报设置为 2 小时 >= 1900 个事件。 如果因不断超过该阈值而触发警报，很可能是由于预配不足。  

* 如果怀疑正在受到限制，可将入**站收到的消息**与事件源的出口消息进行比较。  如果传入事件中心的消息数大于“入口收到的消息数”，时序见解很可能受到了限制。

## <a name="improving-performance"></a>改善性能

要减少限制和延迟，最佳的更正方法是增加环境容量。

可以根据要分析的数据量，适当地配置环境，从而避免延迟和限制。 有关如何向环境增加容量的更多信息，请参阅[缩放环境](time-series-insights-how-to-scale-your-environment.md)。

## <a name="next-steps"></a>后续步骤

- 阅读有关[在时序见解环境中诊断并解决问题的](time-series-insights-diagnose-and-solve-problems.md)信息。

- 了解[如何扩展时序见解环境](time-series-insights-how-to-scale-your-environment.md)。