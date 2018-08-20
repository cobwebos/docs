---
title: Azure SQL 数据仓库发行说明（2018 年 7 月）| Microsoft Docs
description: Azure SQL 数据仓库发行说明。
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: ce1e33a2888b2d0798fb0fe6df87476001e6f7b2
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630256"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Azure SQL 数据仓库中的新增功能 2018 年 7 月
Azure SQL 数据仓库持续得到改进。 本文介绍了 2018 年 7 月发行的版本中所引入的新功能和所做的更改。

## <a name="lightning-fast-query-performance"></a>飞快的查询性能
随着可改进无序操作性能的即时数据访问的推出，[Azure SQL 数据仓库](https://aka.ms/sqldw)制定了新的性能基准。 即时数据访问使用直接的 SQL Server 到 SQL Server 本机数据操作来降低数据移动操作的开销。 为实现数据移动而与 SQL Server 引擎直接集成，意味着 SQL 数据仓库的速度**比 Amazon Redshift 快 67%**，因为它使用派生自公认行业标准 [TPC Benchmark™ H (TPC-H)](http://www.tpc.org/tpch/) 的工作负荷。

![Azure SQL 数据仓库比 Amazon Redshift 更快、更便宜](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>资料来源：[Gigaom Research Analyst Report: Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>（Gigaom 调查分析师报告：符合云基准的数据仓库）

除运行时性能以外，[Gigaom 调查](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)报告还在评估特定工作负荷的美元成本时测量了性价比。 处理 30TB 的工作负荷时，SQL 数据仓库的成本比 Redshift **至少降低了 23%**。 SQL 数据仓库能够弹性缩放计算资源，并且还能暂停和恢复工作负荷；只有服务处于使用中状态时，客户才需付费，因此进一步降低了成本。
![Azure SQL 数据仓库比 Amazon Redshift 更快、更便宜](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>资料来源：[Gigaom Research Analyst Report: Data Warehouse in the Cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>（Gigaom 调查分析师报告：符合云基准的数据仓库）

###<a name="query-concurrency"></a>查询并发性
SQL 数据仓库还确保可跨组织访问数据。 Microsoft 增强了该服务以支持 128 个并发查询，使更多的用户可以查询同一个数据库，而不会受到其他请求的阻止。 相比之下，Amazon Redshift 将最大并发查询数限制为 50 个，因此限制了组织内部的数据访问。

SQL 数据仓库在不提高价格的情况下提供这些查询性能和查询并发性改善，其独特的体系结构建立在分离式存储和计算资源的基础之上。

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>实现跨区域和服务器还原的更精细粒度
现在可以使用任何还原点跨区域和服务器进行还原，而不是选择每 24 小时执行一次的异地冗余备份。 用户定义或自动还原点支持跨区域和服务器还原，从而实现以更精细的粒度进行其他数据保护。 由于有更多还原点可用，你可以确保在跨区域还原时，数据仓库在逻辑上是一致的。

![还原命令 - Azure SQL 数据仓库](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![还原选项 - Azure SQL 数据仓库](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

有关详细信息，请参阅[加速且灵活的还原点](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)博客文章。

## <a name="20-minute-restorations"></a>20 分钟还原
SQL 数据仓库现在可以用**不到 20 分钟**时间在同一区域内还原任何数据仓库，而不管数据库大小如何。 无论还原是在同一区域内的相同还是不同逻辑服务器上进行，此还原时间都适用。 此外，已对快照过程进行改进，以减少创建还原点所需的时间。 在较低性能级别（较低 DWU 设置）中，改进是将快照创建时间*缩短 2 倍*。

有关详细信息，请参阅[加速且灵活的还原点](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)博客文章。

## <a name="custom-restoration-configurations"></a>自定义还原配置
在 Azure 门户中还原时，现在可以更改性能级别 (DWU)。 还可以还原到升级后的第 2 代数据仓库。 通过还原到第 2 代实例，现在可以在[升级第 1 代数据仓库](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation)之前评估第 2 代的影响。

![自定义还原配置 - Azure SQL 数据仓库](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
工具通常使用 [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) 存储过程来获取有关 Transact-SQL 批处理中的参数的元数据。 该过程为批处理中的每个参数返回一行，其中包含该参数的推断类型信息。 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

结果集包含有关 `@id` 参数的元数据（显示部分结果）
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
如果基础对象的更改导致基础元数据过时，[sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) 存储过程会更新数据库对象的元数据。 如果某个视图的基表发生更改，并且尚未重新创建该视图，则可能会发生此情况。 这样就免除了删除再重新创建依赖对象的步骤。

以下示例显示了一个由于基础表发生更改而过时的视图。 可以看到，第一列发生更改后，数据正确（1 到 Mollie），但列名无效，并且第二列不存在。 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

## <a name="next-steps"></a>后续步骤
对 SQL 数据仓库有了初步的认识后，请了解如何快速[创建 SQL 数据仓库][create a SQL Data Warehouse]。 如果不熟悉 Azure，在遇到新术语时，可以参考 [Azure 术语表][Azure glossary]。 或者，查看一下以下一些其他 SQL 数据仓库资源。  

* [客户成功案例]
* [博客]
* [功能请求]
* [视频]
* [客户顾问团队博客]
* [堆栈溢出论坛]
* [Twitter]


[博客]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客户顾问团队博客]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[客户成功案例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[功能请求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[堆栈溢出论坛]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[视频]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md