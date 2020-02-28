---
title: Azure Monitor 日志的结构 |Microsoft Docs
description: 需要执行日志查询来检索 Azure Monitor 提供的日志数据。  本文介绍新的日志查询在 Azure Monitor 中的用法以及创建搜索之前需要了解的概念。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662070"
---
# <a name="structure-of-azure-monitor-logs"></a>Azure Monitor 日志的结构
使用[日志查询](log-query-overview.md)快速获取数据的见解是 Azure Monitor 的一项强大功能。 若要创建有效的有用查询，您应该了解一些基本概念，如您所需的数据所在的位置和结构的结构。 本文提供了入门所需的基本概念。

## <a name="overview"></a>概述
Azure Monitor 日志中的数据存储在 Log Analytics 工作区或 Application Insights 应用程序中。 两者均由 Azure 提供支持[数据资源管理器](/azure/data-explorer/)意味着它们利用强大的数据引擎和查询语言。

工作区和应用程序中的数据被组织到表中，每个表都存储不同种类的数据，并且具有其自己的唯一属性集。 大多数[数据源](../platform/data-sources.md)会在 Log Analytics 工作区中写入其自己的表，而 Application Insights 将写入 Application Insights 应用程序中的预定义表集。 日志查询非常灵活，允许你轻松合并多个表中的数据，甚至使用跨资源查询合并多个工作区中的表中的数据，或者编写合并工作区和应用程序数据的查询。

下图显示了写入示例查询中使用的不同表的数据源示例。

![表](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics 工作区
除 Application Insights 以外 Azure Monitor 日志收集的所有数据都存储在[Log Analytics 工作区](../platform/manage-access.md)中。 你可以根据特定要求创建一个或多个工作区。 [数据源](../platform/data-sources.md)（例如，来自 Azure 资源的活动日志和资源日志、虚拟机上的代理以及来自 insights 和监视解决方案的数据）会将数据写入你作为其载入部分配置的一个或多个工作区。 其他服务（例如[Azure 安全中心](/azure/security-center/)和[azure Sentinel](/azure/sentinel/) ）还使用 Log Analytics 工作区来存储其数据，以便可以使用日志查询以及监视来自其他源的数据进行分析。

不同类型的数据存储在工作区中的不同表中，每个表都具有唯一的属性集。 创建工作区时，将一组标准表添加到工作区，并为不同的数据源、解决方案和服务载入添加新表。 你还可以使用[数据收集器 API](../platform/data-collector-api.md)创建自定义表。

可以在工作区中的 "**架构**" 选项卡 Log Analytics 中浏览工作区中的表及其架构。

![工作区架构](media/scope/workspace-schema.png)

使用以下查询来列出工作区中的表，以及前一天内收集的记录数。 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
有关其创建的表的详细信息，请参阅每个数据源的文档。 示例包括用于[代理数据源](../platform/agent-data-sources.md)、[资源日志](../platform/diagnostic-logs-schema.md)和[监视解决方案](../insights/solutions-inventory.md)的文章。

### <a name="workspace-permissions"></a>工作区权限
请参阅[设计 Azure Monitor 日志部署](../platform/design-logs-deployment.md)以了解访问控制策略和建议，以提供对工作区中数据的访问。 除了授予对工作区本身的访问权限外，还可以使用[表级别 RBAC](../platform/manage-access.md#table-level-rbac)限制对各个表的访问。

## <a name="application-insights-application"></a>Application Insights 应用程序
在 Application Insights 中创建应用程序时，会在 Azure Monitor 日志中自动创建相应的应用程序。 收集数据不需要进行任何配置，应用程序将自动写入监视数据，例如页面视图、请求和异常。

与 Log Analytics 工作区不同，Application Insights 应用程序具有一组固定的表。 不能将其他数据源配置为写入应用程序，因此不能创建其他表。 

| 表 | 说明 | 
|:---|:---|
| availabilityResults | 来自可用性测试的摘要数据。 |
| browserTimings      | 有关客户端性能的数据，例如处理传入数据所用的时间。 |
| customEvents        | 应用程序创建的自定义事件。 |
| customMetrics       | 应用程序创建的自定义指标。 |
| 依赖关系        | 从应用程序到外部组件的调用。 |
| 异常          | 应用程序运行时引发的异常。 |
| pageViews           | 有关每个网站视图的数据与浏览器信息。 |
| performanceCounters | 支持应用程序的计算资源的性能度量。 |
| 请求            | 每个应用程序请求的详细信息。  |
| traces              | 分布式跟踪的结果。 |

可以在应用程序 Log Analytics 的 "**架构**" 选项卡中查看每个表的架构。

![应用程序架构](media/scope/application-schema.png)

## <a name="standard-properties"></a>标准属性
虽然 Azure Monitor 日志中的每个表都有其自己的架构，但所有表均共享了标准属性。 有关每个的详细信息，请参阅[Azure Monitor 日志中的标准属性](../platform/log-standard-properties.md)。

| Log Analytics 工作区 | Application Insights 应用程序 | 说明 |
|:---|:---|:---|
| TimeGenerated | timestamp  | 创建记录的日期和时间。 |
| 类型          | itemType   | 从中检索记录的表的名称。 |
| _ResourceId   |            | 与记录关联的资源的唯一标识符。 |
| _IsBillable   |            | 指定引入数据是否可计费。 |
| _BilledSize   |            | 指定将计费的数据的大小（以字节为单位）。 |

## <a name="next-steps"></a>后续步骤
- 了解如何使用[Log Analytics 来创建和编辑日志搜索](../log-query/portals.md)。
- 查看使用新查询语言的[查询编写教程](../log-query/get-started-queries.md)。
