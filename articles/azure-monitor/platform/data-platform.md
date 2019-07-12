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
ms.openlocfilehash: 00c0fea9d8ca7ee299a9a19473917eba90edd675
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606976"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor 数据平台

在当今运行分布式应用程序（依赖于云和本地服务）的复杂计算环境中启用观测功能，需要从分布式系统的每个层和每个组件收集操作数据。 需要能够对这些数据执行深入的见解分析，并将其整合到可提供不同透视图的单个窗格，这样才能为组织中的众多利益干系人提供支持。

[Azure Monitor](../overview.md) 会将各种源中的数据收集并聚合到一个通用数据平台，在该平台中，可以使用这些数据进行分析、实现可视化和发出警报。 Azure Monitor 基于多个源中的数据提供一致的体验，让你深入洞察所有受监视的资源，甚至是其他服务在 Azure Monitor 中存储的数据。


![Azure Monitor 概述](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Azure Monitor 中的观测数据
指标、日志和分布式跟踪通常称作观测功能的三大支柱。 监视工具必须收集并分析这些不同类型的数据，才能对受监视系统进行充分的观测。 若要实现观测功能，可以关联多个支柱提供的数据，并聚合整个受监视资源集中的数据。 由于 Azure Monitor 将多个源中的数据存储在一起，因此可以使用一组通用的工具来关联和分析这些数据。 Azure Monitor 还可关联多个 Azure 订阅和租户中的数据，并托管其他服务的数据。

Azure 资源会生成大量的监视数据。 Azure Monitor 将这些数据连同其他源中的监视数据一起整合到指标或日志平台。 每个平台已针对特定的监视方案进行优化，各自支持 Azure Monitor 中的不同功能。 使用数据分析、可视化或警报等功能需要了解数据的差异，以便能够以最经济高效的方式实现所需的方案。 Azure Monitor 中的见解功能（例如 [Application Insights](../app/app-insights-overview.md) 或[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)）提供分析工具让你专注于特定的监视方案，而无需了解这两种数据之间的差异。 


### <a name="metrics"></a>度量值
[指标](data-platform-metrics.md)是数字值，用于描述系统某些方面在特定时间点的情况。 指标是定期收集的，使用一个时间戳、一个名称、一个值以及一个或多个定义标签进行标识。 可以使用各种算法聚合指标、将其与其他指标进行比较，以及分析不同时间的指标趋势。 

Azure Monitor 中的指标存储在时序数据库中，该数据库经过优化，可用于分析带时间戳的数据。 因此，指标特别适合用于警报和快速检测问题。 它们可以告知系统的运行状况，但通常需要与日志相结合才能识别问题的根本原因。

在 Azure 门户中使用[指标资源管理器](../app/metrics-explorer.md)可以通过指标进行交互式分析。 可将指标添加到 [Azure 仪表板](../learn/tutorial-app-dashboards.md)，以结合其他数据生成可视化效果，以及用于近实时的[警报](alerts-metric.md)。

在 [Azure Monitor 中的指标](data-platform-metrics.md)中详细了解 Azure Monitor 指标，包括其数据源。

### <a name="logs"></a>日志
[日志](data-platform-logs.md)是指系统中发生的事件。 它们可以包含不同类型的数据，并可以结构化，或者采用带时间戳的自由文本格式。 当环境中的事件生成日志项时，可以偶发性地创建日志；负载较重的系统往往会生成更多的日志。

Azure Monitor 中的日志存储在基于 [Azure 数据资源管理器](/azure/data-explorer/)的 Log Analytics 工作区中。数据资源管理器提供强大的分析引擎和[丰富查询语言](/azure/kusto/query/)。 日志通常提供足够的信息来给出所要识别的问题的完整上下文，在识别问题的根本原因时很有价值。

> [!NOTE]
> 必须将 Azure Monitor 日志与 Azure 中的日志数据源区分开来。 例如，Azure 中的订阅级事件将写入到可以通过 Azure Monitor 菜单查看的[活动日志](activity-logs-overview.md)。 大多数资源会将操作信息写入可转发到不同位置的[诊断日志](diagnostic-logs-overview.md)。 Azure Monitor 日志是一个日志数据平台，它可以收集活动日志和诊断日志以及其他监视数据，以针对整个资源集提供深入分析。


 可以在 Azure 门户中配合 [Log Analytics](../log-query/portals.md) 以交互方式使用[日志查询](../log-query/log-query-overview.md)，或者将结果添加到 [Azure 仪表板](../learn/tutorial-app-dashboards.md)，以结合其他数据生成可视化效果。 还可以创建[日志警报](alerts-log.md)，以根据计划查询的结果触发警报。

在 [Azure Monitor 中的日志](data-platform-logs.md)中详细了解 Azure Monitor 日志，包括其数据源。

### <a name="distributed-traces"></a>分布式跟踪
跟踪是通过分布式系统跟进用户请求的相关事件系列。 跟踪可用于确定应用程序代码的行为，以及不同事务的性能。 日志通常是由分布式系统的单个组件创建的，而跟踪可以度量应用程序在整套组件中的运行状况和性能。

Azure Monitor 中的分布式跟踪是使用 [Application Insights SDK](../app/distributed-tracing.md) 启用的，跟踪数据连同 Application Insights 收集的其他应用程序日志数据一起存储。 因此，可以在用于处理其他日志数据（包括日志查询、仪表板和警报）的相同分析工具中使用跟踪数据。

在[什么是分布式跟踪？](../app/distributed-tracing.md)中详细了解分布式跟踪。


## <a name="compare-azure-monitor-metrics-and-logs"></a>Azure Monitor 指标与日志的比较

下表对 Azure Monitor 中的指标和日志做了比较。

| 特性  | 度量值 | 日志 |
|:---|:---|:---|
| 优点 | 轻型数据，支持警报等近实时方案。 非常适合用于快速检测问题。 | 使用丰富查询语言进行分析。 非常适合用于深入分析和识别根本原因。 |
| Data | 仅限数字值 | 文本或数字数据 |
| 结构 | 标准属性集，包括采样时间、受监视的资源、数字值。 某些指标包含多个维度用于进一步的定义。 | 取决于日志类型的唯一属性集。 |
| Collection | 定期收集。 | 可以在事件触发要创建的记录时偶发性地收集。 |
| 在 Azure 门户中查看 | 指标资源管理器 | Log Analytics |
| 数据源包括 | 从 Azure 资源收集的平台指标。<br>Application Insights 监视的应用程序。<br>应用程序或 API 定义的自定义指标。 | 应用程序和诊断日志。<br>监视解决方案。<br>代理和 VM 扩展。<br>应用程序请求和异常。<br>Azure 安全中心。<br>数据收集器 API。 |

## <a name="collect-monitoring-data"></a>收集监视数据
不同的 [Azure Monitor 数据源](data-sources.md)会将数据写入 Log Analytics 工作区（日志）和/或 Azure Monitor 指标数据库（指标）。 某些源直接将数据写入这些数据存储，而某些源则会将数据写入 Azure 存储等其他位置，这需要指定某种配置来填充日志或指标。 

有关填充每种数据类型的不同数据源的列表，请参阅 [Azure Monitor 中的指标](data-platform-metrics.md)和 [Azure Monitor 中的日志](data-platform-logs.md)。


## <a name="stream-data-to-external-systems"></a>将数据流式传输到外部系统
除了使用 Azure 中的工具分析监视数据以外，可能还需要将这些数据转发到外部工具，例如某个安全信息和事件管理 (SIEM) 产品。 通常，这种转发是通过 [Azure 事件中心](/azure/event-hubs/)直接从受监视资源完成的。 可将某些源配置为直接向事件中心发送数据，同时，可以使用另一个进程（例如逻辑应用）来检索所需的数据。 有关详细信息，请参阅[将 Azure 监视数据流式传输到事件中心供外部工具使用](stream-monitoring-data-event-hubs.md)。



## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 中的指标](data-platform-metrics.md)。
- 详细了解 [Azure Monitor 中的日志](data-platform-logs.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。
