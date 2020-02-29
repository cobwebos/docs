---
title: 可管理性和监视-查询活动，资源利用率
description: 了解可用于管理和监视 Azure Synapse 分析的功能。 使用 Azure 门户和动态管理视图 (DMV) 来了解数据仓库的查询活动和资源利用率。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 47f142a19ac470fb29e9542941cd94a6b29ce240
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195917"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>监视 Azure Synapse 分析中的资源利用率和查询活动
Azure Synapse Analytics 在 Azure 门户内提供丰富的监视体验，以将见解深入到数据仓库工作负荷。 建议使用 Azure 门户来监视数据仓库，因为它提供可配置的保持期、警报、建议，并为指标和日志提供可自定义的图表与仪表板。 门户还使你可以与其他 Azure 监视服务（如 Operations Management Suite （OMS）和 Azure Monitor （日志））集成，以提供全面的监视体验，不仅针对数据仓库，还提供整个 Azure 分析集成监视体验的平台。 本文档介绍可用于优化和管理使用 SQL Analytics 分析平台的监视功能。 

## <a name="resource-utilization"></a>资源利用率 
SQL Analytics Azure 门户中提供了以下指标。 这些指标通过 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics) 显示。


| 标准名称             | 说明                                                  | 聚合类型 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU 百分比          | 数据仓库所有节点的 CPU 利用率      | Avg、Min、Max    |
| 数据 IO 百分比      | 数据仓库所有节点的 IO 利用率       | Avg、Min、Max    |
| 内存百分比       | 数据仓库所有节点的内存使用率（SQL Server） | Avg、Min、Max   |
| 活动查询          | 系统上执行的活动查询数             | SUM              |
| 排队的查询          | 等待开始执行的排队查询数          | SUM              |
| 成功的连接数  | 数据的成功连接数                 | 求和、计数       |
| 失败的连接数      | 数据仓库的失败连接数           | 求和、计数       |
| 被防火墙阻止     | 登录数据仓库受阻次数     | 求和、计数       |
| DWU 限制               | 数据仓库的服务级别目标                | Avg、Min、Max    |
| DWU 百分比          | CPU 百分比与数据 IO 百分比之间的最大值        | Avg、Min、Max    |
| 已用的 DWU                | DWU 限制 * DWU 百分比                                   | Avg、Min、Max    |
| 缓存命中百分比    | (缓存命中数/缓存未命中数) * 100，其中，缓存命中数是在本地 SSD 缓存中所有列存储段的总命中数，缓存未命中数是所有节点上本地 SSD 缓存中列存储段的未命中数之和 | Avg、Min、Max    |
| 缓存使用百分比   | (已用缓存/缓存容量) * 100，其中，已用缓存是所有节点上的本地 SSD 缓存中所有字节之和，缓存容量是所有节点上的本地 SSD 缓存存储容量之和 | Avg、Min、Max    |
| 本地 tempdb 百分比 | 所有计算节点上的本地 tempdb 利用率 - 每五分钟发出一次值 | Avg、Min、Max    |

查看度量值和设置警报时要考虑的事项：

- 针对特定数据仓库报告失败和成功的连接-不是针对逻辑服务器
- 即使数据仓库处于空闲状态，内存百分比也会反映利用率，而不会反映活动工作负荷的内存消耗。 使用并跟踪此指标和其他人（tempdb、gen2 缓存），以便对更多缓存容量的规模提高工作负荷性能以满足您的要求进行全面的决策。


## <a name="query-activity"></a>查询活动
对于通过 T-sql 监视 SQL Analytics 时的编程体验，该服务提供了一组动态管理视图（Dmv）。 在主动排查和识别工作负荷的性能瓶颈时，这些视图非常有用。

若要查看 SQL Analytics 提供的 Dmv 列表，请参阅此[文档](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)。 

## <a name="metrics-and-diagnostics-logging"></a>指标和诊断日志记录
指标和日志都可以导出到 Azure Monitor，特别是[Azure Monitor 日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)组件，可以通过[日志查询](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)以编程方式访问。 SQL Analytics 的日志延迟约为10-15 分钟。 有关影响延迟的因素的详细信息，请访问以下文档。


## <a name="next-steps"></a>后续步骤
以下操作方法指南介绍了在监视和管理数据仓库时可以参考的常见方案和用例：

- [使用 DMV 监视数据仓库工作负荷](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
