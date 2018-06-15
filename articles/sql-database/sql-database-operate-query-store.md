---
title: 在 Azure SQL 数据库中操作 Query Store
description: 了解如何在 Azure SQL 数据库中操作 Query Store
services: sql-database
author: bonova
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: bonova
ms.openlocfilehash: 92e4180f1efe62d2dae9778f70e25f1bb0273b7f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649877"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>在 Azure SQL 数据库中操作 Query Store
Azure 中的 Query Store 是完全托管的数据库功能，可持续收集和提供有关所有查询的详细历史信息。 可以将 Query Store 视为一个航班数据记录器，它可以大幅简化云与本地客户的查询性能故障排除。 本文说明在 Azure 中操作 Query Store 的具体方法。 使用这些预先收集的查询数据，可以快速诊断并解决性能问题，因此将更多的时间投入到业务上。 

从 2015 年 11 月开始，Query Store 已在 Azure SQL 数据库中[全球推出](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/)。 Query Store 是性能分析和优化功能（例如 [SQL 数据库顾问和性能仪表板](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)）的基础。 在本文发布时，Query Store 正在 Azure 中运行 200,000 多个用户数据库，不间断地收集多个月的查询相关信息。

> [!IMPORTANT]
> Microsoft 即将为所有 Azure SQL 数据库（现有的和新的）激活 Query Store。 
> 
> 

## <a name="optimal-query-store-configuration"></a>最佳的 Query Store 配置
本部分描述最佳的配置默认值，这些默认值旨在确保 Query Store 以及依赖功能（例如 [SQL 数据库顾问和性能仪表板](https://azure.microsoft.com/updates/sqldatabaseadvisorga/)）能够可靠运行。 默认配置已针对持续数据收集操作进行优化，即，在 OFF/READ_ONLY 状态下花费最少的时间。

| 配置 | 说明 | 默认 | 注释 |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |指定 Query Store 在客户数据库中占用的数据空间的限制 |100 |对新数据库强制实施 |
| INTERVAL_LENGTH_MINUTES |定义聚合和持久化查询计划收集运行时统计信息的时段大小。 每个活动查询计划将为此配置定义的时间段包含最多一行 |60 |对新数据库强制实施 |
| STALE_QUERY_THRESHOLD_DAYS |基于时间的清理策略，控制持久化运行时统计信息和非活动查询的保留期 |30 |对新数据库和使用以前的默认值 (367) 的数据库强制实施 |
| SIZE_BASED_CLEANUP_MODE |指定当 Query Store 数据大小接近限制时是否自动清理数据 |AUTO |对所有数据库强制实施 |
| QUERY_CAPTURE_MODE |指定是要跟踪所有查询，还是只跟踪一部分查询 |AUTO |对所有数据库强制实施 |
| FLUSH_INTERVAL_SECONDS |指定捕获的运行时统计信息在刷新到磁盘之前，保留在内存中的最大期限 |900 |对新数据库强制实施 |
|  | | | |

> [!IMPORTANT]
> 在 Query Store 的最终激活阶段，会在所有 Azure SQL 数据库中自动应用这些默认值（请参阅上面的重要说明）。 激活后，Azure SQL 数据库不会更改客户设置的配置值，除非这些值对主要工作负荷或 Query Store 的可靠运行造成负面影响。
> 
> 

如果想要保持使用自定义设置，请[结合 Query Store 选项使用 ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx)，将配置还原到以前的状态。 请查看 [Query Store 最佳实践](https://msdn.microsoft.com/library/mt604821.aspx)，了解如何选择最佳的配置参数。

## <a name="next-steps"></a>后续步骤
[SQL 数据库性能见解](sql-database-performance.md)

## <a name="additional-resources"></a>其他资源
有关详细信息，请查看以下文章：

* [数据库的航班数据记录器](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 
* [使用 Query Store 监视性能](https://msdn.microsoft.com/library/dn817826.aspx)
* [Query Store 使用方案](https://msdn.microsoft.com/library/mt614796.aspx)
* [使用 Query Store 监视性能](https://msdn.microsoft.com/library/dn817826.aspx) 

