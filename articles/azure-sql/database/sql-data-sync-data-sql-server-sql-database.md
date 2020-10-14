---
title: 什么是 Azure SQL 数据同步？
description: 本文概述了 Azure SQL 数据同步，它可用于跨多个云端和本地数据库同步数据。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: 268455e582e54dfa8eb73fe81eaad19f453e303b
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057886"
---
# <a name="what-is-sql-data-sync-for-azure"></a>什么是 Azure SQL 数据同步？

使用 SQL 数据同步这项基于 Azure SQL 数据库的服务，可以跨多个本地和云端数据库双向同步选定数据。 

> [!IMPORTANT]
> 目前，Azure SQL 数据同步不支持 Azure SQL 托管实例。


## <a name="overview"></a>概述 

数据同步以同步组的概念为依据。 同步组是一组要同步的数据库。

SQL 数据同步使用中心辐射型拓扑来同步数据。 将同步组中的一个数据库定义为中心数据库。 其余数据库均为成员数据库。 仅在中心和各成员之间同步数据。

- 中心数据库必须是 Azure SQL 数据库。
- 成员数据库可以是 Azure SQL 数据库或 SQL Server 实例中的数据库。
- 同步元数据数据库包含用于数据同步的元数据和日志。同步元数据数据库必须是与中心数据库位于同一区域的 Azure SQL 数据库。 同步元数据数据库的创建者和所有者均为客户。 每个区域和订阅只能有一个同步元数据数据库。 存在同步组或同步代理时，无法删除或重命名同步元数据数据库。 Microsoft 建议新建空的数据库，以用作“同步元数据数据库”。 SQL 数据同步在此数据库中创建表，并经常运行工作负载。

