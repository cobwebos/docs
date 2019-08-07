---
title: Azure Monitor 日志的结构 | Microsoft Docs
description: 需要执行日志查询来检索 Azure Monitor 提供的日志数据。  本文介绍新的日志查询在 Azure Monitor 中的用法以及创建搜索之前需要了解的概念。
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: 6f5ae426018c9e7fa2ac586a2886c8e5e609069b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68813846"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure Monitor 日志的结构
使用[日志查询](log-query-overview.md)快速洞察数据是 Azure Monitor 提供的一项强大功能。 若要创建高效且有用的查询，应该了解一些基本概念，例如，所需数据的位置及其构建方式。 本文将会介绍可帮助你入门的基本概念。

## <a name="overview"></a>概述
Azure Monitor 日志中的数据存储在 Log Analytics 工作区或 Application Insights 应用程序中。 两者都由 [Azure 数据资源管理器](/azure/data-explorer/)提供支持，这意味着，它们将利用数据资源管理器的强大数据引擎和查询语言。

工作区和应用程序中的数据组织成表，每个表存储不同类型的数据，并且有自身独特的属性集。 大多数[数据源](../platform/data-sources.md)将数据写入到 Log Analytics 工作区中其自身的表内，而 Application Insights 将数据写入到 Application Insights 应用程序中一组预定义的表内。 日志查询非常灵活，可让你轻松组合多个表的数据，甚至使用跨资源查询组合多个工作区中的表的数据，或者编写查询来组合工作区数据和应用程序数据。

下图显示了写入到示例查询中使用的不同表的数据源示例。

![表](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics 工作区
Azure Monitor 日志收集的所有数据（Application Insights 数据除外）存储在 [Log Analytics 工作区](../platform/manage-access.md)中。 你可以根据特定的要求创建一个或多个工作区。 [数据源](../platform/data-sources.md)（例如 Azure 资源中的活动日志和诊断日志、虚拟机上的代理以及见解和监视解决方案中的数据）会将数据写入到在加入过程中配置的一个或多个工作区。 其他服务（例如 [Azure 安全中心](/azure/security-center/)和 [Azure Sentinel](/azure/sentinel/)）还会使用 Log Analytics 工作区来存储其数据，因此，可以使用日志查询结合来自其他源的数据对其进行分析。

不同类型的数据存储在工作区中的不同表内，每个表具有独特的属性集。 创建工作区后，会将一组标准表添加到其中；加入不同的数据源、解决方案和服务后，将添加其新表。 还可以使用[数据收集器 API](../platform/data-collector-api.md) 创建自定义表。

可以在工作区的 Log Analytics 中的“架构”选项卡上浏览工作区中的表及其架构。

![工作区架构](media/scope/workspace-schema.png)

使用以下查询列出工作区中的表，以及在前一天收集到每个表中的记录数。 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
有关每个数据源创建的表的详细信息，请参阅相应数据源的文档。 例如，参阅有关[代理数据源](../platform/agent-data-sources.md)、[诊断日志](../platform/diagnostic-logs-schema.md)和[监视解决方案](../insights/solutions-inventory.md)的文章。

### <a name="workspace-permissions"></a>工作区权限
有关提供对工作区中数据的访问权限的详细信息，请参阅[工作区权限和范围](../platform/manage-access.md#manage-accounts-and-users)。 除了授予对工作区本身的访问权限以外，还可以使用[表级别 RBAC](../platform/manage-access.md#table-level-rbac) 限制对单个表的访问。

## <a name="application-insights-application"></a>Application Insights 应用程序
在 Application Insights 中创建应用程序时，会自动在 Azure Monitor 日志中创建相应的应用程序。 无需进行任何配置即可收集数据，应用程序会自动写入页面查看次数、请求和异常等监视数据。

与 Log Analytics 工作区不同，Application Insights 应用程序具有固定的一组表。 无法将其他数据源配置为写入到应用程序，因此无法创建其他表。 

| 表 | 描述 | 
|:---|:---|
| availabilityResults | 可用性测试中的摘要数据。 |
| browserTimings      | 有关客户端性能的数据，例如处理传入数据所用的时间。 |
| customEvents        | 应用程序创建的自定义事件。 |
| customMetrics       | 应用程序创建的自定义指标。 |
| dependencies        | 从应用程序对外部组件发出的调用。 |
| exceptions          | 应用程序运行时引发的异常。 |
| pageViews           | 每个网站的浏览情况数据，以及浏览器信息。 |
| performanceCounters | 支持该应用程序的计算资源的性能度量值。 |
| 请求            | 每个应用程序请求的详细信息。  |
| traces              | 分布式跟踪的结果。 |

可以在应用程序的 Log Analytics 中的“架构”选项卡上查看每个表的架构。

![应用程序架构](media/scope/application-schema.png)

## <a name="standard-properties"></a>标准属性
尽管 Azure Monitor 日志中的每个表具有自身的架构，但所有表共享某些标准属性。 有关详细信息，请参阅 [Azure Monitor 日志中的标准属性](../platform/log-standard-properties.md)。

| Log Analytics 工作区 | Application Insights 应用程序 | 描述 |
|:---|:---|:---|
| TimeGenerated | timestamp  | 创建记录的日期和时间。 |
| 类型          | itemType   | 从中检索到该记录的表的名称。 |
| _ResourceId   |            | 与该记录关联的资源的唯一标识符。 |
| _IsBillable   |            | 指定是否对引入的数据计费。 |
| _BilledSize   |            | 指定要计费的数据大小（以字节为单位）。 |

## <a name="next-steps"></a>后续步骤
- 了解如何使用 [Log Analytics 来创建并编辑日志搜索](../log-query/portals.md)。
- 查看使用新查询语言的[查询编写教程](../log-query/get-started-queries.md)。
