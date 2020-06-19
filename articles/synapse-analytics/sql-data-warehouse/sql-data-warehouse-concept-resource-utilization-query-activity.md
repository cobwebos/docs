---
title: 可管理性和监视 - 查询活动、资源利用率
description: 了解可以使用哪些功能来管理和监视 Azure Synapse Analytics。 使用 Azure 门户和动态管理视图 (DMV) 来了解数据仓库的查询活动和资源利用率。
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7c09e9d9f93ead6f894c954f647ebe33918cf41d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653017"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>监视 Azure Synapse Analytics 中的资源利用率和查询活动

Azure Synapse Analytics 在 Azure 门户中提供了丰富的监视体验，可用于洞察数据仓库工作负载。 建议使用 Azure 门户来监视数据仓库，因为它提供可配置的保持期、警报、建议，并为指标和日志提供可自定义的图表与仪表板。 在门户中，还可与具有日志分析功能的 Azure Monitor（日志）等其他 Azure 监视服务集成，这样，不仅可以针对数据仓库，而且还能为整个 Azure 分析平台提供全面的集成式监视体验。 本文档介绍了可在 Synapse SQL 中使用哪些监视功能来优化和管理分析平台。

## <a name="resource-utilization"></a>资源利用率

Azure 门户中提供了以下可用于 Synapse SQL 的指标。 这些指标通过 [Azure Monitor](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics) 显示。

| 标准名称             | 说明                                                  | 聚合类型 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU 百分比          | 数据仓库所有节点的 CPU 利用率      | 平均值、最小值、最大值    |
| 数据 IO 百分比      | 数据仓库所有节点的 IO 利用率       | 平均值、最小值、最大值    |
| 内存百分比       | 数据仓库所有节点的内存利用率 (SQL Server) | 平均值、最小值、最大值   |
| 活动查询          | 系统上执行的活动查询数             | SUM              |
| 已排队查询数          | 等待开始执行的排队查询数          | SUM              |
| 成功的连接数  | 针对数据库的成功连接数（登录次数） | 总和、计数       |
| 失败的连接数      | 针对数据库的失败连接数（登录次数） | 总和、计数       |
| 被防火墙阻止     | 登录数据仓库受阻次数     | 总和、计数       |
| DWU 限制               | 数据仓库的服务级别目标                | 平均值、最小值、最大值    |
| DWU 百分比          | CPU 百分比与数据 IO 百分比之间的最大值        | 平均值、最小值、最大值    |
| 已用的 DWU                | DWU 限制 * DWU 百分比                                   | 平均值、最小值、最大值    |
| 缓存命中百分比    | (缓存命中数/缓存未命中数) * 100，其中，缓存命中数是在本地 SSD 缓存中所有列存储段的总命中数，缓存未命中数是所有节点上本地 SSD 缓存中列存储段的未命中数之和 | 平均值、最小值、最大值    |
| 缓存使用百分比   | (已用缓存/缓存容量) * 100，其中，已用缓存是所有节点上的本地 SSD 缓存中所有字节之和，缓存容量是所有节点上的本地 SSD 缓存存储容量之和 | 平均值、最小值、最大值    |
| 本地 tempdb 百分比 | 所有计算节点上的本地 tempdb 利用率 - 每五分钟发出一次值 | 平均值、最小值、最大值    |
| 数据存储大小 (GB) | 数据库总计大小。 这包括已使用、已预留和未分配的空间。 为数据库保留未分配的空间，以优化查询和加载性能。 | SUM |
| 灾难恢复大小 (GB) | 每 24 小时进行一次异地备份的总大小 | SUM |
| 快照存储大小 (GB) | 用于提供数据库还原点的快照的总大小。 这包括自动和用户定义的快照。 | SUM |

查看指标和设置警报时的注意事项：

- 使用的 DWU 只是 SQL 池中使用情况的一种概要表示，并不是一种全面的利用率指标。 若要确定是纵向扩展还是缩减，请考虑可能会受 DWU 影响的所有因素，如并发性、内存、tempdb 和自适应缓存容量。 建议[在不同的 DWU 设置下运行工作负载](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units)，以确定哪种设置最适合你的业务目标。
- 会报告特定数据仓库（而不是逻辑服务器）失败和成功的连接数
- 即使数据仓库处于空闲状态，内存百分比也会反映利用率，而不会反映活动工作负载的内存占用率。 使用并跟踪这个指标与其他指标（tempdb、gen2 缓存），以便全面决策扩展额外的缓存容量是否会提高工作负载性能，以满足你的需求。

## <a name="query-activity"></a>查询活动

为了让用户通过 T-SQL 以编程方式监视 Synapse SQL，该服务提供了一系列动态管理视图 (DMV)。 在主动排查和识别工作负荷的性能瓶颈时，这些视图非常有用。

若要查看 Synapse SQL 提供的 DMV 列表，请参阅此[文档](../sql/reference-tsql-system-views.md#sql-pool-dynamic-management-views-dmvs)。 

## <a name="metrics-and-diagnostics-logging"></a>指标和诊断日志记录 

指标和日志都可导出到 Azure Monitor（确切地说，是 [Azure Monitor 日志](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)组件），并可通过[日志查询](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)以编程方式进行访问。 Synapse SQL 的日志延迟约为 10-15 分钟。 有关影响延迟的各种因素的详细信息，请访问以下文档。

## <a name="next-steps"></a>后续步骤

以下操作指南介绍了在监视和管理数据仓库时可以参考的常见方案和用例：

- [使用 DMV 监视数据仓库工作负荷](sql-data-warehouse-manage-monitor.md)
