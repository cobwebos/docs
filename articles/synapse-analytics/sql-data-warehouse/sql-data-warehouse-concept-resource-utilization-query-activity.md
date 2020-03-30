---
title: 可管理性和监视 - 查询活动、资源利用率
description: 了解可用于管理和监视 Azure 突触分析的功能。 使用 Azure 门户和动态管理视图 (DMV) 来了解数据仓库的查询活动和资源利用率。
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d2a9bb0fcaab38f897987a1922e9c95497821d4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350675"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>监视 Azure 突触分析中的资源利用率和查询活动
Azure Synapse 分析在 Azure 门户中提供了丰富的监视体验，以显示有关数据仓库工作负荷的见解。 建议使用 Azure 门户来监视数据仓库，因为它提供可配置的保持期、警报、建议，并为指标和日志提供可自定义的图表与仪表板。 该门户还使您能够与其他 Azure 监视服务（如 Azure 监视器（日志）与日志分析集成，不仅为您的数据仓库提供整体监视体验，还可以为集成的整个 Azure 分析平台提供整体监控体验监控经验。 本文档介绍使用 SQL Analytics 优化和管理分析平台的监控功能。 

## <a name="resource-utilization"></a>资源利用率 
以下指标在 Azure 门户中可用于 SQL 分析。 这些指标通过 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics) 显示。


| 标准名称             | 描述                                                  | 聚合类型 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU 百分比          | 数据仓库所有节点的 CPU 利用率      | 平均，最小，最大值    |
| 数据 IO 百分比      | 数据仓库所有节点的 IO 利用率       | 平均，最小，最大值    |
| 内存百分比       | 数据仓库所有节点的内存利用率 (SQL Server) | 平均，最小，最大值   |
| 活动查询          | 在系统上执行的活动查询数             | SUM              |
| 排队查询          | 等待开始执行的排队查询数          | SUM              |
| 成功的连接数  | 数据的成功连接数                 | 总和，计数       |
| 失败的连接数      | 数据仓库的失败连接数           | 总和，计数       |
| 被防火墙阻止     | 登录数据仓库受阻次数     | 总和，计数       |
| DWU 限制               | 数据仓库的服务级别目标                | 平均，最小，最大值    |
| DWU 百分比          | CPU 百分比与数据 IO 百分比之间的最大值        | 平均，最小，最大值    |
| 已用的 DWU                | DWU 限制 * DWU 百分比                                   | 平均，最小，最大值    |
| 缓存命中百分比    | (缓存命中数/缓存未命中数) * 100，其中，缓存命中数是在本地 SSD 缓存中所有列存储段的总命中数，缓存未命中数是所有节点上本地 SSD 缓存中列存储段的未命中数之和 | 平均，最小，最大值    |
| 缓存使用百分比   | (已用缓存/缓存容量) * 100，其中，已用缓存是所有节点上的本地 SSD 缓存中所有字节之和，缓存容量是所有节点上的本地 SSD 缓存存储容量之和 | 平均，最小，最大值    |
| 本地 tempdb 百分比 | 所有计算节点上的本地 tempdb 利用率 - 每五分钟发出一次值 | 平均，最小，最大值    |
| 数据存储大小 | 加载到数据库中的数据的总大小。 这包括驻留在 CCI 和非 CCI 表中的数据，其中非 CCI 表的大小由数据库文件总大小衡量 | SUM |
| 灾难恢复大小 | 每 24 小时进行一次的地理备份的总大小 | SUM |
| 快照存储大小 | 为提供数据库还原点而拍摄的快照的总大小。 这包括自动快照和用户定义的快照。 | SUM |

查看指标和设置警报时要考虑的事项：

- 使用的 DWU 仅表示整个 SQL 池的**使用情况的高级表示形式**，并不表示综合利用率指标。 要确定是向上还是向下扩展，请考虑所有可能受 DWU 影响的因素，如并发性、内存、tempdb 和自适应缓存容量。 我们建议[在不同的 DWU 设置下运行您的工作负载](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview#finding-the-right-size-of-data-warehouse-units)，以确定哪些工作最符合您的业务目标。
- 为特定数据仓库（而不是逻辑服务器）报告失败和成功的连接
- 即使数据仓库处于空闲状态，内存百分比也会反映利用率，而不会反映活动工作负荷内存消耗。 使用并跟踪这个指标以及其他指标（tempdb、gen2 缓存），以全面判断扩展额外的缓存容量是否会提高工作负荷性能以满足你的需求。


## <a name="query-activity"></a>查询活动
对于通过 T-SQL 监视 SQL 分析的编程体验，该服务提供了一组动态管理视图 （DMV）。 在主动排查和识别工作负荷的性能瓶颈时，这些视图非常有用。

要查看 SQL Analytics 提供的 DMV 列表，请参阅[本文档](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)。 

## <a name="metrics-and-diagnostics-logging"></a>指标和诊断日志记录
指标和日志都可导出到 Azure Monitor（具体而言，是 [Azure Monitor 日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)组件），并可通过[日志查询](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)以编程方式访问它们。 SQL 分析的日志延迟约为 10-15 分钟。 有关影响延迟的因素的更多详细信息，请访问以下文档。


## <a name="next-steps"></a>后续步骤
以下操作方法指南介绍了在监视和管理数据仓库时可以参考的常见方案和用例：

- [使用 DMV 监视数据仓库工作负荷](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
