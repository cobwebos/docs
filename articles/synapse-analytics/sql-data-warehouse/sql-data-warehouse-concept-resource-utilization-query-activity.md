---
title: 可管理性和监视 - 查询活动、资源利用率
description: 了解可用于管理和监视 Azure Synapse 分析的功能。 使用 Azure 门户和动态管理视图 (DMV) 来了解数据仓库的查询活动和资源利用率。
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
ms.openlocfilehash: d38c0df45da3a751a456846813543a4ce5de98eb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416219"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>监视 Azure Synapse 分析中的资源利用率和查询活动

Azure Synapse Analytics 在 Azure 门户内提供丰富的监视体验，以了解有关数据仓库工作负荷的信息。 建议使用 Azure 门户来监视数据仓库，因为它提供可配置的保持期、警报、建议，并为指标和日志提供可自定义的图表与仪表板。 使用门户还可以将其他 Azure 监视服务（如 Azure Monitor （日志）与 Log analytics 集成，以便不仅为你的数据仓库提供总体监视体验，还提供整个 Azure 分析平台来实现集成监视体验。 本文档介绍可用于优化和管理使用 SQL Analytics 分析平台的监视功能。

## <a name="resource-utilization"></a>资源利用率

SQL Analytics Azure 门户中提供了以下指标。 这些指标通过 [Azure Monitor](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics) 显示。

| 标准名称             | 说明                                                  | 聚合类型 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU 百分比          | 数据仓库所有节点的 CPU 利用率      | Avg、Min、Max    |
| 数据 IO 百分比      | 数据仓库所有节点的 IO 利用率       | Avg、Min、Max    |
| 内存百分比       | 数据仓库所有节点的内存利用率 (SQL Server) | Avg、Min、Max   |
| 活动查询数          | 正在系统上执行的活动查询数             | SUM              |
| 排队的查询数          | 等待开始执行的排队查询数          | SUM              |
| 成功的连接数  | 针对数据库的成功连接数（登录名） | Sum、Count       |
| 失败的连接数      | 针对数据库的失败连接数（登录名） | Sum、Count       |
| 被防火墙阻止     | 登录数据仓库受阻次数     | Sum、Count       |
| DWU 限制               | 数据仓库的服务级别目标                | Avg、Min、Max    |
| DWU 百分比          | CPU 百分比与数据 IO 百分比之间的最大值        | Avg、Min、Max    |
| 已用的 DWU                | DWU 限制 * DWU 百分比                                   | Avg、Min、Max    |
| 缓存命中百分比    | (缓存命中数/缓存未命中数) * 100，其中，缓存命中数是在本地 SSD 缓存中所有列存储段的总命中数，缓存未命中数是所有节点上本地 SSD 缓存中列存储段的未命中数之和 | Avg、Min、Max    |
| 缓存使用百分比   | (已用缓存/缓存容量) * 100，其中，已用缓存是所有节点上的本地 SSD 缓存中所有字节之和，缓存容量是所有节点上的本地 SSD 缓存存储容量之和 | Avg、Min、Max    |
| 本地 tempdb 百分比 | 所有计算节点上的本地 tempdb 利用率 - 每五分钟发出一次值 | Avg、Min、Max    |
| 数据存储大小（GB） | 数据库总计大小。 这包括使用的、保留的和未分配的空间。 为数据库保留未分配的空间，以优化查询和加载性能。 | SUM |
| 灾难恢复大小（GB） | 每24小时进行异地备份的总大小 | SUM |
| 快照存储大小（GB） | 用于提供数据库还原点的快照的总大小。 这包括自动和用户定义的快照。 | SUM |

查看指标和设置警报时要考虑的事项：

- 使用的 DWU 只表示在 SQL 池中**使用的高级表示形式**，并不是一种全面的利用率指标。 若要确定是向上扩展还是向下缩放，请考虑可能会受 DWU 影响的所有因素，如并发性、内存、tempdb 和自适应缓存容量。 建议[在不同的 DWU 设置中运行工作负荷](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units)，以确定哪种方法最适合你的业务目标。
- 为特定数据仓库（而不是逻辑服务器）报告失败和成功的连接
- 即使数据仓库处于空闲状态，内存百分比也会反映利用率，而不会反映活动工作负荷内存消耗。 使用并跟踪这个指标以及其他指标（tempdb、gen2 缓存），以全面判断扩展额外的缓存容量是否会提高工作负荷性能以满足你的需求。

## <a name="query-activity"></a>查询活动

对于通过 T-sql 监视 SQL Analytics 时的编程体验，该服务提供了一组动态管理视图（Dmv）。 在主动排查和识别工作负荷的性能瓶颈时，这些视图非常有用。

若要查看适用于 Synapse SQL 的 Dmv 列表，请参阅此[文档](../sql/reference-tsql-system-views.md#sql-pool-dynamic-management-views-dmvs)。 

## <a name="metrics-and-diagnostics-logging"></a>指标和诊断日志记录 

指标和日志都可导出到 Azure Monitor（具体而言，是 [Azure Monitor 日志](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)组件），并可通过[日志查询](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)以编程方式访问它们。 SQL Analytics 的日志延迟约为10-15 分钟。 有关影响延迟的因素的更多详细信息，请访问以下文档。

## <a name="next-steps"></a>后续步骤

以下操作方法指南介绍了监视和管理数据仓库时的常见方案和用例：

- [使用 DMV 监视数据仓库工作负荷](sql-data-warehouse-manage-monitor.md)
