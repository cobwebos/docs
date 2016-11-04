---
title: SQL 数据库：什么是 DTU？| Microsoft Docs
description: 了解什么是 Azure SQL 数据库事务单元。
keywords: 数据库选项,数据库性能
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler

ms.service: sql-database
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/06/2016
ms.author: carlrab

---
# 介绍数据库事务单位 (DTU) 和弹性数据库事务单位 (eDTU)
本文介绍数据库事务单位 (DTU) 和弹性数据库事务单位 (eDTU)，以及达到最大 DTU 或 eDTU 时发生的情况。

## 数据库事务单位 (DTU) 的定义
DTU 是一个资源度量单位，表示保证可用于[独立数据库服务层](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels)内特定性能级别的独立 Azure SQL 数据库的资源。DTU 是一定比例的 CPU、内存和数据 I/O 以及事务日志 I/O 的混合度量值，该比例由 OLTP 基准工作负荷决定（OLTP 基准工作负荷代表真实的 OLTP 工作负荷）。提高数据库的性能级别可使 DTU 加倍，这等同于使该数据库可用的资源集增加一倍。例如，具有 1750 个 DTU 的高级 P11 数据库提供的 DTU 计算能力是具有 5 个 DTU 的基本数据库的 350 倍。要了解用于确定 DTU 混合比例的 OLTP 基准工作负荷所依据的方法，请参阅 [SQL 数据库基准概述](sql-database-benchmark-overview.md)。

![SQL 数据库简介：按层和级别统计的单一数据库 DTU](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

可以随时[更改服务层](sql-database-scale-up.md)，将应用程序故障时间降至最低（通常在平均 4 秒以下）。许多业务和应用只要能够创建数据库并按需调高或调低单一数据库的性能即可，尤其是当使用模式相对容易预测时。但如果有无法预测的使用模式，则管理成本和业务模式就会变得相当困难。在这种情况下，可以使用具有一定数量 eDTU 的弹性池。

## 弹性数据库事务单位 (eDTU) 的定义
eDTU 是一个资源集度量单位，表示可在 Azure SQL Server 中的一组数据库之间共享的资源集，称为[弹性池](sql-database-elastic-pool.png)。弹性池是一种简单的低成本高效益的解决方案，用于管理使用模式变化很大且不可预测的多个数据库的性能目标。有关详细信息，请参阅[弹性池和服务层](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)。

![SQL 数据库简介：按层和级别统计的 eDTU](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

对池提供了固定数量的 eDTU，以获得固定价格。在池中，单独的数据库都被赋予了在固定参数内自动缩放的灵活性。高负荷下的数据库可能会消耗更多的 eDTU 以满足需求。低负荷下的数据库消耗较少的 eDTU，没有任何负荷的数据库不会消耗任何 eDTU。设置整个池（而非单个数据库）的资源简化了管理任务。此外，必须具有该池的可预测预算。

可以对现有池增加额外的 eDTU，这不会造成数据库故障，也不会影响弹性池中的数据库。同样，你随时可以从现有池中删除不再需要的额外 eDTU。可以增加或减少池的数据库。如果可以预测到数据库的资源利用率不足，则将其移出。

## 如何确定工作负荷所需的 DTU 数？
如果打算将现有的本地或 SQL Server 虚拟机工作负荷迁移到 Azure SQL 数据库，可以使用 [DTU 计算器](http://dtucalculator.azurewebsites.net/)来估计所需的 DTU 数。对于现有的 Azure SQL 数据库工作负荷，可以使用 [SQL 数据库 Query Performance Insight](sql-database-query-performance.md) 来了解数据库资源使用量 (DTU)，更深入地了解如何优化工作负荷。还可以使用 [sys.dm\_db\_ resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV 来获取过去一小时的资源消耗信息。或者，也可以查询目录视图 [sys.resource\_stats](http://msdn.microsoft.com/library/dn269979.aspx)，获取过去 14 天的相同数据（尽管是以平均五分钟的较低保真度）。

## 如何知道是否可以从资源的弹性池受益？
池适合具有特定使用模式的大量数据库。对于给定的数据库，此模式的特征是低平均使用量与相对不频繁的使用高峰。SQL数据库自动评估现有 SQL 数据库服务器中数据库的历史资源使用率，并在 Azure 门户中推荐适当的池配置。有关详细信息，请参阅[何时使用弹性数据库池？](sql-database-elastic-pool-guidance.md)

## 达到最大 DTU 时发生的情况
会校准并控制性能级别，以便在选定服务层/性能级别所允许的最大限制范围内提供所需的资源来运行数据库工作负荷。如果工作负荷达到了 CPU/数据 IO/日志 IO 限制中的其中一个限制，会继续接收资源直到达到最大允许级别，但是，可能会发现查询延迟不断增加。这些限制不会导致任何错误，而只会减慢工作负荷，直到严重变慢，以致于查询开始超时。如果达到了并发用户会话/请求（工作线程）的最大允许数目限制，会看到明确的错误。有关 CPU、内存、数据 I/O 和事务日志 I/O 以外的资源的限制信息，请参阅 [Azure SQL 数据库资源限制](sql-database-resource-limits.md)。

## 后续步骤
* 有关独立数据库和弹性池可用的 DTU 和 eDTU 的信息，请参阅[服务层](sql-database-service-tiers.md)。
* 有关 CPU、内存、数据 I/O 和事务日志 I/O 以外的资源的限制信息，请参阅 [Azure SQL 数据库资源限制](sql-database-resource-limits.md)。
* 请参阅 [SQL 数据库 Query Performance Insight](sql-database-query-performance.md) 以了解 (DTU) 使用情况。
* 要了解用于确定 DTU 混合比例的 OLTP 基准工作负荷所依据的方法，请参阅 [SQL 数据库基准概述](sql-database-benchmark-overview.md)。

<!---HONumber=AcomDC_0921_2016-->