---
title: "同步数据（预览版）| Microsoft Docs"
description: "本文概述了 Azure SQL 数据同步（预览版）。"
services: sql-database
documentationcenter: 
author: douglaslms
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: douglasl
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 075b5563688158289d51f2f0b5da4a3441ddd13a
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017


---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>使用 SQL 数据同步跨多个云和本地数据库同步数据

使用 SQL 数据同步这项基于 Azure SQL 数据库的服务，可以跨多个 SQL 数据库和 SQL Server 实例双向同步选定数据。

SQL 数据同步以同步组的概念为依据。 同步组是一组要同步的数据库。

同步组具有以下多个属性：

-   “同步架构”描述了在同步的数据。

-   “同步方向”可以是双向同步，也可以仅为单向同步。 也就是说，“同步方向”可以是“从中心同步到成员”和/或“从成员同步到中心”。

-   “同步间隔”是指多久执行一次同步。

-   “冲突解决策略”是组级别策略，可以是“中心胜出”，也可以是“成员胜出”。

SQL 数据同步使用中心辐射型拓扑来同步数据。 必须将组中的一个数据库定义为中心数据库。 其余数据库均为成员数据库。 仅在中心和各成员之间同步数据。
-   中心数据库必须是 Azure SQL 数据库。
-   成员数据库可以是 SQL 数据库、本地 SQL Server 数据库或 Azure 虚拟机上的 SQL Server 实例。
-   同步数据库包含数据同步的元数据和日志。 同步数据库必须是与中心数据库位于同一区域的 Azure SQL 数据库。 同步数据库的创建者和所有者均为客户。

> [!NOTE]
> 如果使用本地数据库，必须[配置本地代理](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-sql-data-sync)。

![在数据库之间同步数据](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>何时使用 SQL 数据同步

如果需要跨多个 Azure SQL 数据库或 SQL Server 数据库更新数据，SQL 数据同步就非常有用。 下面是 SQL 数据同步的主要用例：

-   **混合数据同步：**借助 SQL 数据同步，可以在本地数据库和 Azure SQL 数据库之间同步数据，以便启用数据层位于 SQL 中的混合应用程序。 此功能可能会吸引在考虑迁移到云中，并希望启用 Azure 应用程序的客户。

-   **分布式应用程序：**在许多情况下，跨各个数据库分散不同的工作负载会大有裨益。 例如，如果有大型生产数据库，但还需要对此数据运行报表或分析工作负载，那么使用第二个数据库来处理此额外工作负载将会有所帮助。 这种方法可最大限度地减轻对生产工作负载造成的性能影响。 可以使用 SQL 数据同步来同步这两个数据库。

-   **全局分布式应用程序：**许多企业的业务分布在多个区域，甚至是多个国家/地区。 为了最大限度地缩短网络延迟时间，最好将数据存储在靠近的区域中。 借助 SQL 数据同步，可轻松同步世界各地区域中的数据库。

不建议将 SQL 数据同步用于以下方案：

-   灾难恢复

-   读取缩放

-   ETL（OLTP 到 OLAP）

-   从本地 SQL Server 迁移到 Azure SQL 数据库

## <a name="how-does-data-sync-work"></a>SQL 数据同步的工作原理 

-   **跟踪数据更改：**SQL 数据同步使用插入、更新和删除触发器来跟踪更改。 更改记录在用户数据库中的端表内。

-   **同步数据：**根据设计，SQL 数据同步采用中心辐射型模型。 中心与各个成员同步数据。 中心内的更改会先下载到成员，然后成员内的更改会上传到中心。

-   **解决冲突：**SQL 数据同步提供两个冲突解决选项，即“中心胜出”或“成员胜出”。
    -   如果选择“中心胜出”，中心内的更改始终覆盖成员内的更改。
    -   如果选择“成员胜出”，成员内的更改覆盖中心内的更改。 如果有多个成员，最终值取决于哪个成员最先同步。

## <a name="limitations-and-considerations"></a>限制和注意事项

### <a name="performance-impact"></a>性能影响
SQL 数据同步使用插入、更新和删除触发器来跟踪更改。 它在用户数据库中创建端表。 这些活动会影响数据库工作负载。因此，请评估服务层，并根据需要进行升级。

### <a name="eventual-consistency"></a>最终一致性
由于 SQL 数据同步是基于触发器的服务，因此无法保证事务一致性。 Microsoft 保证最终执行所有更改，且 SQL 数据同步不会导致数据丢失。

### <a name="unsupported-data-types"></a>不支持的数据类型

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection（支持 XML）

-   Cursor、Timestamp、Hierarchyid

### <a name="requirements"></a>要求

-   每个表都必须有主键。

-   如果表包含标识列，则必须是主键。

-   数据库名称不能包含特殊字符。

### <a name="limitations-on-service-and-database-dimensions"></a>服务和数据库维度方面的限制

|                                                                 |                        |                             |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| **维度**                                                      | **限制**              | **解决方法**              |
| 任何数据库可属于的同步组的数量上限。       | 5                      |                             |
| 一个同步组中包含的终结点的数量上限              | 30                     | 创建多个同步组 |
| 一个同步组中包含的本地终结点的数量上限。 | 5                      | 创建多个同步组 |
| 数据库、表、架构和列名称                       | 每个名称 50 个字符 |                             |
| 同步组中的表                                          | 500                    | 创建多个同步组 |
| 同步组中的表列                              | 1000                   |                             |
| 表中的数据行大小                                        | 24MB                  |                             |
| 最小同步间隔                                           | 5 分钟              |                             |

## <a name="next-steps"></a>后续步骤

有关 SQL 数据库和 SQL 数据同步的详细信息，请参阅：

-   [SQL 数据同步入门](sql-database-get-started-sql-data-sync.md)

-   [下载完整的 SQL 数据同步技术文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)

-   [下载 SQL 数据同步 REST API 文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

-   [SQL 数据库概述](sql-database-technical-overview.md)

-   [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)



