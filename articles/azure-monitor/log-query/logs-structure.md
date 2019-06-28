---
title: Azure Monitor 日志结构 |Microsoft Docs
description: 需要执行日志查询来检索 Azure Monitor 提供的日志数据。  本文介绍新的日志查询在 Azure Monitor 中的用法以及创建搜索之前需要了解的概念。
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: e243ebbc31f9e941678ac76a83738276995b5ba1
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297130"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure Monitor 日志的结构
能够快速深入了解使用数据[日志查询](log-query-overview.md)是 Azure Monitor 的一项强大功能。 若要创建高效且有用的查询，应了解一些基本概念，如所需的数据所在的位置和它的方式。 本文提供了您入门所需的基本概念。

## <a name="overview"></a>概述
Azure Monitor 日志中的数据存储在 Log Analytics 工作区或 Application Insights 应用程序。 同时后盾[Azure 数据资源管理器](/azure/data-explorer/)这意味着它们利用其功能强大的数据引擎和查询语言。

工作区和应用程序中的数据划分为多个表，其中每个存储不同类型的数据，并且有其自己唯一的属性集。 大多数[数据源](../platform/data-sources.md)Application Insights 将写入到一组预定义的 Application Insights 应用程序中的表时将写入到 Log Analytics 工作区中，在其自己的表。 日志查询时非常灵活，从而可以轻松地合并来自多个表的数据，甚至使用跨资源查询合并来自多个工作区中的表的数据或编写查询，以组合工作区和应用程序数据。

下图显示了写入到的示例查询中使用的不同表的数据源的示例。

![表](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics 工作区
通过 Azure Monitor 日志除了 Application Insights 收集的所有数据都存储在[Log Analytics 工作区](../platform/manage-access.md)。 您可以创建一个或多个工作区，具体取决于特定的要求。 [数据源](../platform/data-sources.md)活动日志和诊断日志从 Azure 资源，例如虚拟机和见解和监视解决方案中的数据上的代理会将数据写入到配置为其载入的一部分的一个或多个工作区。 其他服务，例如[Azure 安全中心](/azure/security-center/)并[Azure Sentinel](/azure/sentinel/)还使用 Log Analytics 工作区来存储其数据，因此使用日志查询，以及监视从其他数据进行分析源。

不同类型的数据存储在工作区中，在不同的表和每个表包含一组唯一的属性。 一组标准的表添加到工作区创建，且新表添加为不同的数据源、 解决方案和服务，因为它们时载入。 此外可以创建使用自定义表[数据收集器 API](../platform/data-collector-api.md)。

您可以浏览工作区并在其架构中的表**架构**中 Log Analytics 工作区的选项卡。

![工作区架构](media/scope/workspace-schema.png)

使用以下查询列出工作区和的记录数中的表收集到每个通过前一天。 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
请参阅它们创建的表的详细信息每个数据源的文档。 示例包括文章，了解[代理数据源](../platform/agent-data-sources.md)，[诊断日志](../platform/diagnostic-logs-schema.md)，并[监视解决方案](../insights/solutions-inventory.md)。

### <a name="workspace-permissions"></a>工作区权限
请参阅[工作区的权限和作用域](../platform/manage-access.md#workspace-permissions-and-scope)有关对工作区中数据访问的详细信息。 除了授予访问工作区本身，可以限制对单个表使用的访问[表级别 RBAC](../platform/manage-access.md#table-level-rbac)。

## <a name="application-insights-application"></a>Application Insights 应用程序
在 Application Insights 中创建应用程序时，将自动在 Azure Monitor 日志中创建相应的应用程序。 无需配置要收集数据，并将自动编写应用程序监视数据，例如页面视图、 请求和异常。

与 Log Analytics 工作区中，不同的 Application Insights 应用程序具有一组固定的表。 不能配置其他数据源写入应用程序，因此可以不创建任何其他表。 

| 表 | 描述 | 
|:---|:---|
| availabilityResults | 可用性测试的摘要数据。 |
| browserTimings      | 有关客户端性能，例如处理传入的数据所花费的时间的数据。 |
| customEvents        | 应用程序创建的自定义事件。 |
| customMetrics       | 应用程序创建的自定义指标。 |
| 依赖项        | 应用程序中的外部组件调用。 |
| 异常          | 由应用程序运行时引发的异常。 |
| pageViews           | 有关每个网站的数据视图的浏览器信息。 |
| performanceCounters | 从支持该应用程序的计算资源的性能度量值。 |
| 请求            | 每个应用程序请求的详细信息。  |
| traces              | 从分布式跟踪的结果。 |

您可以查看每个表中的架构**架构**Log Analytics 中的应用程序的选项卡。

![应用程序架构](media/scope/application-schema.png)

## <a name="standard-properties"></a>标准属性
在 Azure Monitor 日志中的每个表具有其自己的架构，在共享的所有表的标准属性。 请参阅[Azure Monitor 日志中的标准属性](../platform/log-standard-properties.md)的每个详细信息。

| Log Analytics 工作区 | Application Insights 应用程序 | 描述 |
|:---|:---|:---|
| TimeGenerated | timestamp  | 记录的创建日期和时间。 |
| Type          | itemType   | 该记录中检索到的表的名称。 |
| _ResourceId   |            | 与之关联的资源记录的唯一标识符。 |
| _IsBillable   |            | 指定是否为可计费的引入的数据。 |
| _BilledSize   |            | 指定的大小以字节为单位的数据收费。 |

## <a name="next-steps"></a>后续步骤
- 了解如何使用[Log Analytics，以创建和编辑日志搜索](../log-query/portals.md)。
- 查看使用新查询语言的[查询编写教程](../log-query/get-started-queries.md)。
