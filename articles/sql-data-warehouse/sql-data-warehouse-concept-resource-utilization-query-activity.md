---
title: Azure SQL 数据仓库的可管理性和监视 - 查询活动、资源利用率 | Microsoft Docs
description: 了解可以使用哪些功能来管理和监视 Azure SQL 数据仓库。 使用 Azure 门户和动态管理视图 (DMV) 来了解数据仓库的查询活动和资源利用率。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/26/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: c783045d242725ee19dfe7e0baee13625d986312
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2018
ms.locfileid: "43246488"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>监视 Azure SQL 数据仓库中的资源利用率和查询活动
Azure SQL 数据仓库在 Azure 门户中提供丰富的监视体验用于洞察数据仓库工作负荷。 建议使用 Azure 门户来监视数据仓库，因为它提供可配置的保留期、警报、建议，并为指标和日志提供可自定义的图表与仪表板。 在门户中，还可与 Operations Management Suite (OMS)/Log Analytics 和 Azure Monitor 等其他 Azure 监视服务集成，这样，不仅可以针对数据仓库，而且还能针对整个 Azure 分析平台提供一体式的监视体验，构成一种集成式的监视体验。 本文档介绍可在 SQL 数据仓库中使用哪些监视功能来优化和管理分析平台。 

## <a name="resource-utilization"></a>资源利用率 
Azure 门户中提供以下指标。

| 指标名称                           | Description     | 聚合类型 |
| --------------------------------------- | ---------------- | --------------------------------------- |
| CPU 百分比                          | 数据仓库所有节点的 CPU 利用率 | 最大值      |
| 数据 IO 百分比                      | 数据仓库所有节点的 IO 利用率 | 最大值   |
| 成功的连接数                  | 数据的成功连接数 | 总计            |
| 失败的连接数                      | 数据仓库的失败连接数 | 总计            |
| 被防火墙阻止                     | 登录数据仓库受阻次数 | 总计            |
| DWU 限制                              | 数据仓库的服务级别目标 | 最大值   |
| DWU 百分比                          | CPU 百分比与数据 IO 百分比之间的最大值 | 最大值   |
| 已用的 DWU                                | DWU 限制 * DWU 百分比 | 最大值   |
| 缓存命中百分比 | (缓存命中数/缓存未命中数) * 100，其中，缓存命中数是在本地 SSD 缓存中所有列存储段的总命中数，缓存未命中数是所有节点上本地 SSD 缓存中列存储段的未命中数之和 | 最大值 |
| 缓存使用百分比 | (已用缓存/缓存容量) * 100，其中，已用缓存是所有节点上的本地 SSD 缓存中所有字节之和，缓存容量是所有节点上的本地 SSD 缓存存储容量之和 | 最大值 |

## <a name="query-activity"></a>查询活动
为了让用户通过 T-SQL 以编程方式监视 SQL 数据仓库，该服务提供了一系列动态管理视图 (DMV)。 在主动排查和识别工作负荷的性能瓶颈时，这些视图非常有用。

若要查看 SQL 数据仓库提供的 DMV 列表，请参阅此[文档](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)。 

## <a name="metrics-and-diagnostics-logging"></a>指标和诊断日志记录
可将指标和日志导出到 [Operations Management Suite](https://azure.microsoft.com/resources/videos/operations-management-suite-oms-overview/) (OMS)（具体而言，是 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 组件），并可通过[日志搜索](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)以编程方式访问它们。


> [!NOTE]
> 截至 2018 年 8 月，SQL 数据仓库的日志功能目前仍在实现中

## <a name="next-steps"></a>后续步骤
以下操作方法指南介绍了在监视和管理数据仓库时可以参考的常见方案和用例：

- [使用 DMV 监视数据仓库工作负荷](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

