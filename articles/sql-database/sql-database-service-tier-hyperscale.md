---
title: 超大规模概述
description: 本文介绍 Azure SQL 数据库中基于 vCore 的购买模型中的“超大规模”服务层级，并说明它与“常规用途”服务层级和“业务关键”服务层级的不同之处。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: b09e5366584e9974e67d47d34f22a3483be14f7a
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805750"
---
# <a name="hyperscale-service-tier"></a>“超大规模”服务层级

Azure SQL 数据库基于 SQL Server 数据库引擎体系结构，该体系结构已根据云环境做出调整，以确保即使在发生基础结构故障时，也仍能提供 99.99% 的可用性。 Azure SQL 数据库中使用了三种体系结构模型：
- 常规用途/标准 
-  超大规模
-  业务关键/高级

Azure SQL 数据库中的“超大规模”服务层级是基于 vCore 的购买模型中的最新服务层级。 此服务层级是一个高度可缩放的存储和计算性能层，它利用 Azure 体系结构来横向扩展 Azure SQL 数据库的存储和计算资源，远远超出了“常规用途”和“业务关键”服务层级的可用限制。

> 
> [!NOTE]
> 有关基于 vCore 的购买模型中的“常规用途”服务层级和“业务关键”服务层级的详细信息，请参阅[常规用途](sql-database-service-tier-general-purpose.md)服务层级和[业务关键](sql-database-service-tier-business-critical.md)服务层级。 有关基于 vCore 购买模型与基于 DTU 购买模型的比较，请参阅 [Azure SQL 数据库购买模型和资源](sql-database-service-tiers.md)。


## <a name="what-are-the-hyperscale-capabilities"></a>“超大规模”服务层级具有哪些功能

Azure SQL 数据库中的“超大规模”服务层级提供了以下附加功能：

- 支持高达 100 TB 的数据库大小
- 几乎即时数据库备份（基于存储在 Azure Blob 存储中的文件快照）不考虑对计算资源的 IO 影响  
- 在几分钟而不是几小时或几天内快速数据库时间点还原（基于文件快照）（不是数据操作的大小）
- 无论数据卷如何，由于更高的日志吞吐量和更快的事务提交速度，整体性能更高
- 快速横向扩展 - 可预配一个或多个只读节点，以卸载读取工作负载并用作热备用服务器
- 快速纵向扩展 - 可在不变的时间内纵向扩展计算资源，以在需要时适应繁重的工作负载，然后在不需要时缩减计算资源。

“超大规模”服务层级消除了传统上云数据库中出现的许多实际限制。 当大多数其他数据库受单个节点中可用资源的限制时，“超大规模”服务层级中的数据库则没有此类限制。 它具备灵活的存储体系结构，存储可按需增长。 实际上，不会使用定义的最大大小创建“超大规模”数据库。 “超大规模”数据库会按需扩大，你仅需为所使用的容量付费。 对于读取密集型工作负荷，“超大规模”服务层级通过按需预配其他只读副本来卸载读取工作负荷，从而实现快速横向扩展。

此外，创建数据库备份或纵向扩展/横向扩展所需的时间不再与数据库中的数据卷相关。 几乎可以即时备份“超大规模”数据库。 还可在几分钟内纵向扩展或横向扩展数十 TB 的数据库。 此功能使你无需担心受初始配置选项的约束。

有关“超大规模”服务层级计算大小的详细信息，请参阅[服务层级特征](sql-database-service-tiers-vcore.md#service-tiers)。

## <a name="who-should-consider-the-hyperscale-service-tier"></a>哪些群体应考虑使用“超大规模”服务层级

超大规模服务层适用于大多数业务工作负荷，因为它通过可单独缩放的计算和存储资源提供极大的灵活性和高性能。 由于能够自动缩放最大为 100 TB 的存储，因此对于以下客户来说，这是一个很好的选择：

- 在本地部署大型数据库，并想要通过迁移到云来实现应用程序的现代化
- 已在云中，受限于其他服务层的最大数据库大小限制（1-4 TB）
- 具有较小的数据库，但需要快速的垂直和水平计算缩放、高性能、即时备份和快速数据库还原。

超大规模服务层支持从纯 OLTP 到纯分析的各种 SQL Server 工作负荷，但它主要针对 OLTP 和混合事务以及分析处理（HTAP）工作负荷进行了优化。

> [!IMPORTANT]
> 弹性池不支持“超大规模”服务层级。

## <a name="hyperscale-pricing-model"></a>“超大规模”定价模型

仅 [vCore 模型](sql-database-service-tiers-vcore.md)提供“超大规模”服务层级。 为了适应新的体系结构，它的定价模型与“常规用途”或“业务关键”服务层级略有不同：

- **计算**：

  “超大规模”计算单位按副本计费。 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)价格会自动应用到读取扩展副本。 默认情况下，每个超大规模数据库创建一个主副本和一个只读副本。  用户可以调整副本的总数，包括1-5 中的主副本。

