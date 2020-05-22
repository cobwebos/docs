---
title: 工作负荷管理门户监视
description: Azure Synapse Analytics 中的工作负荷管理门户监视指南。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 31533eefbfae63e0eb4049d2eabaf6b853340636
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590241"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring"></a>Azure Synapse Analytics – 工作负荷管理门户监视

本文介绍了如何监视[工作负荷组](sql-data-warehouse-workload-isolation.md#workload-groups)资源利用率和查询活动。
有关如何配置 Azure 指标资源管理器的详细信息，请参阅 [Azure 指标资源管理器入门](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)一文。  有关如何监视系统资源使用情况的详细信息，请参阅 Azure Synapse Analytics 监视文档中的[资源利用率](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization)部分。
有两种不同类别的工作负荷组指标用于监视工作负荷管理：资源分配和查询活动。  可以按工作负荷组拆分和筛选这些指标。  根据指标是系统定义的（资源类工作负荷组）还是用户定义的（由用户通过 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法创建），可以对其进行拆分和筛选。

## <a name="workload-management-metric-definitions"></a>工作负荷管理指标定义

|标准名称                    |说明  |聚合类型 |
|-------------------------------|-------------|-----------------|
|有效的资源百分比上限 | “有效的资源百分比上限”是对工作负荷组可访问的资源百分比的硬性限制，它考虑到了为其他工作负荷组分配的“有效的最小资源百分比”。 使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中的 `CAP_PERCENTAGE_RESOURCE` 参数配置“有效的资源百分比上限”指标。  此处描述了有效值。<br><br>例如，如果一个工作负荷组 `DataLoads` 是使用 `CAP_PERCENTAGE_RESOURCE` = 100 创建的，而另一个工作负荷组是使用 25% 的有效最小资源百分比创建的，则 `DataLoads` 工作负荷组的“有效的资源百分比上限”为 75%。<br><br>“有效的资源百分比上限”确定了工作负荷组可以实现的并发上限（以及可能的吞吐量）。  除了“有效的资源百分比上限”指标当前报告的数量外，如果还需要更多吞吐量，要么增加 `CAP_PERCENTAGE_RESOURCE`，减少其他工作负荷组的 `MIN_PERCENTAGE_RESOURCE`，要么扩展实例以添加更多资源。  降低 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 会提升并发性，但可能不会提高整体吞吐量。| 最小值、平均值、最大值 |
|有效的最小资源百分比 |“有效的最小资源百分比”是指针对最低服务级别为工作负荷组预留和隔离的资源的最小百分比。  使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中的 `MIN_PERCENTAGE_RESOURCE` 参数配置“有效的最小资源百分比”指标。  [此处](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values)描述了有效值。<br><br>如果未筛选和拆分此指标，使用 Sum 聚合类型可监视系统上配置的总工作负荷隔离情况。<br><br>“有效的最小资源百分比”确定了工作负荷组可以实现的保证并发下限（以及保证的吞吐量）。  除了“有效的最小资源百分比”指标当前报告的数量外，如果还需要更多保证资源，请增加为工作负荷组配置的 `MIN_PERCENTAGE_RESOURCE` 参数。  降低 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 会提升并发性，但可能不会提高整体吞吐量。 |最小值、平均值、最大值|
|工作负荷组活动查询  |此指标用于报告工作负荷组内的活动查询。  在取消筛选和拆分的情况下，此指标会显示系统上运行的所有活动查询。|SUM         |
|按最大资源百分比列出的工作负荷组分配 |此指标显示了每个工作负荷组相对于“有效的资源百分比上限”的资源分配百分比。  该指标提供了工作负荷组的有效利用率。<br><br>假设工作负荷组 `DataLoads`的“有效的资源百分比上限”为 75%，`REQUEST_MIN_RESOURCE_GRANT_PERCENT` 配置为 25%。  如果在此工作负荷组中运行单个查询，则为 `DataLoads`“按最大资源百分比列出的工作负荷组分配”筛选的值将为 33% (25%/75%)。<br><br>使用此指标可标识工作负荷组的利用率。  值接近 100% 表示正在使用的工作负荷组拥有所有可用资源。  此外，对于同一工作负荷组，如果“工作负荷组排队查询”指标显示的值大于零，则表示此工作负荷组将利用其他资源（如果已分配）。  相反，如果此指标持续较低，“工作负荷组活动查询”也较低，则说明当前未使用该工作负荷组。  如果“有效的资源百分比上限”大于零，则这种情况尤其有问题，因为这表示[工作负荷隔离利用不足](#underutilized-workload-isolation)。|最小值、平均值、最大值 |
|按系统百分比列出的工作负荷组分配 | 此指标显示相对于整个系统的资源分配百分比。<br><br>假设工作负荷组 `DataLoads` 配置有 25% 的 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`。  如果在此工作负荷组中运行单个查询，则“按系统百分比列出的工作负荷组分配”为 `DataLoads` 筛选的值将为 25% (25%/100%)。|最小值、平均值、最大值 |
|工作负荷组查询超时 |超时的工作负荷组查询。仅在开始执行查询后，此指标才报告查询超时（不包括由于锁定或资源等待而导致的等待时间）。<br><br>使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中的 `QUERY_EXECUTION_TIMEOUT_SEC` 参数配置查询超时。  增加此值可以减少查询超时次数。<br><br>考虑增加工作负荷组的 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 参数，以减少超时次数并为每个查询分配更多资源。  请注意，增加 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 会减少工作负荷组的并发量。 |SUM |
|工作负荷组排队查询 | 当前排队等待开始执行的工作负荷组查询。  当查询等待资源或锁时，可为其排队。<br><br>查询可能会出于多种原因而处于等待状态。  如果系统过载且并发需求超过了可用的资源，则查询将会排队。<br><br>请考虑增加 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语句中的 `CAP_PERCENTAGE_RESOURCE` 参数，将更多资源添加到工作负荷组。  如果 `CAP_PERCENTAGE_RESOURCE` 大于“有效的资源百分比上限”指标，则为其他工作负荷组配置的工作负荷隔离会影响分配到此工作负荷组的资源。  请考虑降低其他工作负荷组的 `MIN_PERCENTAGE_RESOURCE` 或纵向扩展实例以添加更多资源。 |SUM |

## <a name="monitoring-scenarios-and-actions"></a>监视方案和操作

下面是一系列图表配置，其中重点介绍了工作负荷管理指标在故障排除方面的用法，以及解决问题的相关操作。

### <a name="underutilized-workload-isolation"></a>工作负荷隔离利用不足

假设在以下工作负荷组和分类器配置中创建了名为 `wgPriority` 的工作负荷组，TheCEO `membername` 使用 `wcCEOPriority` 工作负荷分类器映射到此工作负荷组。  `wgPriority` 工作负荷组为其配置了 25% 的工作负荷隔离 (`MIN_PERCENTAGE_RESOURCE` = 25)。  TheCEO 提交的每个查询获得了 5% 的系统资源 (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5)。

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

下方的图表使用以下指标配置：<br>
指标 1：有效的最小资源百分比（平均值聚合，`blue line`）<br>
指标 2：按系统百分比列出的工作负荷组分配（平均值聚合，`purple line`）<br>
筛选：[工作负荷组] = `wgPriority`<br>
![underutilized-wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) 该图表显示，工作负荷隔离配置为 25% 时，平均使用率仅为 10%。  在这种情况下，可将 `MIN_PERCENTAGE_RESOURCE` 参数值降至 10 到 15 之间，并允许系统上的其他工作负荷使用资源。

### <a name="workload-group-bottleneck"></a>工作负荷组瓶颈

假设在以下工作负荷组和分类器配置中创建了名为 `wgDataAnalyst` 的工作负荷组，DataAnalyst `membername` 使用 `wcDataAnalyst` 工作负荷分类器映射到此工作负荷组。  `wgDataAnalyst` 工作负荷组为其配置了 6% 的工作负荷组隔离(`MIN_PERCENTAGE_RESOURCE` = 6)，资源限制为 9% (`CAP_PERCENTAGE_RESOURCE` = 9)。  DataAnalyst 提交的每个查询获得了 3% 的系统资源 (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3)。

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

下方的图表使用以下指标配置：<br>
指标 1：有效的资源百分比上限（平均值聚合，`blue line`）<br>
指标 2：按最大资源百分比列出的工作负荷组分配（平均值聚合，`purple line`）<br>
指标 3：工作负荷组排队查询（总和聚合，`turquoise line`）<br>
筛选：[工作负荷组] = `wgDataAnalyst`<br>
![bottle-necked-wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) 该图表显示，资源上限为 9% 时，工作负荷组的利用率为 90% 以上（从“按最大资源百分比列出的工作负荷组分配”指标可以看出）。  如“工作负荷组排队查询”指标所示，查询正在稳定排队。  在这种情况下，将 `CAP_PERCENTAGE_RESOURCE` 值增加至 9% 以上将允许更多查询并行执行。  增加 `CAP_PERCENTAGE_RESOURCE` 的前提条件是有足够的可用资源，并且其他工作负荷组未隔离资源。  通过检查“有效的资源百分比上限”指标来确认上限是否增加。  如果需要更高的吞吐量，另请考虑将 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 值增加至 3 以上。  增加 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 可以提高查询的运行速度。

## <a name="next-steps"></a>后续步骤

- [快速入门：使用 T-SQL 配置工作负荷隔离](quickstart-configure-workload-isolation-tsql.md)<br>
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [监视资源利用率](sql-data-warehouse-concept-resource-utilization-query-activity.md)
