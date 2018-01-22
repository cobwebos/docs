---
title: "Azure SQL 数据仓库速查表 | Microsoft Docs"
description: "查找链接和最佳做法，以便快速生成 Azure SQL 数据仓库解决方案。"
services: sql-data-warehouse
documentationcenter: NA
author: acomet
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 12/14/2017
ms.author: acomet
ms.openlocfilehash: 2d17385ff255ddf7b85baa81600a2af60d015540
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Azure SQL 数据仓库速查表
此页面应当有助于设计数据仓库解决方案的主要用例。 在用户生成世界一流数据仓库的道路上，此速查表应当能提供强有力的支持，但我们仍极力建议去了解每个步骤的详细信息。 首先，建议阅读这篇有关 SQL 数据仓库**[真实面目]**的上乘之作。

下面是开始设计 SQL 数据仓库时应遵循的流程草图。

![草图]

## <a name="queries-and-operations-across-tables"></a>跨表的查询和操作

事先了解数据仓库中发生的最重要的操作和查询真的很重要。 数据仓库体系结构应优先这些操作。 常见操作示例包括：
* 将一个或两个事实数据表与维度表联接，基于某个时间段筛选此表并将结果追加到数据市场中
* 对事实销售进行较大或较小的更新
* 仅将数据追加到表

知道操作类型有助于优化表设计。

## <a name="data-migration"></a>数据迁移

建议先将数据加载**[到 ADLS]** 或 Azure Blob 存储。 然后，应使用 Polybase 将数据加载到 SQL 数据仓库的临时表中。 建议使用以下配置：

| 设计 | 建议 |
|:--- |:--- |
| 分发 | 轮循机制 |
| 索引 | 堆 |
| 分区 | 无 |
| 资源类 | largerc 或 xlargerc |

详细了解**[数据迁移]、[数据加载]**以及**有关加载的[更深入的指南]**。 

## <a name="distributed-or-replicated-tables"></a>分布式表或复制表

建议根据表属性采用以下策略：

| Type | 非常适合 | 谨慎使用|
|:--- |:--- |:--- |
| 复制 | • 星型架构中压缩后（约 5 倍压缩率）存储小于 2 GB 的小型维度表 |• 表上有大量写入事务（例如：插入、upsert、删除、更新）<br></br>• 经常更改数据仓库单位 (DWU) 预配<br></br>• 仅使用 2-3 列，而表有许多列<br></br>• 对复制表编制索引 |
| 轮循机制（默认值） | • 临时表<br></br> • 没有明显的联接键或合适的候选列 |• 因数据移动而性能下降 |
| 哈希 | • 事实数据表<br></br>• 大型维度表 |• 不能更新分布键 |

**提示：**
* 先使用轮循机制，但以哈希分布策略为最终目标，以便充分利用大规模并行体系结构
* 确保常见的哈希键具有相同的数据格式
* 请勿分布 varchar 格式
* 可以将具有常见哈希键的维度表哈希分布到具有频繁联接操作的事实数据表
* 使用 *[sys.dm_pdw_nodes_db_partition_stats]* 分析数据中的任何偏斜
* 使用 *[sys.dm_pdw_request_steps]* 分析查询背后的数据移动、监视时间广播以及随机选择操作需要。 有助于查看分布策略。

深入了解**[复制表]和[分布式表]**。

## <a name="indexing-your-table"></a>对表编制索引

编制索引**有助于**快速读取表。 下面列出了一组独有的技术，用户可根据需要使用：

| Type | 非常适合 | 谨慎使用|
|:--- |:--- |:--- |
| 堆 | • 临时表<br></br>• 包含小规模查找的小型表 |• 任何查找扫描全表 |
| 聚集索引 | • 最多包含 1 亿行的表<br></br>• 仅 1-2 列被频繁使用的大型表（超过 1 亿行） |• 用于复制表<br></br>• 涉及多个联接、分组操作的复杂查询<br></br>• 对索引列进行更新，它会占用内存 |
| 聚集列存储索引 (CCI)（默认值） | • 大型表（超过 1 亿行） | • 用于复制表<br></br>• 对表进行大规模更新操作<br></br>• 对表过度分区：行组未横跨不同的分布节点和分区 |

