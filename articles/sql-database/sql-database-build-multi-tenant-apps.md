<properties
   pageTitle="使用具有隔离和效率的 Azure SQL 数据库构建多租户应用"
   description="了解 SQL 数据库如何构建多租户应用"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.date="05/04/2016"
   wacn.date="05/23/2016"/>

# 使用具有隔离和效率的 Azure SQL 数据库构建多租户应用

## 利用弹性池并构建更高效的多租户应用

如果你是 SaaS 开发人员并正在编写多租户应用和管理大量客户，那么你通常需要权衡客户性能、管理和安全性。使用 Azure SQL 数据库弹性数据库池，你就不再需要做出这种让步。这些池可帮助你管理和监视多租户应用，并获得在每个数据库中隔离一名客户的益处。

![构建多租户应用](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## 自动缩放控件

池会自动为弹性数据库动态缩放性能和存储容量。你可以控制分配给池的性能，按需添加或删除弹性数据库，以及定义弹性数据库的性能，而不会影响池的总成本。这意味着你不必担心如何管理单个数据库的使用情况。

[阅读文档](/documentation/articles/sql-database-elastic-pool)

## 对环境的智能管理

内置的尺寸调整建议会主动标识出将受益于池的数据库。这些建议允许进行假设情况分析以获得快速优化，从而满足你的性能目标。丰富的性能监视和故障排除仪表板可帮助让历史池使用情况可视化。

[阅读文档](/documentation/articles/sql-database-elastic-pool-guidance)

## 符合你需求的性能和价格

基本、标准和高级池为你提供范围广泛的性能、存储空间和定价选择。池最多可包含 400 个弹性数据库。弹性数据库可自动增加到 1000 个弹性数据库事务单位 (eDTU)。

[阅读文档](/home/features/sql-database/#price/?b=16.50)

## 弹性工具

除了弹性池，还提供可帮助跨多个数据库管理运营活动的 SQL 数据库功能：

**执行跨数据库查询和报告。**  
[弹性数据库查询](/documentation/articles/sql-database-elastic-query-overview)使你能够跨弹性池中的数据库运行查询或报表，并立即访问存储在池中多个数据库中的远程数据。

**跨数据库事务运行。**  
[弹性数据库事务](/documentation/articles/sql-database-elastic-transactions-overview)允许你跨 SQL 数据库中多个数据库运行事务并执行操作（即跨数据库处理财务事务时，或更新一个数据库中的库存或订单时）。

**在多个数据库上执行相同操作。**  
[弹性数据库作业](/documentation/articles/sql-database-elastic-jobs-overview)执行管理性操作，如跨弹性池中的每个数据库重新生成索引或更新架构。

转到主页以查看 SQL 数据库可提供的其他功能。
[请查看](/home/features/sql-database)

## 后续步骤

获取 [Azure 订阅](/pricing/1rmb-trial)，并[创建你的第一个 Azure SQL 数据库](/documentation/articles/sql-database-get-started)。

## 了解详细信息

浏览所有 [SQL 数据库的功能](/home/features/sql-database)。
 
查看 [SQL 数据库技术概述](/documentation/articles/sql-database-technical-overview)。

<!---HONumber=Mooncake_0530_2016-->