---
title: Azure SQL 数据同步（预览版）| Microsoft 文档
description: 本文概述了 Azure SQL 数据同步（预览版）
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: data-sync
ms.topic: article
ms.date: 04/01/2018
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: e66adb8b0485e30fded487e18af6b2030f9c7f5b
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync-preview"></a>使用 SQL 数据同步（预览版）跨多个云和本地数据库同步数据

使用 SQL 数据同步这项基于 Azure SQL 数据库的服务，可以跨多个 SQL 数据库和 SQL Server 实例双向同步选定数据。

SQL 数据同步以同步组的概念为依据。 同步组是一组要同步的数据库。

同步组具有以下属性：

-   “同步架构”描述了在同步的数据。

-   “同步方向”可以是双向同步，也可以仅为单向同步。 也就是说，“同步方向”可以是“从中心同步到成员”和/或“从成员同步到中心”。

-   “同步间隔”是指多久执行一次同步。

-   “冲突解决策略”是组级别策略，可以是“中心胜出”，也可以是“成员胜出”。

SQL 数据同步使用中心辐射型拓扑来同步数据。 将组中的一个数据库定义为中心数据库。 其余数据库均为成员数据库。 仅在中心和各成员之间同步数据。
-   中心数据库必须是 Azure SQL 数据库。
-   成员数据库可以是 SQL 数据库、本地 SQL Server 数据库或 Azure 虚拟机上的 SQL Server 实例。
-   同步数据库包含数据同步的元数据和日志。同步数据库必须是与中心数据库位于同一区域的 Azure SQL 数据库。 同步数据库的创建者和所有者均为客户。

