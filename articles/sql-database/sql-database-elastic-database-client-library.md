---
title: "构建可缩放的云数据库 | Microsoft 文档"
description: "使用弹性数据库客户端库构建可缩放的 .NET 数据库应用"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 1f11c52d-13c1-4994-b9b1-5b1ae2f9255f
ms.service: sql-database
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 41be0faae6e54a7772d97b75923647283c6a7f22


---
# <a name="building-scalable-cloud-databases"></a>构建可缩放的云数据库
使用 Azure SQL 数据库的可缩放工具和功能，可以轻松扩大数据库。 特别是，你可以使用**弹性数据库客户端库**来创建和管理扩大的数据库。 此功能可让你使用成百上千个 Azure SQL 数据库，轻松地开发分片应用程序。 然后，可以使用[弹性作业](sql-database-elastic-jobs-powershell.md)帮助简化这些数据库的管理。

若要安装该库，请转到 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。 

## <a name="documentation"></a>文档
1. [弹性数据库工具入门](sql-database-elastic-scale-get-started.md)
2. [弹性数据库功能](sql-database-elastic-scale-introduction.md)
3. [分片映射管理](sql-database-elastic-scale-shard-map-management.md)
4. [迁移要扩大的现有数据库](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [数据依赖型路由](sql-database-elastic-scale-data-dependent-routing.md)
6. [多分片查询](sql-database-elastic-scale-multishard-querying.md)
7. [使用弹性数据库工具添加分片](sql-database-elastic-scale-add-a-shard.md)
8. [具有弹性数据库工具和行级安全性的多租户应用程序](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [升级客户端库应用](sql-database-elastic-scale-upgrade-client-library.md) 
10. [弹性查询概述](sql-database-elastic-query-overview.md)
11. [弹性数据库工具术语表](sql-database-elastic-scale-glossary.md)
12. [将弹性数据库客户端库与实体框架配合使用](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [弹性数据库客户端库与 Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [拆分/合并工具](sql-database-elastic-scale-overview-split-and-merge.md)
15. [分片映射管理器的性能计数器](sql-database-elastic-database-client-library.md) 
16. [弹性数据库工具常见问题](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>客户端功能
无论对于开发人员还是管理员，使用*分片*扩大应用程序都存在挑战。 客户端库通过提供工具让开发人员和管理员管理扩大的数据库，简化了管理任务。 在典型的示例中，有许多称为“分片”的数据库要管理。 客户归置于同一数据库，并且每个客户（单租户方案）一个数据库。 客户端库包含下列功能：

1. **分片映射管理**：创建一个称为“分片映射管理器”的特殊数据库。 分片映射管理是一种使应用程序能够管理其分片相关元数据的功能。 开发人员可使用此功能将数据库注册为分片（描述各个分片键或键范围到这些数据库的映射），并随着数据库的数量和组成发展来维护此元数据，以反映容量更改。 如果不使用弹性数据库客户端库，实现分片时你必须花费大量时间来编写管理代码。 有关详细信息，请参阅[分片映射管理](sql-database-elastic-scale-shard-map-management.md)。

* **数据依赖型路由**：假设将一个请求传入应用程序。 基于请求的分片键值，应用程序必须根据该键值判断正确的数据库。 接着，它会与数据库建立连接来处理请求。 借助数据依赖型路由，您能够通过对应用程序的分片映射的单个简单调用打开连接。 数据依赖型路由是基础结构代码的另一个区域，现在它由弹性数据库客户端库中的功能所取代。 有关详细信息，请参阅[数据依赖型路由](sql-database-elastic-scale-data-dependent-routing.md)。
* **多分片查询 (MSQ)**：当一个请求涉及多个（或所有）分片时，多分片查询将生效。 多分片查询在所有分片或一组分片上执行相同的 T-SQL 代码。 使用 UNION ALL 语义，将参与分片中的结果合并到一个总结果集中。 该功能通过该客户端库处理多个任务公开，其中包括连接管理、线程管理、故障处理和中间结果处理。 MSQ 最多可以查询数百个分片。 有关详细信息，请参阅[多分片查询](sql-database-elastic-scale-multishard-querying.md)。

一般而言，当使用弹性数据库工具的客户提交具有其自己的语义的局部分片操作（与跨分片操作相对）时，预期可获取完整的 T-SQL 功能。

## <a name="next-steps"></a>后续步骤
尝试运行用于演示客户端功能的[示例应用](sql-database-elastic-scale-get-started.md)。 

若要安装该库，请转到[弹性数据库客户端库](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。

有关使用拆分/合并工具的说明，请参阅[拆分/合并工具概述](sql-database-elastic-scale-overview-split-and-merge.md)。

[弹性数据库客户端库的源代码现已发布！](https://azure.microsoft.com/blog/elastic-database-client-library-is-now-open-sourced/)

使用[弹性查询](sql-database-elastic-query-overview.md)。

在 [GitHub](https://github.com/Azure/elastic-db-tools) 上，以开源软件的形式提供该库。 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-database-client-library/glossary.png




<!--HONumber=Nov16_HO3-->