- **存储**：

  配置“超大规模”数据库时，无需指定最大数据大小。 超大规模层中根据实际用量收取数据库存储费用。 在 10 gb 到 100 TB 之间，以动态方式在 10 GB 和 40 GB 之间动态调整存储空间。  

有关“超大规模”服务层级定价的详细信息，请参阅 [Azure SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>分布式功能体系结构

不同于将所有数据管理功能集中在一个位置/进程中的传统数据库引擎（即便是当今生产中所谓的分布式数据库也有单片数据引擎的多个副本），“超大规模”数据库将查询处理引擎（其中各种数据引擎的语义不同）与为数据提供长期存储和持久性的组件分隔开来。 通过这种方式，可根据需要顺利地扩大存储容量（初始目标是 100 TB）。 只读副本共享相同的存储组件，因此不需要数据复制来启动新的可读副本。 

下图说明了“超大规模”数据库中不同类型的节点：

![体系结构](./media/sql-database-hyperscale/hyperscale-architecture2.png)

超大规模数据库包含以下不同类型的组件：

### <a name="compute"></a>计算

计算节点是关系引擎的所在位置，因此会出现所有语言元素、查询处理等。 所有用户与“超大规模”数据库的交互都通过这些计算节点进行。 计算节点具有基于 SSD 的缓存（在上图中标记为 RBPEX - 可复原缓冲池扩展），可最小化提取一页数据所需的网络往返次数。 其中有一个处理所有读写工作负载和事务的主计算节点。 有一个或多个充当热备用服务器节点的辅助计算节点，用于进行故障转移，也充当用于卸载只读工作负载的只读计算节点（如需此功能）。

### <a name="page-server"></a>页面服务器

页面服务器是表示横向扩展存储引擎的系统。  每个页面服务器负责数据库中页面的一个子集。  通常，每个页面服务器都控制 128 GB 和 1 TB 的数据。 多个页面服务器上不共享任何数据（为冗余和可用性而保留的副本除外）。 页面服务器的任务是按需向计算节点提供数据库页面，并在事务更新数据时持续更新页面。 页面服务器通过从日志服务播放日志记录来保持最新。 页面服务器还保留基于 SSD 的缓存，可提高性能。 数据页的长期存储保存在 Azure 存储中，可提高可靠性。

### <a name="log-service"></a>日志服务

日志服务接受来自主要计算副本的日志记录，将它们保留在持久缓存中，并将日志记录转发到计算副本的其余部分（以便它们可以更新其缓存）以及相关的页面服务器，以便可以更新数据。可能. 通过这种方式，可以将主计算副本中的所有数据更改通过日志服务传播到所有辅助计算副本和页面服务器。 最后，将日志记录推送到 Azure 存储中的长期存储，这是一个几乎不需要的存储存储库。 此机制消除了频繁进行日志截断的需求。 日志服务还具有本地缓存，以加速对日志记录的访问。

### <a name="azure-storage"></a>Azure 存储

Azure 存储包含数据库中的所有数据文件。 页面服务器将 Azure 存储中的数据文件保持在最新状态。 此存储用于备份目的，以及用于在 Azure 区域之间进行复制。 备份是使用数据文件的存储快照实现的。 无论数据大小如何，使用快照的还原操作都能快速进行。 数据可以还原到数据库备份保留期内的任意时间点。

## <a name="backup-and-restore"></a>备份和还原

备份是基于文件快照的，因此它们几乎是瞬时的。 通过存储和计算分离功能，可以将备份/还原操作推送到存储层，以减少主计算副本上的处理负担。 因此，数据库备份不会影响主计算节点的性能;同样，还原是通过还原到文件快照来完成的，因此不是数据操作的大小。 还原是一项恒定的操作，甚至可以在几分钟内（而不是几小时或几天）还原多 tb 数据库。 通过还原现有备份来创建新数据库时，还可以利用此功能：在同一逻辑服务器中创建用于开发或测试的数据库副本（甚至是 tb 大小的数据库）是可行。

## <a name="scale-and-performance-advantages"></a>缩放和性能优势

超大规模体系结构能快速启动/关闭其他只读计算节点，拥有重要的读取扩展功能，还可以释放主计算节点，以满足更多写入请求。 此外，由于超大规模体系结构的共享存储体系结构，可快速纵向扩展/横向扩展计算节点。

## <a name="create-a-hyperscale-database"></a>创建超大规模数据库

可以使用 [Azure 门户](https://portal.azure.com)、[T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)[Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) 或者 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) 创建超大规模数据库。 仅可通过[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)使用超大规模数据库。

以下 T-SQL 命令可创建一个“超大规模”数据库。 必须在 `CREATE DATABASE` 语句中指定版本和服务目标。 有关有效服务目标的列表，请参阅[资源限制](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen5)。

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
这将在具有4个内核的 Gen5 硬件上创建超大规模数据库。

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>将现有 Azure SQL 数据库迁移到“超大规模”服务层级

可以使用 [Azure 门户](https://portal.azure.com)[T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)[Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) 或者 [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)将现有的 Azure SQL 数据库迁移到“超大规模”层级。 目前，这是一个单向迁移。 除了导出和导入数据外，不能将数据库从超大规模移到其他服务层。 对于概念证明（Poc），我们建议创建生产数据库的副本，并将副本迁移到超大规模。 将现有 Azure SQL 数据库迁移到超大规模层是数据操作的大小。

以下 T-SQL 命令可将数据库移动到“超大规模”服务层级。 必须在 `ALTER DATABASE` 语句中指定版本和服务目标。

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>连接到“超大规模”数据库的读取扩展副本

在超大规模数据库中，由客户端提供的连接字符串中的 `ApplicationIntent` 参数决定连接是路由到写入副本，还是路由到只读的次要副本。 如果将 `ApplicationIntent` 设置为 `READONLY`并且数据库不具有辅助副本，连接将路由到主副本，默认执行 `ReadWrite` 行为。

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

超大规模辅助副本都是相同的，它们使用与主副本相同的服务级别目标。 如果存在多个辅助副本，则会在所有可用的辅助副本之间分布工作负荷。 每个辅助副本单独更新，因此不同的副本相对于主副本可能具有不同的数据延迟。

## <a name="database-high-availability-in-hyperscale"></a>超大规模中的数据库高可用性

与所有其他服务层一样，超大规模保证已提交事务的数据持久性，而不考虑计算副本的可用性。 由于主副本变得不可用而导致的停机范围取决于故障转移的类型（计划的与未计划的），以及是否存在至少一个辅助副本。 在计划的故障转移（即维护事件）中，系统将在启动故障转移之前创建新的主副本，或使用现有的辅助副本作为故障转移目标。 在非计划的故障转移（即主副本上的硬件故障）中，系统使用辅助副本作为故障转移目标（如果存在），或从可用计算容量池中创建新的主副本。 在后一种情况下，由于创建新的主副本需要额外的步骤，导致停机持续时间较长。

有关超大规模 SLA，请参阅[AZURE SQL 数据库的 sla](https://azure.microsoft.com/support/legal/sla/sql-database/)。

## <a name="disaster-recovery-for-hyperscale-databases"></a>超大规模数据库的灾难恢复

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>将超大规模数据库还原到不同的地理位置
如果需要将 Azure SQL Database 超大规模 DB 还原到它当前所在的区域以外的区域，作为灾难恢复操作或钻取、重定位或其他任何原因的一部分，主要方法是对数据库进行异地还原。  这涉及到与将任何其他 AZURE SQL 数据库还原到不同区域时使用的步骤完全相同的步骤：
1. 如果你还没有适当的服务器，请在目标区域中创建一个 SQL 数据库服务器。  此服务器应属于原始（源）服务器所在的同一订阅。
2. 按照从自动备份还原 Azure SQL 数据库页上的 "[异地还原](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore)" 主题中的说明进行操作。

> [!NOTE]
> 由于源和目标位于不同的区域中，因此数据库无法与源数据库共享快照存储，就像在非异地还原中一样，这种情况极快完成。  对于超大规模数据库的异地还原，它将是一项数据大小的操作，即使目标位于异地复制存储的配对区域中。  这意味着，执行异地还原需要与要还原的数据库的大小成正比。  如果目标在配对的区域中，则副本将位于数据中心内，其速度要快于 internet 上的长距离副本，但它仍将复制所有的位。

## <a name=regions></a>可用区域

Azure SQL Database 超大规模层目前在以下区域中提供：

- 澳大利亚东部
- 澳大利亚东南部
- 巴西南部
- 加拿大中部
- 美国中部
- 中国东部 2
- 中国北部 2
- 亚洲东部
- 美国东部
- 美国东部2
- 法国中部
- 日本东部
- 日本西部
- 韩国中部
- 韩国南部
- 美国中北部
- 北欧
- 南非北部
- 美国中南部
- 亚洲东南部
- 英国南部
- 英国西部
- 欧洲西部
- 美国西部
- 美国西部 2

如果要在未列出为受支持的区域中创建超大规模数据库，可以通过 Azure 门户发送载入请求。 我们正在努力展开受支持区域的列表，请查看最新的地区列表。

请求在未列出的区域中创建超大规模数据库的功能：

1. 导航到 " [Azure 帮助和支持" 边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. 单击 " [**新建支持请求**"](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Azure "帮助和支持" 边栏选项卡](media/sql-database-service-tier-hyperscale/request-screen-1.png)

3. 对于 "**问题类型**"，请选择 "**服务和订阅限制（配额）** "

4. 选择要用于创建数据库的订阅

5. 对于 "**配额类型**"，选择 " **SQL 数据库**"

6. 单击 "**下一步：解决方案**"

1. 单击 "**提供详细信息**"

    ![问题详细信息](media/sql-database-service-tier-hyperscale/request-screen-2.png)

8. 选择**SQL 数据库配额类型**：**其他配额请求**

9. 填写以下模板：

    ![配额详细信息](media/sql-database-service-tier-hyperscale/request-screen-3.png)

    在模板中提供以下信息

    > 请求在新区域中创建 Azure 超大规模 SQL 数据库<br/> 区域： [填写所需区域]  <br/>
    > 计算 SKU/总内核数，包括可读副本 <br/>
    > 估计的 TB 数 
    >

10. 选择“严重性 C”

11. 选择适当的联系方法并填写详细信息。

12. 单击 "**保存**并**继续**"

## <a name="known-limitations"></a>已知限制
这是公开上市后超大规模服务层的当前限制。  我们正在努力尽可能多地删除这些限制。

| 问题 | 描述 |
| :---- | :--------- |
| 逻辑服务器的 "管理备份" 窗格不显示将从 SQL server 筛选超大规模数据库  | 超大规模具有用于管理备份的单独方法，因此，长期保留期和时间点备份保留设置不适用/无效。 相应地，“超大规模”数据库不会显示在“管理备份”窗格中。 |
| 时间点还原 | 将数据库迁移到超大规模服务层后，不支持在迁移之前还原到某个时间点。|
| 将非超大规模 DB 还原到 Hypserscale，反之亦然 | 不能将超大规模数据库还原到非超大规模数据库中，也不能将非超大规模数据库还原到超大规模数据库中。|
| 如果数据库的一个或多个数据文件大于 1 TB，则迁移将失败 | 在某些情况下，可以通过将大文件收缩为小于 1 TB 来解决此问题。 如果迁移在迁移过程中使用的数据库，请确保没有任何文件大于 1 TB。 使用以下查询来确定数据库文件的大小。 `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`；|
| 托管实例 | 超大规模数据库当前不支持 Azure SQL 数据库托管实例。 |
| 弹性池 |  当前不支持对 SQL 数据库超大规模进行弹性池。|
| 迁移到“超大规模”服务层级目前是单向操作 | 将数据库迁移到“超大规模”层级后，它不能直接迁移到非“超大规模”服务层级。 目前，将数据库从超大规模迁移到非超大规模的唯一方法是使用 BACPAC 文件或其他数据移动技术（大容量复制、Azure 数据工厂、Azure Databricks、SSIS 等）进行导出/导入。|
| 迁移具有持久性内存中对象的数据库 | 超大规模仅支持非持久性内存中对象（表类型、本机 Sp 和函数）。  在将数据库迁移到超大规模服务层之前，必须删除持久性内存中表和其他对象，并将其重新创建为非内存对象。|
| 更改跟踪 | 更改跟踪当前为公共预览版，可以在新的或现有的超大规模数据库上启用。 |
| 异地复制  | 你尚未配置 Azure SQL 数据库超大规模的异地复制。 |
| 数据库复制 | 你还不能使用数据库副本在 Azure SQL 超大规模中创建新数据库。 |
| TDE/AKV 集成 | 对于 Azure SQL 数据库超大规模，尚不支持使用 Azure Key Vault （通常称为 "自带密钥" 或 BYOK）的透明数据库加密，但是，完全支持具有服务托管密钥的 TDE。 |
|智能数据库功能 | 除了 "强制计划" 选项外，所有其他自动优化选项在超大规模中尚不受支持：选项可能看起来已启用，但不会有任何建议或操作。 |
| 收缩数据库 | Azure SQL 超大规模数据库当前不支持 DBCC SHRINKDATABASE 或 DBCC SHRINKFILE。 |

## <a name="next-steps"></a>后续步骤

- 有关“超大规模”的常见问题，请参阅[“超大规模”常见问题解答](sql-database-service-tier-hyperscale-faq.md)。
- 有关服务层级的信息，请参阅[服务层级](sql-database-service-tiers.md)
- 有关服务器和订阅级别限制的信息，请参阅[逻辑服务器上的资源限制概述](sql-database-resource-limits-logical-server.md)。
- 有关单一数据库的购买模型限制的信息，请参阅 [适用于单一数据库的 Azure SQL 数据库基于 vCore 的购买模型限制](sql-database-vcore-resource-limits-single-databases.md)。
- 有关功能和比较列表，请参阅 [SQL 常用功能](sql-database-features.md)。
