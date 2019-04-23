---
title: Azure Monitor 数据平台 |Microsoft Docs
description: Azure Monitor 收集的监视数据划分为指标（轻型数据，能够支持近实时方案）和日志（用于高级分析）。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8883c1e7f2874e1e2e61b8eca122f2ec294c7849
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790920"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor 数据平台

启用跨今天的复杂计算环境运行依赖于云端和本地服务的分布式应用程序的可观察性需要集合中每一层和分布式系统的每个组件的操作数据。 您需要能够对此数据执行深入的见解并将它合并到不同的角度可支持多个利益干系人的组织中的玻璃效果的单个窗格。

[Azure 监视器](../overview.md)收集和聚合数据从各种源到常用数据平台，它可用于分析、 可视化和警报。 它提供了基于来自多个源的数据，这将使您深入了解跨所有被监视资源和甚至数据从其数据存储在 Azure Monitor 中的其他服务提供一致的体验。


![Azure Monitor 概述](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Azure Monitor 中的可观察性数据
指标、 日志和分布式的跟踪是通常称为可观察性的三个重要组成部分。 这些是系统的不同类型的数据必须进行收集和分析，提供足够的可观察性的受监视监视工具。 可观察性可以通过跨整个受监视的资源集聚合数据，然后将数据从多个支柱相关联。 Azure Monitor 将来自多个源组合在一起的数据存储，因为数据可以关联和分析使用一组常用的工具。 它还将数据跨多个 Azure 订阅和租户，除了托管的其他服务的数据关联起来。

Azure 资源生成大量的监视数据。 Azure 监视器将此数据以及监视到来自其他源的数据合并指标或日志的平台。 每个适用于特定监视方案，每个 Azure Monitor 中支持不同的功能。 数据分析、 可视化效果，或发出警报，需要了解的差异，以便可以以最高效且经济高效的方式实现你要求的方案。 例如 Azure Monitor 中的见解[Application Insights](../app/app-insights-overview.md)或[适用于 Vm 的 Azure Monitor](../insights/vminsights-overview.md)具有允许您以集中于特定的监视方案，而无需了解的分析工具两种类型的数据之间的差异。 


### <a name="metrics"></a>度量值
[指标](data-platform-metrics.md)是数字值，用于描述系统某些方面在特定时间点的情况。 它们是按固定间隔收集和使用时间戳、 名称、 值和一个或多个定义标签标识。 可以使用各种算法，相比其他指标，并随着时间的推移进行分析趋势的聚合指标。 

Azure Monitor 中的指标存储在时间序列数据库针对分析加盖时间戳数据进行了优化。 这样，指标特别适用于警报和快速检测到的问题。 它们可以告诉您如何执行你的系统，但通常需要结合日志以确定问题的根本原因。

指标是可用于在 Azure 门户中使用交互式分析[指标资源管理器](../app/metrics-explorer.md)。 可以将它们添加到[Azure 仪表板](../learn/tutorial-app-dashboards.md)可视化效果中与其他数据结合，用于近实时[警报](alerts-metric.md)。

详细了解 Azure Monitor 指标，包括其数据源中的数据源[Azure Monitor 中的指标](data-platform-metrics.md)。

### <a name="logs"></a>日志
[日志](data-platform-logs.md)是在系统内发生的事件。 它们可以包含不同类型的数据和可能结构化或自由格式文本时间戳。 它们可能会根据在环境中的事件日志项，生成和负载较重的系统通常应生成详细的日志卷个别情况下创建。

Azure Monitor 中的日志存储在基于 Log Analytics 工作区[Azure 数据资源管理器](/azure/data-explorer/)提供功能强大的分析引擎和[丰富的查询语言](/azure/kusto/query/)。 日志通常提供足够的信息来提供完整上下文所识别的问题，很有价值的标识的问题的根本原因。

> [!NOTE]
> 请务必区分 Azure Monitor 日志和 Azure 中的日志数据源。 例如，在 Azure 中的订阅级别事件将写入[活动日志](activity-logs-overview.md)，您可以从 Azure Monitor 菜单。 最多的资源会将操作信息写入[诊断日志](diagnostic-logs-overview.md)可转发到不同位置。 Azure Monitor 日志是一个将活动日志和诊断日志以及其他监视数据，以在你的整个资源集之间提供深入分析收集的日志数据平台。


 您可以使用[记录查询](../log-query/log-query-overview.md)使用以交互方式[Log Analytics](../log-query/portals.md)在 Azure 门户中或添加到结果[Azure 仪表板](../learn/tutorial-app-dashboards.md)与结合使用的可视化效果其他数据。 此外可以创建[日志警报](alerts-log.md)这将触发警报的基础计划查询的结果。

详细了解 Azure Monitor 日志包括其数据源中的数据源[Azure Monitor 中的日志](data-platform-logs.md)。

### <a name="distributed-traces"></a>分布式的跟踪
跟踪是系列遵循通过分布式系统的用户请求的相关事件。 它们可以用于确定应用程序代码的行为和不同的事务的性能。 虽然通常将通过一个分布式系统的各个组件创建日志，但跟踪组件的整个集测量的操作和应用程序的性能。

使用启用了 Azure Monitor 中的分布式的跟踪[Application Insights SDK](../app/distributed-tracing.md)，和跟踪数据存储与 Application Insights 收集的其他应用程序日志数据。 这样，可用于与其他日志数据，包括日志查询、 仪表板和警报相同的分析工具。

阅读更多有关分布式跟踪处[什么是分布式跟踪？](../app/distributed-tracing.md)。


## <a name="compare-azure-monitor-metrics-and-logs"></a>比较 Azure Monitor 指标和日志

下表比较了指标和 Azure Monitor 中的日志。

| 属性  | 度量值 | 日志 |
|:---|:---|:---|
| 优点 | 轻量并能够近实时方案，例如向发出警报。 适用于快速检测到的问题。 | 使用丰富的分析查询语言。 非常适合于深入分析和确定根本原因。 |
| 数据 | 仅数字值 | 文本或数值数据 |
| 结构 | 包括示例时间、 受监视的资源、 数字值的属性的一组标准。 某些指标包括有关进一步的定义的多个维度。 | 唯一具体取决于日志类型的属性集。 |
| 集合 | 在固定的时间间隔收集。 | 可能会偶尔收集，因为事件会触发要创建的记录。 |
| 在 Azure 门户中查看 | 指标资源管理器 | Log Analytics |
| 数据源包括 | 从 Azure 资源收集平台度量值。<br>由 Application Insights 监视应用程序。<br>由应用程序或 API 定义的自定义。 | 应用程序和诊断日志。<br>监视解决方案。<br>代理和 VM 扩展。<br>应用程序请求和异常。<br>Azure 安全中心。<br>数据收集器 API。 |

## <a name="collect-monitoring-data"></a>收集监视数据
不同[的 Azure Monitor 数据源](data-sources.md)将写入到 Log Analytics 工作区 （日志） 和/或 Azure Monitor 指标数据库 （指标）。 某些源将直接写入到这些数据存储，而其他人可能将写入到另一个位置，例如 Azure 存储，并且需要一些配置，以填充日志或指标。 

请参阅[Azure Monitor 中的指标](data-platform-metrics.md)并[在 Azure 监视器中日志](data-platform-logs.md)有关填充每种类型的不同数据源的列表。


## <a name="stream-data-to-external-systems"></a>将数据流式传输到外部系统
除了使用 Azure 中的工具分析监视数据以外，可能还需要将这些数据转发到外部工具，例如某个安全信息和事件管理 (SIEM) 产品。 通常，这种转发是通过 [Azure 事件中心](/azure/event-hubs/)直接从受监视资源完成的。 可以配置某些源将数据直接发送到事件中心时可以使用另一个进程，例如，逻辑应用检索所需的数据。 请参阅[Stream Azure 监视到事件中心以便外部工具](stream-monitoring-data-event-hubs.md)有关详细信息。



## <a name="next-steps"></a>后续步骤

- 详细了解[Azure Monitor 中的指标](data-platform-metrics.md)。
- 详细了解[Azure Monitor 中的日志](data-platform-logs.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。