> [!NOTE]
> 如果使用本地数据库作为成员数据库，则必须[安装并配置本地同步代理](sql-data-sync-sql-server-configure.md#add-on-prem)。

![在数据库之间同步数据](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

同步组具有以下属性：

- “同步架构”描述了在同步的数据。
- “同步方向”可以是双向同步，也可以仅为单向同步。 也就是说，“同步方向”可以是“从中心同步到成员”和/或“从成员同步到中心”。
- “同步间隔”描述多久执行一次同步。
- “冲突解决策略”是组级别策略，可以是“中心胜出”，也可以是“成员胜出”。

## <a name="when-to-use"></a>何时使用

如果需要跨 Azure SQL 数据库或 SQL Server 中的多个数据库使数据保持最新，数据同步非常有用。 下面是 SQL 数据同步的主要用例：

- **混合数据同步：** 借助数据同步，可以在 SQL Server 和 Azure SQL 数据库中的数据库之间保持数据同步，以便启用混合应用程序。 此功能可能会吸引在考虑迁移到云中，并希望启用 Azure 应用程序的客户。
- **分布式应用程序：** 在许多情况下，跨各个数据库分散不同的工作负载会大有裨益。 例如，如果有大型生产数据库，但还需要对此数据运行报表或分析工作负载，那么使用第二个数据库来处理此额外工作负载将会有所帮助。 这种方法可最大限度地减轻对生产工作负载造成的性能影响。 可以使用 SQL 数据同步来同步这两个数据库。
- **全球分布的应用程序：** 许多企业的业务分布在多个区域，甚至是多个国家/地区/区域。 为了最大限度地缩短网络延迟时间，最好将数据存储在靠近的区域中。 借助 SQL 数据同步，可轻松同步世界各地区域中的数据库。

数据同步不是以下场景的首选解决方案：

| 方案 | 一些建议的解决方案 |
|----------|----------------------------|
| 灾难恢复 | [Azure 异地冗余备份](automated-backups-overview.md) |
| 读取缩放 | [使用只读副本对只读的查询工作负荷进行负载均衡（预览版）](read-scale-out.md) |
| ETL（OLTP 到 OLAP） | [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)或 [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| 从 SQL Server 迁移到 Azure SQL 数据库 | [Azure 数据库迁移服务](https://azure.microsoft.com/services/database-migration/) |
|||



## <a name="how-it-works"></a>工作原理

- **跟踪数据更改：** SQL 数据同步使用插入、更新和删除触发器来跟踪更改。 更改记录在用户数据库中的端表内。 请注意，默认情况下 BULK INSERT 不会激发触发器。 如果未指定 FIRE_TRIGGERS，则不执行任何插入触发器操作。 添加 FIRE_TRIGGERS 选项，以便数据同步可以跟踪这些插入。 
- **同步数据：** 根据设计，数据同步采用中心辐射型模型。 中心与各个成员同步数据。 中心内的更改会先下载到成员，然后成员内的更改会上传到中心。
- **解决冲突：** SQL 数据同步提供两个冲突解决选项，即“中心胜出”或“成员胜出” 。
  - 如果选择“中心胜出”，中心内的更改始终覆盖成员内的更改。
  - 如果选择“成员胜出”，成员内的更改覆盖中心内的更改。 如果有多个成员，最终值取决于哪个成员最先同步。

## <a name="compare-with-transactional-replication"></a>与事务复制比较

| | 数据同步 | 事务复制 |
|---|---|---|
| **优点** | - 主动-主动支持<br/>- 在本地和 Azure SQL 数据库之间双向同步 | - 更低的延迟<br/>- 事务一致性<br/>- 迁移后重用现有拓扑 <br/>\- Azure SQL 托管实例支持 |
| **缺点** | - 5 分钟或更长的延迟<br/>- 无事务一致性<br/>- 更高的性能影响 | - 无法从 Azure SQL 数据库发布 <br/>- 维护成本高 |

## <a name="get-started"></a>入门 

### <a name="set-up-data-sync-in-the-azure-portal"></a>在 Azure 门户中设置数据同步

- [设置 Azure SQL 数据同步](sql-data-sync-sql-server-configure.md)
- Data Sync Agent - [Azure SQL 数据同步的 Data Sync Agent](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>使用 PowerShell 设置数据同步

- [使用 PowerShell 在 Azure SQL 数据库中的多个数据库之间同步](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [使用 PowerShell 在 Azure SQL 数据库中的数据库和 SQL Server 实例中的数据库之间同步](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>查看数据同步最佳做法

- [Azure SQL 数据同步最佳实践](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>出现了错误

- [Azure SQL 数据同步问题疑难解答](../../sql-database/sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>一致性和性能

### <a name="eventual-consistency"></a>最终一致性

由于 SQL 数据同步是基于触发器的服务，因此无法保证事务一致性。 Microsoft 保证最终做出的所有更改，并且数据同步不会导致数据丢失。

### <a name="performance-impact"></a>性能影响

SQL 数据同步使用插入、更新和删除触发器来跟踪更改。 它在用户数据库中创建用于跟踪的端表。 这些更改跟踪活动会对数据库工作负荷产生影响。 评估服务层级并根据需要升级。

在同步组创建、更新和删除期间预配和取消预配也可能会影响数据库性能。

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> 要求和限制

### <a name="general-requirements"></a>一般要求

- 每个表都必须有主键。 请勿更改任何一行中的主键值。 如果必须更改主键值，请先删除行，再使用新的主键值重新创建此行。

> [!IMPORTANT]
> 更改现有主键的值会导致以下错误行为：
> - 即使同步未报告任何问题，中心和成员之间的数据也会丢失。
> - 由于主键更改，跟踪表的源行不存在，同步可能会失败。

- 必须同时为同步成员和中心启用快照隔离。 有关详细信息，请参阅 [SQL Server 中的快照隔离](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server)。

### <a name="general-limitations"></a>一般限制

- 表不能包含非主键标识列。
- 表必须具有聚集索引，才能使用数据同步。
- 主键不能具有以下数据类型：sql_variant、binary、varbinary、image、xml。
- 使用以下数据类型作为主键时请小心谨慎，因为支持的精度仅到秒：time、datetime、datetime2、datetimeoffset。
- 对象（数据库、表和列）的名称不能包含可打印字符句点 (.)、左方括号 ([) 或右方括号 (])。
- 不支持 Azure Active Directory 身份验证。
- 如果存在具有相同名称但不同架构的表 (例如，dbo. customers 和 sales。客户) 只能将其中一个表添加到同步中。
- 不支持具有用户定义数据类型的列
- 不支持在不同订阅之间移动服务器。 

#### <a name="unsupported-data-types"></a>不支持的数据类型

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection（支持 XML）
- Cursor、RowVersion、Timestamp、Hierarchyid

#### <a name="unsupported-column-types"></a>不支持的列类型

数据同步无法同步只读列或系统生成的列。 例如：

- 计算列。
- 系统生成的时态表列。

#### <a name="limitations-on-service-and-database-dimensions"></a>服务和数据库维度方面的限制

| **Dimensions**                                                  | **限制**              | **解决方法**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| 任何数据库可属于的同步组的数量上限。       | 5                      |                             |
| 一个同步组中包含的终结点的数量上限              | 30                     |                             |
| 一个同步组中包含的本地终结点的数量上限。 | 5                      | 创建多个同步组 |
| 数据库、表、架构和列名称                       | 每个名称 50 个字符 |                             |
| 同步组中的表                                          | 500                    | 创建多个同步组 |
| 同步组中的表列                              | 1000                   |                             |
| 表中的数据行大小                                        | 24MB                  |                             |
| 最小同步间隔                                           | 5 分钟              |                             |

> [!NOTE]
> 如果只有一个同步组，则单个同步组中最多可能有 30 个终结点。 如果有多个同步组，则所有同步组中的终结点总数不能超过 30。 如果数据库属于多个同步组，则该数据库计算为多个终结点，而不是一个。

### <a name="network-requirements"></a>网络要求

在建立同步组后，数据同步服务需要连接到中心数据库。 建立同步组时，Azure SQL server 的设置中必须具有以下配置 `Firewalls and virtual networks` ：

 * *拒绝公共网络访问* 必须设置为 *Off*。
 * "*允许 Azure 服务和资源访问此服务器*" 必须设置为 *"是"*，或者必须为[数据同步服务使用的 ip 地址](network-access-controls-overview.md#data-sync)创建 ip 规则。

创建并设置了同步组后，你可以禁用这些设置。 同步代理将直接连接到中心数据库，你可以使用服务器的 [防火墙 IP 规则](firewall-configure.md) 或 [专用终结点](private-endpoint-overview.md) 来允许代理访问中心服务器。

> [!NOTE]
> 如果更改同步组的架构设置，你将需要允许数据同步服务再次访问服务器，以便可以重新预配中心数据库。

## <a name="faq-about-sql-data-sync"></a>SQL 数据同步常见问题解答

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>SQL 数据同步服务的价格是多少

对 SQL 数据同步服务本身不收取任何费用。 但是，对于传入和传出 SQL 数据库实例的数据移动，你仍然收取数据传输费用。 有关价格，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。

### <a name="what-regions-support-data-sync"></a>哪些区域支持数据同步

SQL 数据同步在所有区域中都可用。

### <a name="is-a-sql-database-account-required"></a>是否需要 SQL 数据库帐户

是的。 必须拥有 SQL 数据库帐户才能托管中心数据库。

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>是否可以使用数据同步仅在 SQL Server 数据库之间进行同步

无法直接配合使用。 但是，可以间接地在 SQL Server 数据库之间同步，方法是在 Azure 中创建一个中心数据库，然后将本地数据库添加到同步组。

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>是否可以使用数据同步在 SQL 数据库中属于不同订阅的数据库之间进行同步

是的。 可以在由不同订阅拥有的资源组中的数据库之间同步。

- 如果订阅属于同一租户，并且你对所有订阅都有权限，则可以在 Azure 门户中配置同步组。
- 否则，必须使用 PowerShell 来添加属于不同订阅的同步成员。

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>是否可以使用数据同步在 SQL 数据库中属于不同云（例如 Azure 公有云和 Azure 中国世纪互联）的数据库之间进行同步

是的。 可以在属于不同云的数据库之间进行同步。 必须使用 PowerShell 来添加属于不同订阅的同步成员。

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>是否可以使用数据同步将生产数据库中的数据种子植入到空数据库，然后将这两个数据库同步

是的。 通过从原始数据库编写脚本，在新数据库中手动创建架构。 创建架构后，将表添加到同步组以复制数据并使其同步。

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>我应该使用 SQL 数据同步备份和还原数据库吗

建议不要使用 SQL 数据同步创建数据备份。 由于 SQL 数据同步的同步不进行版本控制，因此无法备份并还原到特定时间点。 此外，SQL 数据同步不会备份其他 SQL 对象（如存储过程），并且不会快速执行还原操作的等效操作。

有关推荐的备份技术，请参阅[在 Azure SQL 数据库中复制数据库](database-copy.md)。

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>数据同步是否可以同步加密的表或列

- 如果数据库使用了 Always Encrypted，则只能同步未加密的表和列。 无法同步加密的列，因为数据同步无法对数据进行解密。
- 如果某个列使用了列级加密 (CLE)，则可以对该列进行同步，只要行大小小于最大大小 24 MB。 数据同步将密钥 (CLE) 加密的列视为普通的二进制数据。 若要解密其他同步成员上的数据，则需要具有相同的证书。

### <a name="is-collation-supported-in-sql-data-sync"></a>SQL 数据同步是否支持排序规则

是的。 SQL 数据同步在以下情况下支持排序规则：

- 如果所选同步架构表不在中心或成员数据库中，则部署同步组时，该服务会使用空目标数据库中选择的排序规则设置自动创建相应的表和列。
- 如果要同步的表已经存在于中心和成员数据库中，则 SQL 数据同步要求中心数据库和成员数据库之间的主键列具有相同的排序规则，以成功部署同步组。 主键列以外的列没有排序规则限制。

### <a name="is-federation-supported-in-sql-data-sync"></a>SQL 数据同步是否支持联合

联合根数据库可用于 SQL 数据同步服务，没有任何限制。 不能将联合数据库终结点添加到当前版本的 SQL 数据同步。

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>是否可以使用数据同步来同步通过自带数据库 (BYOD) 功能从 Dynamics 365 导出的数据？

利用 Dynamics 365，自带数据库功能，管理员可以将应用程序中的数据实体导出到各自 Microsoft Azure 的 SQL 数据库。 如果使用“增量推送”（不支持完全推送）导出数据，并且“在目标数据库中启用触发器”设置为“是”，则可以使用数据同步来将这些数据同步到其他数据库中  。

## <a name="next-steps"></a>后续步骤

### <a name="update-the-schema-of-a-synced-database"></a>更新同步数据库的架构

是否必须更新同步组中数据库的架构？ 不会自动复制架构更改。 有关某些解决方案，请参阅以下文章：

- [在 Azure SQL 数据同步中自动复制架构更改](../../sql-database/sql-database-update-sync-schema.md)
- [使用 PowerShell 更新现有同步组中的同步架构](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>监视和故障排除

SQL 数据同步是否按预期执行？ 若要监视活动和排查问题，请参阅以下文章：

- [使用 Azure Monitor 日志监视 SQL 数据同步](../../sql-database/sql-database-sync-monitor-oms.md)
- [Azure SQL 数据同步问题疑难解答](../../sql-database/sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>了解有关 Azure SQL 数据库的详细信息

有关 Azure SQL 数据库的详细信息，请参阅以下文章：

- [SQL 数据库概述](sql-database-paas-overview.md)
- [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)
 
