---
title: "结合使用弹性查询概念和 Azure SQL 数据仓库 | Microsoft Docs"
description: "结合使用弹性查询概念和 Azure SQL 数据仓库"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 09/18/2017
ms.author: elbutter
ms.openlocfilehash: 4c351d88b31adfa3443dd2231f67bb442f2b8fe0
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2017
---
# <a name="how-to-use-elastic-query-with-sql-data-warehouse"></a>如何结合使用弹性查询和 SQL 数据仓库



结合使用弹性查询和 Azure SQL 数据仓库，可以在 SQL 数据库中编写 Transact-SQL，通过外部表将它远程发送到 Azure SQL 数据仓库实例。 使用此功能，不仅可以节约成本，还能构建更多高效益体系结构，具体视方案而定。

此功能支持两个主要方案：

1. 域隔离
2. 远程执行查询

### <a name="domain-isolation"></a>域隔离

域隔离是指经典数据市场方案。 在某些情况下，不妨向与数据仓库的其余部分隔离的下游用户提供数据的逻辑域。具体原因有很多，包括但不限于：

1. 资源隔离 - SQL 数据库更适合为大量并发用户提供服务，所提供的工作负载与数据仓库为大型分析查询保留的工作负载有点不同。 隔离可确保由合适的工具提供正确的工作负载。
2. 安全隔离 - 通过特定架构有选择性地分开已授权数据子集。
3. 沙盒 - 提供一组样本数据作为“演练”，用于探索生产查询等。

使用弹性查询，可以轻松选择 SQL 数据仓库数据子集，并将它移到 SQL 数据库实例中。 此外，这种隔离并不妨碍同时启用远程执行查询，这样就形成了更有趣的“缓存”方案。

### <a name="remote-query-execution"></a>远程执行查询

使用弹性查询，可以对 SQL 数据仓库实例远程执行查询。 通过在两个数据库之间将热数据和冷数据分隔开来，可以充分利用 SQL 数据库和 SQL 数据仓库。 用户可以在 SQL 数据库中保留更多最新数据，这些数据可用于生成报告，并供大量普通业务用户参考。 不过，如果需要更多数据或计算，用户可以将部分查询卸载到 SQL 数据仓库实例中，这样可以更快、更高效地处理大规模聚合。



## <a name="elastic-query-overview"></a>弹性查询概述

使用弹性查询，能够让 SQL 数据仓库中的数据可供 SQL 数据库实例使用。 借助弹性查询，SQL 数据库中的查询可以引用远程 SQL 数据仓库实例中的表。 

第一步是创建外部数据源定义，用于引用 SQL 数据仓库实例，这需要使用 SQL 数据仓库中的现有用户凭据。 在远程 SQL 数据仓库实例中，不需要进行任何更改。 

> [!IMPORTANT] 
> 
> 必须拥有 ALTER ANY EXTERNAL DATA SOURCE 权限。 此权限包含在 ALTER DATABASE 权限中。 必须拥有 ALTER ANY EXTERNAL DATA SOURCE 权限，才能引用远程数据源。

接下来，在 SQL 数据库实例中创建远程外部表定义，指向 SQL 数据仓库中的远程表。 如果查询使用外部表，引用外部表的查询部分会发送到 SQL 数据仓库实例进行处理。 查询完成后，结果集便会发送回调用方 SQL 数据库实例。 有关如何在 SQL 数据库和 SQL 数据仓库之间设置弹性查询的简易教程，请参阅[配置结合使用弹性查询和 SQL 数据仓库][Configure Elastic Query with SQL Data Warehouse]。

若要详细了解如何结合使用弹性查询和 SQL 数据库，请参阅 [Azure SQL 数据库弹性查询概述][Azure SQL Database elastic query overview]。



## <a name="best-practices"></a>最佳实践

### <a name="general"></a>常规

- 启用远程执行查询时，务必仅选择必要的列，并应用正确的筛选器。 否则，不仅会增加必要计算，还会增加结果集的大小，进而增加需要在两个实例之间移动的数据量。
- 在 SQL 数据仓库和 SQL 数据库中，保持聚集列存储中的数据可用于分析目的，以提升分析性能。
- 务必对源表进行分区，以便于查询和移动数据。
- 务必对用作缓存的 SQL 数据库实例进行分区，以便细化更新和简化管理。 
- 理想情况下，使用 PremiumRS 数据库，因为此类数据库是高级数据库的缩减版，可以提供聚集列存储索引的分析优势，并将重心放在 IO 密集型工作负载上。
- 加载后，在 SQL 数据库实例中使用负载或 upsert 的有效日期标识列，维护缓存源的完整性。 
- 在 SQL 数据仓库实例中创建单独的登录用户凭据，用作在外部数据源中定义的 SQL 数据库远程登录凭据。 

