---
title: Synapse SQL 建议
description: 了解 Synapse SQL 建议及其生成方式
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/30/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 546945d70554adbb28f19a3153faa67495e55f04
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607737"
---
# <a name="synapse-sql-recommendations"></a>Synapse SQL 建议

本文介绍了通过 Azure 顾问提供的 Synapse SQL 建议。  

SQL Analytics 提供了一些建议，确保数据仓库工作负载始终针对性能进行优化。 建议与 [Azure 顾问](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)紧密集成，直接在 [Azure 门户](https://aka.ms/Azureadvisor)中为你提供最佳做法。 SQL Analytics 每天收集活动工作负载的遥测数据并提出建议。 下面概述了支持的推荐方案，以及如何应用建议的操作。

可以立即[查看你的建议](https://aka.ms/Azureadvisor)！ 

## <a name="data-skew"></a>数据倾斜

运行工作负荷时，数据倾斜可能会造成额外的数据移动或资源瓶颈。 以下文档介绍了如何识别数据倾斜，以及如何通过选择最佳的分布键来防止发生数据倾斜。

- [识别和消除倾斜](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>无统计信息或统计信息过时

统计信息欠佳可能严重影响查询性能，因为它可能导致 SQL 查询优化器生成欠佳的查询计划。 以下文档介绍了有关创建和更新统计信息的最佳做法：

- [创建和更新表统计信息](sql-data-warehouse-tables-statistics.md)

若要查看受这些建议影响的表列表，请运行以下 [T-SQL 脚本](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)。 顾问会不断运行相同的 T-SQL 脚本以生成这些建议。

## <a name="replicate-tables"></a>复制表

为了提供复制表的建议，顾问将基于以下物理特征检测表候选项：

- 复制表的大小
- 列数
- 表分布类型
- 分区数

顾问持续利用基于工作负荷的试探法（例如，表的访问频率、平均返回的行数、有关数据仓库大小和活动的阈值）来确保生成优质建议。

以下部分介绍了可以在每个复制表建议的 Azure 门户中找到的基于工作负荷的试探法：

- 扫描平均值 - 在过去七天每次访问表时，从表中返回的行的平均百分比
- 频繁读取且不更新 - 表示该表在过去七天尚未更新，同时显示访问活动
- 读取/更新频率 - 访问表的频率，相对于过去七天更新表的时间
- 活动 - 基于访问活动度量使用情况。 此活动将在过去7天内，相对于数据仓库的平均表访问活动比较表访问活动。

目前，顾问一次性只会显示包含聚集列存储索引的、具有最高活动优先级的最多四个复制表候选项。

> [!IMPORTANT]
> 复制表建议不是完整的证据，且未考虑帐户数据移动操作。 我们正在努力将此添加为一种试探方法，但在此期间，请始终在应用建议后验证工作负荷。 若要详细了解复制表，请访问以下[文档](design-guidance-for-replicated-tables.md#what-is-a-replicated-table)。


## <a name="adaptive-gen2-cache-utilization"></a>自适应（Gen2）缓存利用率
如果你有一个很大的工作集，则可能会出现缓存命中百分比较低和缓存利用率较高的情况。 对于这种情况，应增加大小以增加缓存容量并重新运行工作负荷。 有关详细信息，请访问以下[文档](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-how-to-monitor-cache)。 

## <a name="tempdb-contention"></a>Tempdb 争用

当存在高 tempdb 争用时，查询性能可能会降低。  Tempdb 争用可以通过用户定义的临时表或在发生大量数据移动时发生。 对于这种情况，可以扩展更多 tempdb 分配，并[配置资源类和工作负荷管理](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)，为查询提供更多内存。 
