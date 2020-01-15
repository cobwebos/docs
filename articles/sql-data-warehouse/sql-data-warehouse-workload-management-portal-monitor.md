---
title: 工作负荷管理门户监视
description: Azure Synapse 分析中的工作负荷管理门户监视指南。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/13/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: f3baaab59031c4cfad036a7181318502d1969715
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942420"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse Analytics –工作负荷管理门户监视（预览版）
本文介绍如何监视[工作负荷组](sql-data-warehouse-workload-isolation.md#workload-groups)资源使用情况和查询活动。 有关如何配置 Azure 指标资源管理器的详细信息，请参阅[azure 入门指标资源管理器](../azure-monitor/platform/metrics-getting-started.md)文章。  有关如何监视系统资源使用情况的详细信息，请参阅 Azure SQL 数据仓库监视文档中的[资源利用率](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization)部分。
提供了两种不同类别的工作负荷组指标用于监视工作负荷管理：资源分配和查询活动。  可以按工作负荷组拆分和筛选这些指标。  根据度量值是系统定义的（资源类工作负荷组）还是用户定义的（用户使用[CREATE 工作负荷组](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)语法创建），可以对其进行拆分和筛选。

## <a name="workload-management-metric-definitions"></a>工作负荷管理指标定义

|标准名称                    |Description  |聚合类型 |
|-------------------------------|-------------|-----------------|
|有效上限资源百分比 | *有效上限资源百分比*是工作负荷组可访问的资源百分比的硬性限制，考虑为其他工作负荷组分配的*有效的最小资源百分比*。 *有效上限资源百分比*指标是使用[CREATE 工作负荷组](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)语法中的 `CAP_PERCENTAGE_RESOURCE` 参数配置的。  此处描述了有效值。<br><br>例如，如果工作负荷组 `DataLoads` 是使用 `CAP_PERCENTAGE_RESOURCE` = 100 创建的，并且创建了另一个工作负荷组，其有效的最小资源百分比为25%，则 `DataLoads` 工作负荷组的*有效上限资源百分比*为75%。<br><br>*有效的上限资源百分比*确定工作负荷组可以实现的并发（因而可能是吞吐量）的上限。  如果 "*有效上限资源百分比*" 指标当前所报告的内容超出了所需的吞吐量，请增加 `CAP_PERCENTAGE_RESOURCE`，减少其他工作负荷组的 `MIN_PERCENTAGE_RESOURCE`，或纵向扩展实例以添加更多资源。  降低 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 会提高并发性，但可能不会提高整体吞吐量。| 最小值、平均值、最大值 |
|有效的最小资源百分比 |"*有效的最小资源百分比*" 是指为工作负荷组预留和隔离的资源的最小百分比，最小值为服务级别。  有效的最小资源百分比指标是使用[CREATE 工作负荷组](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)语法中的 `MIN_PERCENTAGE_RESOURCE` 参数配置的。  [此处](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values)描述了有效值。<br><br>如果未筛选此指标，请使用 Sum 聚合类型，并撤消以监视系统上配置的总工作负荷隔离。<br><br>*有效的最小资源百分比*确定工作负荷组可以实现的有保证并发性（从而保证吞吐量）。  如果需要的其他保证资源超出了 "*有效的最小资源百分比*" 度量值当前报告的范围，请增加为工作负荷组配置的 `MIN_PERCENTAGE_RESOURCE` 参数。  降低 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 会提高并发性，但可能不会提高整体吞吐量。 |最小值、平均值、最大值|
|工作负荷组活动查询  |此指标报告工作负荷组内的活动查询。  如果使用未筛选的此指标并撤消显示在系统上运行的所有活动查询。|SUM         |
|按最大资源百分比分配的工作负荷组 |此指标显示相对于每个工作负荷组的*有效上限资源百分比*的资源分配百分比。  此指标提供工作负荷组的有效利用率。<br><br>假设工作负荷组 `DataLoads` 的*有效 cap 资源百分比*为75%，而 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 配置为25%。  按筛选到 `DataLoads` 的*最大资源百分比值分配的工作负荷组*将为33% （25%/75%）如果在此工作负荷组中运行单个查询，则为。<br><br>使用此指标标识工作负荷组的利用率。  接近100% 的值表示正在使用工作负荷组的所有可用资源。  此外，显示大于零的相同工作负荷组的 "*工作负荷组排队查询" 度量*值将指示工作负荷组将利用其他资源（如果已分配）。  相反，如果此指标持续较低，且*工作负荷组活动查询*较少，则不会使用工作负荷组。  如果*有效上限资源百分比*大于零，则这种情况尤其有问题，因为这会指示[工作量隔离不充分](#underutilized-workload-isolation)。|最小值、平均值、最大值 |
|按系统百分比分配的工作负荷组 | 此指标显示相对于整个系统的资源分配百分比。<br><br>假设工作负荷组 `DataLoads` 配置为25% 的 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`。  按筛选到 `DataLoads`*的系统百分比值分配工作负荷组*将为25% （25%/100%）如果在此工作负荷组中运行单个查询，则为。|最小值、平均值、最大值 |
|工作负荷组查询超时 |查询已超时的工作负荷组。 此指标报告的查询超时仅在开始执行查询后（不包括由于锁定或资源等待而导致的等待时间）。<br><br>查询超时值是使用[CREATE 工作负荷组](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)语法中的 `QUERY_EXECUTION_TIMEOUT_SEC` 参数配置的。  增加值可以减少查询超时。<br><br>考虑增加工作负荷组的 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 参数，以减少超时量并为每个查询分配更多资源。  请注意，增加 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 会减少工作负荷组的并发量。 |SUM |
|工作负荷组排队查询 | 查询当前排队等待开始执行的工作负荷组。  查询可以是排队的，因为它们正在等待资源或锁。<br><br>查询可能正在等待多种原因。  如果系统过载并且并发要求大于可用的数量，则查询将排队。<br><br>请考虑通过在[CREATE 工作负荷组](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)语句中增加 `CAP_PERCENTAGE_RESOURCE` 参数，将更多资源添加到工作负荷组。  如果 `CAP_PERCENTAGE_RESOURCE` 大于*有效上限资源百分比*度量值，则为其他工作负荷组配置的工作负荷隔离会影响分配给此工作负荷组的资源。  请考虑降低其他工作负荷组 `MIN_PERCENTAGE_RESOURCE` 或纵向扩展实例以添加更多资源。 |SUM |

## <a name="monitoring-scenarios-and-actions"></a>监视方案和操作
下面是一系列图表配置，用于重点介绍工作负荷管理指标的使用情况，以及用于解决问题的相关操作。

### <a name="underutilized-workload-isolation"></a>充分利用的工作负荷隔离
请考虑以下工作负荷组和分类器配置，其中创建了名为 `wgPriority` 的工作负荷组，并使用 `wcCEOPriority` 的工作负荷分类器将*TheCEO* `membername` 映射到该工作负荷组。  `wgPriority` 工作负荷组为其配置了25% 的工作负荷隔离（`MIN_PERCENTAGE_RESOURCE` = 25）。  *TheCEO*提交的每个查询都给定5% 的系统资源（`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5）。
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
以下图表配置如下：度量值1：有效的*最小资源百分比*（avg 聚合，`blue line`）指标2：*按系统百分比分配的工作负荷组分配*（avg 聚合，`purple line`）筛选器： [工作负荷组] = `wgPriority`
![underutilized-wg](media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) 图表显示了25% 的工作负荷隔离，平均平均使用10%。  在这种情况下，可以将 `MIN_PERCENTAGE_RESOURCE` 参数值降低到10到15之间，并允许系统中的其他工作负荷使用这些资源。

### <a name="workload-group-bottleneck"></a>工作负荷组瓶颈
请考虑以下工作负荷组和分类器配置，其中创建了一个名为 `wgDataAnalyst` 的工作负荷组，并使用 `wcDataAnalyst` 的工作负荷分类器将*DataAnalyst* `membername` 映射到该工作负荷组。  "`wgDataAnalyst` 工作负荷组" 为其配置了6% 的工作负荷隔离（`MIN_PERCENTAGE_RESOURCE` = 6），资源限制为9% （`CAP_PERCENTAGE_RESOURCE` = 9）。  *DataAnalyst*提交的每个查询都提供系统资源的3% （`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3）。

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
以下图表配置如下：度量值1：*有效上限资源百分比*（平均聚合，`blue line`）指标2：*按最大资源百分比*（Avg 聚合，`purple line`）指标3：*工作负荷组排队查询*（Sum 聚合，）指标3：工作负荷组排队查询（Sum 聚合，`turquoise line`）筛选器的工作负荷组分配： [工作负荷组] = `wgDataAnalyst`
![necked-wg](media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) *资源百分比度量值*）。  如*工作负荷组排队查询指标*所示，有一种稳定的查询队列。  在这种情况下，将 `CAP_PERCENTAGE_RESOURCE` 增大到大于9% 的值将允许同时执行更多查询。  增加 `CAP_PERCENTAGE_RESOURCE` 假设有足够的可用资源，并且其他工作负荷组未隔离资源。  通过检查 "*有效 cap 资源百分比" 指标*来验证上限。  如果需要更大的吞吐量，还应考虑将 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 增加到大于3的值。  增加 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 可能导致查询的运行速度更快。

## <a name="next-steps"></a>后续步骤
[快速入门：使用 T-sql 配置工作负荷隔离](quickstart-configure-workload-isolation-tsql.md)<br>
[CREATE 工作负荷组（Transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[CREATE 工作负荷分类器（Transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[监视资源利用率](sql-data-warehouse-concept-resource-utilization-query-activity.md)

