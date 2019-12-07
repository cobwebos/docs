---
title: Azure Monitor 数据平台 |Microsoft Docs
description: Azure Monitor 收集的监视数据划分为指标（轻型数据，能够支持近实时方案）和日志（用于高级分析）。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 4e9779f612bc4a2521459bf76a6e2b399fc89e07
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894124"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor 数据平台

若要跨当今复杂的计算环境启用可观察性，并且运行依赖于云和本地服务的分布式应用程序，需要收集每个层和分布式系统的每个组件的操作数据。 你需要能够深入了解此数据，并将其合并到具有不同透视图的单个窗格中，以支持组织中的众多利益干系人。

[Azure Monitor](../overview.md)收集数据并将数据从各种源聚合到公共数据平台，可用于分析、可视化和警报。 它在来自多个源的数据的基础上提供了一致的体验，这为你提供了跨所有受监视资源的深入见解，甚至还提供了其他服务中的数据，这些数据将数据存储在 Azure Monitor 中。


![Azure Monitor 概述](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Azure Monitor 中的数据可观察性
指标、日志和分布式跟踪通常称为可观察性的三个支柱。 监视工具必须收集和分析这些数据，才能提供充足的监视系统可观察性。 可以通过将来自多个支柱的数据与在被监视的整个资源集中聚合的数据进行关联来实现可观察性。 由于 Azure Monitor 将来自多个源的数据存储在一起，因此可以使用一组常用工具来关联和分析数据。 它还将数据与多个 Azure 订阅和租户相关联，并为其他服务托管数据。

Azure 资源会生成大量的监视数据。 Azure Monitor 将此数据与监视其他数据源中的数据合并到指标或日志平台。 每个针对特定监视方案进行了优化，每个方案都支持 Azure Monitor 中的不同功能。 诸如数据分析、可视化或警报等功能要求您了解这些差异，以便您能够以最高效且经济高效的方式实现所需的方案。 [Application Insights](../app/app-insights-overview.md)或[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)等 Azure Monitor 中的见解具有分析工具，使你能够专注于特定的监视方案，而无需了解两种数据类型之间的差异。 


### <a name="metrics"></a>指标
[指标](data-platform-metrics.md)是数字值，用于描述系统某些方面在特定时间点的情况。 它们按固定间隔收集，并由时间戳、名称、值以及一个或多个定义标签标识。 可以使用多种算法来聚合指标，与其他指标相比，分析了一段时间内的趋势。 

Azure Monitor 中的指标存储在一个时间系列数据库中，该数据库针对分析时间戳的数据进行了优化。 这使指标特别适用于警报和快速检测问题。 用户可以告诉您系统的执行情况，但通常需要与日志相结合来确定问题的根本原因。

使用[指标资源管理器](../app/metrics-explorer.md)的 Azure 门户中的指标可用于进行交互式分析。 可以将其添加到[Azure 仪表板](../learn/tutorial-app-dashboards.md)，以便与其他数据组合可视化，并用于近乎实时的[警报](alerts-metric.md)。

有关详细信息，请参阅 Azure Monitor 中的[指标](data-platform-metrics.md)Azure Monitor 指标。

### <a name="logs"></a>日志
[日志](data-platform-logs.md)是在系统内发生的事件。 它们可以包含不同类型的数据，并且可以是具有时间戳的结构化或自由格式文本。 它们可能在环境中生成日志条目的事件时偶尔创建，而负载较重的系统通常会生成更多的日志卷。

Azure Monitor 中的日志存储在基于[Azure 数据资源管理器](/azure/data-explorer/)的 Log Analytics 工作区中，后者提供强大的分析引擎和[丰富的查询语言](/azure/kusto/query/)。 日志通常提供足够的信息来提供所识别问题的完整上下文，并且对于识别问题的根本情况非常有用。

> [!NOTE]
> 在 Azure 中区分日志数据 Azure Monitor 日志和源，这一点很重要。 例如，Azure 中的订阅级别事件将写入到可从 "Azure Monitor" 菜单中查看的[活动日志](activity-logs-overview.md)。 大多数资源会将操作信息写入到可转发到不同位置的[资源日志](resource-logs-overview.md)。 Azure Monitor 日志是一种日志数据平台，用于收集活动日志和资源日志以及其他监视数据，以跨整个资源集提供深入分析。


 你可以使用 Azure 门户中的[Log Analytics](../log-query/portals.md)以交互方式使用[日志查询](../log-query/log-query-overview.md)，也可以将结果添加到[Azure 仪表板](../learn/tutorial-app-dashboards.md)，以便与其他数据进行可视化。 你还可以创建[日志警报](alerts-log.md)，这将根据计划查询的结果触发警报。

阅读有关 Azure Monitor 日志的详细信息，包括[Azure Monitor 中的日志中](data-platform-logs.md)的数据源。

### <a name="distributed-traces"></a>分布式跟踪
跟踪是按照用户请求通过分布式系统执行的一系列相关事件。 它们可用于确定应用程序代码的行为和不同事务的性能。 尽管日志通常由分布式系统的单个组件创建，但跟踪会度量整个组件集内的应用程序的操作和性能。

Azure Monitor 中的分布式跟踪与[APPLICATION INSIGHTS SDK](../app/distributed-tracing.md)一起启用，跟踪数据与 Application Insights 收集的其他应用程序日志数据一起存储。 这样，就可以将其用于与其他日志数据相同的分析工具，包括日志查询、仪表板和警报。

阅读有关分布式跟踪的详细[信息。](../app/distributed-tracing.md)


## <a name="compare-azure-monitor-metrics-and-logs"></a>比较 Azure Monitor 指标和日志

下表比较了 Azure Monitor 中的指标和日志。

| 属性  | 指标 | 日志 |
|:---|:---|:---|
| 优势 | 轻型和可近乎实时的情况，如警报。 非常适合用于快速检测问题。 | 通过丰富的查询语言进行分析。 适用于深层分析和确定根本原因。 |
| 数据 | 仅数值 | 文本或数字数据 |
| 结构 | 一组标准的属性，包括采样时间、所监视的资源、一个数值。 某些指标包含多个维度以便进一步定义。 | 根据日志类型的唯一属性集。 |
| 集合 | 按固定间隔收集。 | 可能会因为事件触发要创建的记录而偶尔收集。 |
| 在 Azure 门户中查看 | 指标资源管理器 | Log Analytics |
| 数据源包括 | 从 Azure 资源收集的平台指标。<br>Application Insights 监视的应用程序。<br>由应用程序或 API 定义的自定义。 | 应用程序和资源日志。<br>监视解决方案。<br>代理和 VM 扩展。<br>应用程序请求和异常。<br>Azure 安全中心。<br>数据收集器 API。 |

## <a name="collect-monitoring-data"></a>收集监视数据
Azure Monitor 的不同[数据源](data-sources.md)会写入 Log Analytics 工作区（日志）或 Azure Monitor 指标数据库（指标）。 某些源将直接写入这些数据存储，而其他源可能会写入到其他位置（如 Azure 存储），并需要一些配置来填充日志或指标。 

有关填充每个类型的不同数据源的列表，请参阅 Azure Monitor 中的[度量值](data-platform-metrics.md)和[日志 Azure Monitor](data-platform-logs.md) 。


## <a name="stream-data-to-external-systems"></a>将数据流式传输到外部系统
除了使用 Azure 中的工具分析监视数据以外，可能还需要将这些数据转发到外部工具，例如某个安全信息和事件管理 (SIEM) 产品。 通常，这种转发是通过 [Azure 事件中心](/azure/event-hubs/)直接从受监视资源完成的。 一些源可配置为将数据直接发送到事件中心，同时可以使用其他进程（如逻辑应用）来检索所需数据。 有关详细信息，请参阅[通过外部工具将 Azure 监视数据流式传输到事件中心](stream-monitoring-data-event-hubs.md)。



## <a name="next-steps"></a>后续步骤

- 有关[指标的](data-platform-metrics.md)详细信息，请参阅 Azure Monitor。
- 详细了解[Azure Monitor 中的日志](data-platform-logs.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。