> [!NOTE]
> 如果使用本地数据库，必须[配置本地代理](sql-database-get-started-sql-data-sync.md#add-on-prem)。

![在数据库之间同步数据](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>何时使用 SQL 数据同步

如果需要跨多个 Azure SQL 数据库或 SQL Server 数据库更新数据，SQL 数据同步就非常有用。 下面是 SQL 数据同步的主要用例：

-   **混合数据同步：**借助 SQL 数据同步，可以在本地数据库和 Azure SQL 数据库之间同步数据，以便启用混合应用程序。 此功能可能会吸引在考虑迁移到云中，并希望启用 Azure 应用程序的客户。

-   **分布式应用程序：**在许多情况下，跨各个数据库分散不同的工作负载会大有裨益。 例如，如果有大型生产数据库，但还需要对此数据运行报表或分析工作负载，那么使用第二个数据库来处理此额外工作负载将会有所帮助。 这种方法可最大限度地减轻对生产工作负载造成的性能影响。 可以使用 SQL 数据同步来同步这两个数据库。

-   **全局分布式应用程序：**许多企业的业务分布在多个区域，甚至是多个国家/地区。 为了最大限度地缩短网络延迟时间，最好将数据存储在靠近的区域中。 借助 SQL 数据同步，可轻松同步世界各地区域中的数据库。

数据同步不适合以下方案：

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

## <a name="sync-req-lim"></a> 要求和限制

### <a name="general-considerations"></a>一般注意事项

#### <a name="eventual-consistency"></a>最终一致性
由于 SQL 数据同步是基于触发器的服务，因此无法保证事务一致性。 Microsoft 保证最终执行所有更改，且 SQL 数据同步不会导致数据丢失。

#### <a name="performance-impact"></a>性能影响
SQL 数据同步使用插入、更新和删除触发器来跟踪更改。 它在用户数据库中创建用于跟踪的端表。 这些更改跟踪活动会对数据库工作负荷产生影响。 评估服务层并根据需要升级。

### <a name="general-requirements"></a>一般要求

-   每个表都必须有主键。 请勿更改任何一行中的主键值。 如果必须更改主键值，请先删除行，再使用新的主键值重新创建此行。 

-   必须启用快照隔离。 有关详细信息，请参阅 [SQL Server 中的快照隔离](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server)。

### <a name="general-limitations"></a>一般限制

-   表不能包含非主键标识列。

-   主键不能有 datetime 数据类型。

-   对象（数据库、表和列）的名称不能包含可打印字符句点 (.)、左方括号 ([) 或右方括号 (])。

-   不支持 Azure Active Directory 身份验证。

#### <a name="unsupported-data-types"></a>不支持的数据类型

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection（支持 XML）

-   Cursor、Timestamp、Hierarchyid

#### <a name="limitations-on-service-and-database-dimensions"></a>服务和数据库维度方面的限制

| **维度**                                                      | **限制**              | **解决方法**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| 任何数据库可属于的同步组的数量上限。       | 5                      |                             |
| 一个同步组中包含的终结点的数量上限              | 30                     | 创建多个同步组 |
| 一个同步组中包含的本地终结点的数量上限。 | 5                      | 创建多个同步组 |
| 数据库、表、架构和列名称                       | 每个名称 50 个字符 |                             |
| 同步组中的表                                          | 500                    | 创建多个同步组 |
| 同步组中的表列                              | 1000                   |                             |
| 表中的数据行大小                                        | 24MB                  |                             |
| 最小同步间隔                                           | 5 分钟              |                             |
|||

## <a name="faq-about-sql-data-sync"></a>SQL 数据同步常见问题解答

### <a name="how-much-does-the-sql-data-sync-preview-service-cost"></a>SQL 数据同步（预览版）服务的价格是多少？

预览期间，不对 SQL 数据同步（预览版）服务本身收取任何费用。  但是，对于传入和传出 SQL 数据库实例的数据移动，仍然会产生数据传输费用。 有关价格，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

### <a name="what-regions-support-data-sync"></a>哪些区域支持数据同步？

SQL 数据同步（预览版）适用于所有公共云区域。

### <a name="is-a-sql-database-account-required"></a>是否需要 SQL 数据库帐户？ 

是的。 必须拥有 SQL 数据库帐户才能托管中心数据库。

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>数据同步是否可以用于只在 SQL Server 本地数据库之间同步？ 
无法直接配合使用。 但是，可以间接地在 SQL Server 本地数据库之间同步，方法是在 Azure 中创建一个中心数据库，然后将本地数据库添加到同步组。
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>是否可以将生产数据库中的数据种子植入到空数据库，然后使它们保持同步？ 
是的。 通过从原始数据库编写脚本，在新数据库中手动创建架构。 创建架构后，将表添加到同步组以复制数据并使其同步。

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>我应该使用 SQL 数据同步备份和还原数据库吗？

不建议使用 SQL 数据同步（预览版）创建数据备份。 由于 SQL 数据同步（预览版）的同步没有版本控制，因此无法备份和还原到特定时间点。 此外，SQL 数据同步（预览版）不会备份其他 SQL 对象（如存储过程），并且不会快速执行还原操作的等效操作。

对于推荐的备份技术，请参阅[复制 Azure SQL 数据库](sql-database-copy.md)。

### <a name="is-collation-supported-in-sql-data-sync"></a>SQL 数据同步是否支持排序规则？

是的。 SQL 数据同步在以下情况下支持排序规则：

-   如果所选同步架构表不在中心或成员数据库中，则部署同步组时，该服务会使用空目标数据库中选择的排序规则设置自动创建相应的表和列。

-   如果要同步的表已经存在于中心和成员数据库中，则 SQL 数据同步要求中心数据库和成员数据库之间的主键列具有相同的排序规则，以成功部署同步组。 主键列以外的列没有排序规则限制。

### <a name="is-federation-supported-in-sql-data-sync"></a>SQL 数据同步是否支持联合？

联合根数据库可用于 SQL 数据同步（预览版）服务，没有任何限制。 不能将联合数据库终结点添加到当前版本的 SQL 数据同步（预览版）。

## <a name="next-steps"></a>后续步骤

有关 SQL 数据同步的详细信息，请参阅：

-   [设置 Azure SQL 数据同步](sql-database-get-started-sql-data-sync.md)
-   [Azure SQL 数据同步最佳实践](sql-database-best-practices-data-sync.md)
-   [使用 Log Analytics 监视 Azure SQL 数据同步](sql-database-sync-monitor-oms.md)
-   [Azure SQL 数据同步问题疑难解答](sql-database-troubleshoot-data-sync.md)

-   演示如何配置 SQL 数据同步的完整 PowerShell 示例：
    -   [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [下载 SQL 数据同步 REST API 文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

有关 SQL 数据库的详细信息，请参阅：

-   [SQL 数据库概述](sql-database-technical-overview.md)
-   [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)
