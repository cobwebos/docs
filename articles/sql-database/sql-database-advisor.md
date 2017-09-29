---
title: "性能建议 - Azure SQL 数据库 | Microsoft Docs"
description: "Azure SQL 数据库提供有关 SQL 数据库的建议，以提升当前的查询性能。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/20/2017
ms.author: sstein
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 53bfbd602c2c395d510529eacd5b8075b20437ab
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="performance-recommendations"></a>性能建议

Azure SQL 数据库可以学习和适应应用程序并提供自定义的建议，使你能够将 SQL 数据库的性能最大化。 通过分析 SQL 数据库使用情况历史记录，可以持续评估性能。 提供的建议是以数据库唯一工作负载模式为依据，有助于提升性能。

> [!TIP]
> [自动优化](sql-database-automatic-tuning.md)是性能优化的推荐方式。 [智能见解](sql-database-intelligent-insights.md)是监控性能的推荐方式。 
>

## <a name="create-index-recommendations"></a>“创建索引”建议
Azure SQL 数据库持续监视执行中的查询，并发现可以提升性能的索引。 确信缺少特定索引后，便会新建“创建索引”建议。 Azure SQL 数据库通过估计索引在一段时间内带来的性能提升，确信是否有必要创建索引。 根据估计的性能提升高低，将性能建议分为高、中或低三类。 

通过应用建议创建的索引始终都会标记为 auto_created。 可以通过查看 sys.indexes 视图，确定哪些是 auto_created 索引。 自动创建的索引不会阻止 ALTER/RENAME 命令。 如果尝试删除包含自动创建的索引的列，那么命令将会传递下去，自动创建的索引会随列一起删除。 常规索引会阻止对已编制索引的列执行 ALTER/RENAME 命令。

应用“创建索引”建议后，Azure SQL 数据库便会比较查询性能与基线性能。 如果新索引确实带来了性能提升，系统会将建议标记为成功，并生成影响力报表。 否则，索引将会自动还原。 这样，Azure SQL 数据库可确保应用的建议只会提升数据库性能。

所有“创建索引”建议都有撤回策略，即如果在过去 20 分钟内数据库或池 DTU 使用率超过 80% 或存储空间使用率超过 90%，则不允许应用建议。 在这种情况下，将推迟应用建议。

## <a name="drop-index-recommendations"></a>“删除索引”建议
除了能够检测缺少的索引外，Azure SQL 数据库还能持续分析现有索引的性能。 Azure SQL 数据库会建议删除未使用的索引。 在以下两种情况下，建议删除索引：
* 索引是另一索引的副本（已编入索引且包含的列、分区架构和筛选器都相同）
* 长时间（93 天）未使用索引

也会验证已实现的“删除索引”建议。 如果性能得到提升，将会生成影响力报表。 如果检测到性能降低，将会还原建议。


## <a name="parameterize-queries-recommendations"></a>参数化查询建议
当具有一个或多个正在持续被重新编译但都以相同的查询执行计划结束的查询时，就会出现**参数化查询**建议。 这种状态提供了一个应用强制参数化的机会，其允许查询计划进行缓存并在将来可以被重复使用，从而改善性能和减少资源使用。 

对 SQL Server 发出的每个查询一开始需要进行编译，生成执行计划。 每个生成的计划将添加到计划缓存中，相同查询的后续执行可以重复使用该缓存中的此计划，而无需进一步编译。 

发送包含非参数化值的查询的应用程序可能会导致性能开销，其中对于使用不同参数值的每个此类查询，将重新编译执行计划。 在许多情况下，使用不同参数值的相同查询将生成相同的执行计划，但这些计划将仍然分别添加到计划缓存中。 重新编译执行计划会占用数据库资源、增加查询持续时间并使计划缓存溢出，从而导致系统从缓存中逐出计划。 可以通过对数据库设置强制参数化选项来更改 SQL Server 的此行为。 

为了帮助你估计此建议的影响，将提供实际 CPU 使用率和预计 CPU 使用率（就像已应用建议一样）之间的比较。 除了节省 CPU 外，查询持续时间会因编译花费的时间而减少。 计划缓存上的开销也会更低，从而允许大部分计划保留在缓存中并可重复使用。 可以通过单击“应用”命令来快速轻松地应用此建议。 

应用此建议后，它会在几分钟之内对数据库启用强制参数化，并将启动监视进程（大约持续 24 小时）。 经过这段时间后，即可看到验证报告，该报告显示应用此建议之前和之后的 24 小时内数据库的 CPU 使用率。 SQL 数据库顾问具有安全机制，在检测到性能衰退的情况下，可以自动还原已应用的建议。

## <a name="fix-schema-issues-recommendations"></a>修复架构问题建议
当 SQL 数据库服务发现 Azure SQL 数据库上架构相关 SQL 错误的数量发生异常时，就会出现**修复架构问题**建议。 此建议通常在数据库在一个小时内遭遇到多个架构相关的错误（无效的列名称、无效的对象名称等）时出现。

“架构问题”是 SQL Server 中的一类语法错误，于 SQL 查询的定义与数据库架构的定义不一致时发生。 例如，目标表中可能缺少查询所需的某个列，反之亦然。 

当 Azure SQL 数据库服务发现 Azure SQL 数据库上架构相关 SQL 错误的数量发生异常时，就会出现“修复架构问题”建议。 下表显示与架构问题相关的错误：

| SQL 错误代码 | 消息 |
| --- | --- |
| 201 |过程或函数“*”需要参数“*”，但未提供该参数。 |
| 207 |列名称“*”无效。 |
| 208 |对象名“*”无效。 |
| 213 |列名或所提供值的数目与表定义不匹配。 |
| 2812 |找不到存储过程“*”。 |
| 8144 |为过程或函数 * 指定了过多的参数。 |

## <a name="next-steps"></a>后续步骤
监视建议并继续应用它们以优化性能。 数据库工作负荷是动态的，并且不断地更改。 SQL 数据库顾问将继续监视和提供可能提高数据库性能的建议。 

* 请参阅 [Azure SQL 数据库自动优化](sql-database-automatic-tuning.md)，了解数据库索引和查询执行计划的自动优化。
* 请参阅 [Azure SQL 智能见解](sql-database-intelligent-insights.md)，了解借助自动诊断和性能问题的根本原因分析自动监视数据库性能。
* 请参阅 [Azure 门户中的性能建议](sql-database-advisor-portal.md)，了解如何在 Azure 门户中使用性能建议。
* 若要了解和查看排名靠前的查询的性能影响，请参阅[查询性能见解](sql-database-query-performance.md)。