**提示：**
* 除了聚集索引，可能还需要向经常用于筛选的列添加非聚集索引。 
* 注意如何使用 CCI 管理表上的内存。 加载数据时，你希望用户（或查询）受益于大型资源类。 确保避免剪裁和创建许多经过压缩的小型行组
* 已使用 CCI 针对计算层障碍进行优化
* 对于 CCI，可能因行组压缩不当而出现性能下降的情况，此时你可能需要重新生成或重新整理 CCI。 你希望每个压缩后的行组包含至少 10 万行。 理想状态为一个行组 100 万行。
* 基于增量加载频率和大小，你想自动执行索引的重新整理或重新生成操作。 彻底清理操作始终有用。
* 想要剪裁行组时应更具战略性：打开的行组有多大？ 未来几天希望加载多少数据？

深入了解**[索引]**。

## <a name="partitioning"></a>分区
如果拥有一个大型事实数据表（超过 10 亿行的表），可能需要对表分区。 在 99% 的情况下，分区键应基于日期。 注意不要过度分区，尤其是在具有聚集列存储索引时。
对于需要 ETL 的临时表，可从分区中受益。 它使数据生命周期管理更便捷。
注意不要对数据过度分区，尤其是对聚集列存储索引。

深入了解**[分区]**。

## <a name="incremental-load"></a>增量加载

首先，应确保分配更大的资源类来加载数据。 建议使用 Polybase 和 ADF V2 在 SQL DW 中自动执行 ETL 管道。

对于历史数据中的大批量更新，建议先删除相关数据。 然后可以批量插入新数据。 这种双步骤方法更高效。

## <a name="maintain-statistics"></a>维护统计信息
自动统计信息功能即将正式推出。 在此之前，SQL 数据仓库需要手动维护统计信息。 对数据做**重大**更改时务必更新统计信息。 这有助于优化查询计划。 如果发现维护所有统计信息所需时间太长，可能要更谨慎地选择包含统计信息的列。 还可以定义更新频率。 例如，可能想要更新每天都要添加新值的日期列。 对涉及联接的列、WHERE 子句中使用的列、在 GROUP BY 中找到的列进行信息统计，可以获得最大效益。

深入了解**[统计信息]**。

## <a name="resource-class"></a>资源类
SQL 数据仓库使用资源组作为将内存分配给查询的一种方式。 如果需要更多内存来提高查询或加载速度，应分配更大的资源类。 但另一方面，使用更大的资源类会影响并发。 在将所有用户移动到大型资源类之前，你希望把这点纳入考虑范围。

如果发现查询所需时间过长，请确保用户未在大型资源类中运行。 大型资源类会占用许多并发槽。 它们可能导致其他查询排队等待。

最后，相比弹性优化层，使用计算优化层时每个资源类获得的内存要多 2.5 倍。

深入了解如何使用**[资源类和并发]**。

## <a name="lower-your-cost"></a>降低成本
SQL 数据仓库的一个重要功能，是能够在不使用它时予以暂停，这会停止计算资源的计费。 另一个重要功能是能够缩放资源。 暂停和缩放可以通过 Azure 门户或 PowerShell 命令完成。

立即使用 Azure Functions 根据需要进行自动缩放：

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-you-architecture-for-performance"></a>优化体系结构以提高性能

建议考虑在中心辐射型体系结构中使用 SQL 数据库和 Azure Analysis Services。 该解决方案可以在不同的用户组之间提供工作负荷隔离，同时还能利用 SLQ DB 和 Azure Analysis Services 的一些高级安全功能。 这也是一种向用户提供无限并发的方式。

深入了解**[利用 SQL DW 的典型体系结构]**。

单击即可在 SQL DW 的 SQL DB 数据库中部署辐射型体系结构：

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[草图]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[数据加载]:./design-elt-data-loading.md
[更深入的指南]: ./guidance-for-loading-data.md
[索引]:./sql-data-warehouse-tables-index.md
[分区]:./sql-data-warehouse-tables-partition.md
[统计信息]:./sql-data-warehouse-tables-statistics.md
[资源类和并发]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[利用 SQL DW 的典型体系结构]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[真实面目]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[数据迁移]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[复制表]:https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[分布式表]:https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[到 ADLS]: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