### <a name="elastic-querying"></a>弹性查询

- 在许多情况下，可能需要管理一种延伸表，此表中的一部分作为缓存数据存在于 SQL 数据库中供使用，剩余的数据存储在 SQL 数据仓库中。 SQL 数据库中需要有两个对象：SQL 数据库内引用 SQL 数据仓库中基表的外部表，SQL 数据库中表的“缓存”部分。 考虑在表格缓存部分和外部表基础上创建视图，该操作会联合这两个表，并应用筛选器，用于分离在通过外部表公开的 SQL 数据库和 SQL 数据仓库中具体化的数据。

  假设要在 SQL 数据库实例中保留最近一年的数据。 有两个表，分别为引用数据仓库订单表的 ext.Orders，和表示 SQL 数据库实例中最近几年数据的 dbo.Orders。 我们根据两个表在最近一年的分区点上创建视图，而不是让用户决定是否查询哪个表。

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  通过这样生成的视图，查询编译器可以确定是否需要使用数据仓库实例来响应用户查询。 

  对数据仓库实例运行的每个弹性查询都有相关的提交、编译、运行和数据移动开销。 请注意，每个弹性查询都会计入并发槽计数，并且都会使用资源。  


- 如果用户打算进一步向下钻取到数据仓库实例中的结果集，不妨在 SQL 数据库的临时表中具体化，从而提升性能，并防止不必要的资源使用。

### <a name="moving-data"></a>数据移动 

- 尽可能使用仅限追加的源表来简化数据管理，这样可以在数据仓库和数据库实例之间轻松维护更新。
- 在分区一级使用刷新和填充语义移动数据，可以最大限度地降低数据仓库级查询成本，以及不断更新数据库实例所需移动的数据量。 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>何时选择 Azure Analysis Services 与 SQL 数据库

#### <a name="azure-analysis-services"></a>Azure Analysis Services

- 打算将缓存与提交许多小型查询的 BI 工具结合使用
- 需要次秒级查询延迟
- 在管理/开发 Analysis Services 模型方面拥有丰富的经验 

#### <a name="sql-database"></a>SQL 数据库

- 希望使用 SQL 查询缓存数据
- 需要远程执行特定查询
- 有更大的缓存需求



## <a name="faq"></a>常见问题

问：能否将弹性池内的数据库与弹性查询结合使用？

答：是的。 弹性池中的 SQL 数据库可以使用弹性查询。 

问：可以对弹性查询使用的数据库数量是否有上限？

答：弹性查询可使用的数据库数量没有硬上限。 但是，每个弹性查询（命中 SQL 数据仓库的查询）都会计入常规并发限制。

问：弹性查询是否有 DTU 限制？

答：弹性查询的 DTU 限制和一般查询相同。 标准策略是逻辑服务器有 DTU 限制，以防客户意外超支。 若要为多个数据库和 SQL 数据仓库实例启用弹性查询，可能会意外达到上限。 如果发生这种情况，请提交增加逻辑服务器的 DTU 限制的请求。 可增加配额，具体方法为[创建支持票证](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket)，并选择“配额”作为请求类型

问：能否将行级别安全性/动态数据掩码与弹性查询结合使用？

答：如果客户希望在 SQL 数据库中使用更多高级安全功能，为此可以先在 SQL 数据库中移动和存储数据。 暂无法对通过外部表查询的数据应用行级别安全性或 DDM。 

问：能否从 SQL 数据库实例写入数据仓库实例？

答：暂不支持此功能。 如果希望今后可以使用此功能，请访问我们的[反馈页][Feedback page]，创建/投票支持此功能。 

问：能否使用几何/地理等空间类型？

答：可以在 SQL 数据仓库中将空间类型存储为 varbinary(max) 值。 使用弹性查询查询这些列时，可以在运行时将它们转换为相应的类型。

![空间类型](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)





<!--Image references-->

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop/
[Configure Elastic Query with SQL Data Warehouse]: ./tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md

<!--MSDN references-->

<!--Other Web references-->
