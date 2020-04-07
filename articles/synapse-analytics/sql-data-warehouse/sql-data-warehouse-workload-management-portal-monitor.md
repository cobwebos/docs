---
title: 工作负载管理门户监控
description: Azure 突触分析中工作负载管理门户监视指南。
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
ms.openlocfilehash: a79e6fb2be717b5ecee243b26824039630e1a416
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744289"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure 突触分析 + 工作负载管理门户监视（预览）

本文介绍如何监视[工作负载组](sql-data-warehouse-workload-isolation.md#workload-groups)资源利用率和查询活动。
有关如何配置 Azure 指标资源管理器的详细信息，请参阅[使用 Azure 指标资源管理器入门](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)一文。  有关如何监视系统资源消耗的详细信息，请参阅 Azure Synapse 分析监视文档中的资源[利用率](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization)部分。
为监视工作负载管理提供了两种不同类别的工作负荷组指标：资源分配和查询活动。  这些指标可以按工作负载组拆分和筛选。  指标可以拆分和筛选，具体取决于它们是系统定义的（资源类工作负载组）还是用户定义的指标（由用户使用[CREATE 工作组](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)语法创建）。

## <a name="workload-management-metric-definitions"></a>工作负载管理指标定义

|标准名称                    |说明  |聚合类型 |
|-------------------------------|-------------|-----------------|
|有效上限资源百分比 | *有效上限资源百分比*是工作负载组可访问的资源百分比的硬限制，同时考虑到分配给其他工作负载组*的有效最小资源百分比*。 使用[创建工作组](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)语法中的`CAP_PERCENTAGE_RESOURCE`参数配置*有效上限资源百分比*指标。  此处介绍了有效值。<br><br>`DataLoads`例如，如果使用`CAP_PERCENTAGE_RESOURCE`= 100 创建工作负荷组，并且创建另一个工作负荷组，有效最小资源百分比为 25%，`DataLoads`则工作负荷组*的有效上限资源百分比*为 75%。<br><br>*有效上限资源百分比*确定工作负载组可以达到的并发性（以及潜在吞吐量）的上限。  如果需要超出*有效上限资源百分比*指标当前报告的额外吞吐量，请增加`CAP_PERCENTAGE_RESOURCE`、减少`MIN_PERCENTAGE_RESOURCE`其他工作负荷组或向上扩展实例以添加更多资源。  减少`REQUEST_MIN_RESOURCE_GRANT_PERCENT`可能会增加并发性，但可能不会增加总体吞吐量。| 最小值，平均，最大值 |
|有效最小资源百分比 |*有效最小资源百分比*是为工作负载组保留和隔离的资源的最小百分比，同时考虑到服务级别最小值。  使用[CREATE 工作组](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)语法中的`MIN_PERCENTAGE_RESOURCE`参数配置有效最小资源百分比指标。  [此处](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values)介绍了有效值。<br><br>当此指标未筛选且未拆分时，请使用 Sum 聚合类型来监视系统上配置的总工作负载隔离。<br><br>*有效最小资源百分比*确定工作负载组可以实现的保证并发性（从而保证吞吐量）的下限。  如果需要超出*有效最小资源百分比*指标当前报告的其他保证资源，则增加为工作负载组配置`MIN_PERCENTAGE_RESOURCE`的参数。  减少`REQUEST_MIN_RESOURCE_GRANT_PERCENT`可能会增加并发性，但可能不会增加总体吞吐量。 |最小值，平均，最大值|
|工作负载组活动查询  |此指标报告工作负荷组中的活动查询。  使用此指标未筛选和未拆分将显示系统上运行的所有活动查询。|SUM         |
|按最大资源百分比分配工作负载组 |此指标显示相对于每个工作负载组*的有效上限资源百分比*的资源分配的百分比。  此指标提供工作负载组的有效利用。<br><br>考虑有效*上限资源百分比为*75%且配置为 25% 的`REQUEST_MIN_RESOURCE_GRANT_PERCENT`工作负载组。 `DataLoads`  *按最大资源百分比值划分的工作负载组分配*值`DataLoads`将筛选为 33%（25% / 75%）如果此工作负荷组中运行了单个查询。<br><br>使用此指标可以标识工作负荷组的利用率。  接近 100% 的值表示正在使用工作负载组的所有可用资源。  此外，显示值大于零的同一工作负荷组的*工作负载组排队查询指标*将指示工作负荷组在分配时将利用其他资源。  相反，如果此指标始终较低，并且*工作负载组活动查询*较低，则未使用工作负载组。  如果*有效上限资源百分比*大于零，则这种情况尤其成问题，因为这将指示[未充分利用的工作负载隔离](#underutilized-workload-isolation)。|最小值，平均，最大值 |
|按系统百分比分配工作负载组 | 此指标显示相对于整个系统的资源分配的百分比。<br><br>考虑配置为`DataLoads`25% 的`REQUEST_MIN_RESOURCE_GRANT_PERCENT`工作负载组。  *按系统百分比值*筛选`DataLoads`为 25%（25% / 100%）的工作负载组分配如果此工作负荷组中运行了单个查询。|最小值，平均，最大值 |
|工作负载组查询超时 |已超时的工作负载组的查询。 此指标报告的查询超时仅在查询开始执行后（不包括由于锁定或资源等待而导致的等待时间）。<br><br>使用`QUERY_EXECUTION_TIMEOUT_SEC`[创建工作组](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)语法中的参数配置查询超时。  增加该值可以减少查询超时数。<br><br>请考虑增加工作负载`REQUEST_MIN_RESOURCE_GRANT_PERCENT`组的参数，以减少超时量，并为每个查询分配更多资源。  请注意，增加`REQUEST_MIN_RESOURCE_GRANT_PERCENT`可减少工作负载组的并发量。 |SUM |
|工作负载组排队查询 | 当前排队等待开始执行的工作负载组的查询。  查询可以是队列，因为它们正在等待资源或锁。<br><br>查询可能等待的原因有很多。  如果系统超载，并且并发需求大于可用需求，查询将排队。<br><br>请考虑通过增加`CAP_PERCENTAGE_RESOURCE`["创建工作群体"](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)语句中的参数，向工作负载组添加更多资源。  如果`CAP_PERCENTAGE_RESOURCE`大于*有效上限资源百分比*指标，则其他工作负载组的已配置工作负载隔离会影响分配给此工作负载组的资源。  请考虑降低`MIN_PERCENTAGE_RESOURCE`其他工作负荷组或向上扩展实例以添加更多资源。 |SUM |

## <a name="monitoring-scenarios-and-actions"></a>监视方案和操作

下面是一系列图表配置，用于突出显示用于故障排除的工作负载管理指标使用情况以及解决问题的相关操作。

### <a name="underutilized-workload-isolation"></a>未充分利用的工作负载隔离

请考虑以下`wgPriority`工作负载组和分类器配置，其中创建了名为工作负荷组，并使用`wcCEOPriority`工作负载分类器映射到*TheCEO。* `membername`  工作负载`wgPriority`组为其配置了 25% 的工作负载隔离`MIN_PERCENTAGE_RESOURCE`（= 25）。  *Ceo*提交的每个查询都得到 5% 的系统资源`REQUEST_MIN_RESOURCE_GRANT_PERCENT`（= 5）。

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

下图的配置如下：<br>
指标 1：*有效最小资源百分比*（平均聚合`blue line`，）<br>
指标 2：*按系统百分比分配的工作负载组*（平均聚合`purple line`， ）<br>
过滤器： [工作负载组] |`wgPriority`<br>
![](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png)图表显示，在 25% 的工作负载隔离下，平均仅使用 10%。  在这种情况下，`MIN_PERCENTAGE_RESOURCE`参数值可以降低到 10 或 15 之间，并允许系统上的其他工作负载消耗资源。

### <a name="workload-group-bottleneck"></a>工作负载组瓶颈

请考虑以下`wgDataAnalyst`工作负载组和分类器配置，其中创建了名为工作负荷组，并使用`wcDataAnalyst`工作负载分类器将*DataAnalyst*`membername`映射到该配置。  工作负载`wgDataAnalyst`组为其配置了 6% 的工作负载隔离`MIN_PERCENTAGE_RESOURCE`（= 6），资源限制为 9%（=`CAP_PERCENTAGE_RESOURCE` 9）。  *数据分析师*提交的每个查询都得到系统资源的 3% （=`REQUEST_MIN_RESOURCE_GRANT_PERCENT` 3）。

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

下图的配置如下：<br>
指标 1：*有效上限资源百分比*（平均聚合`blue line`，）<br>
指标 2：*按最大资源百分比（平均聚合）划分工作负载组分配*。 `purple line`<br>
指标 3：*工作负载组排队查询*（总和聚合）， `turquoise line`<br>
过滤器： [工作负载组] |`wgDataAnalyst`<br>
![瓶颈-wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png)图表显示，如果资源上限为 9%，则工作负载组利用率为 90%*（从*工作负载组分配中按最大资源百分比指标）。*  如*从工作负载组排队查询指标*中所示，查询有稳定的队列。  在这种情况下，将 的值`CAP_PERCENTAGE_RESOURCE`增加到高于 9% 的值将允许同时执行更多查询。  增加`CAP_PERCENTAGE_RESOURCE`假定有足够的可用资源，并且其他工作负荷组不隔离。  通过检查*有效上限资源百分比指标*来验证增加的上限。  如果需要更多吞吐量，也应考虑将 增加到`REQUEST_MIN_RESOURCE_GRANT_PERCENT`大于 3 的值。  增加`REQUEST_MIN_RESOURCE_GRANT_PERCENT`可以允许查询运行得更快。

## <a name="next-steps"></a>后续步骤

- [快速入门：使用 T-SQL 配置工作负载隔离](quickstart-configure-workload-isolation-tsql.md)<br>
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [监视资源利用率](sql-data-warehouse-concept-resource-utilization-query-activity.md)
