---
title: “超大规模”常见问题解答
description: 对客户关于“超大规模”服务层级中的 Azure SQL 数据库（通常称为超大规模数据库）提出的常见问题的回答。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 10/12/2019
ms.openlocfilehash: 89316811f39b4cab918b913b45ae5945686e6b15
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818214"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL Database 超大规模常见问题

本文提供了有关在 Azure SQL 数据库超大规模服务层中使用数据库的客户的常见问题的解答，这只是在本 FAQ 的其余部分中称为超大规模。 本文介绍超大规模支持的方案以及与超大规模兼容的功能。

- 本常见问题解答适用于对“超大规模”服务层级有基本了解并希望其具体问题得到解答的读者。
- 此常见问题并非 guidebook 或回答有关如何使用超大规模数据库的问题。 有关超大规模的简介，建议参阅[AZURE SQL 数据库超大规模](sql-database-service-tier-hyperscale.md)文档。

## <a name="general-questions"></a>一般问题

### <a name="what-is-a-hyperscale-database"></a>什么是“超大规模”数据库

超大规模数据库是“超大规模”服务层级中的 Azure SQL 数据库，由超大规模横向扩展存储技术提供支持。 一个“超大规模”数据库支持最多 100 TB 的数据，提供高吞吐量和高性能，可以快速缩放以适应工作负荷要求。 缩放对应用程序（连接性、查询处理等）是透明的，与任何其他 Azure SQL 数据库的工作方式相同。

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>哪些资源类型和购买模型支持“超大规模”

“超大规模”服务层级仅适用于在 Azure SQL 数据库中使用基于 vCore 的购买模型的单一数据库。  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>“超大规模”服务层级与“常规用途”和“业务关键”服务层级有何区别

基于 vCore 的服务层根据数据库可用性和存储类型、性能和最大大小进行区分，如下表中所述。

| | 资源类型 | 常规用途 |  超大规模 | 业务关键 |
|:---:|:---:|:---:|:---:|:---:|
| **最适用于** |All|提供以预算导向的、均衡的计算和存储选项。|大多数业务工作负荷。 自动缩放存储大小，最高可达 100 TB、垂直和水平计算缩放和快速数据库还原。|事务速率较高和 IO 延迟较低的 OLTP 应用程序。 使用多个同步更新的副本提供最高的故障恢复能力和快速故障转移。|
|  **资源类型** ||单一数据库/弹性池/托管实例 | 单一数据库 | 单一数据库/弹性池/托管实例 |
| **计算大小**|单一数据库/弹性池* | 1 - 80 个 vCore | 1 - 80 个 vCore* | 1 - 80 个 vCore |
| |托管实例 | 8、16、24、32、40、64、80 个 vCore | 不适用 | 8、16、24、32、40、64、80 个 vCore |
| **存储类型** | All |高级远程存储（每个实例） | 具有本地 SSD 缓存的分离的存储（每个实例） | 超快的本地 SSD 存储（每个实例） |
| **存储大小** | 单一数据库/弹性池*| 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 托管实例  | 32 GB – 8 TB | 不适用 | 32 GB – 4 TB |
| **IOPS** | 单一数据库 | 每个 vCore 提供 500 IOPS，最大 7000 IOPS | 超大规模是具有多个级别缓存的多层体系结构。 有效 IOPS 将取决于工作负荷。 | 5000 IOPS，最大 200,000 IOPS|
| | 托管实例 | 取决于文件大小 | 不适用 | 1375 IOPS/vCore |
|**可用性**|All|1个副本，无读取横向扩展，无本地缓存 | 多个副本，多达4个读取横向扩展，局部本地缓存 | 3个副本，1个读取横向扩展，个区域冗余 HA，完整本地存储 |
|**备份**|All|GRS，7-35 天保留期（默认为7天）| GRS，7天保留期，固定时间点恢复（PITR） | GRS，7-35 天保留期（默认为7天） |

超大规模服务层不支持 \* 弹性池

### <a name="who-should-use-the-hyperscale-service-tier"></a>哪些群体应使用“超大规模”服务层级

超大规模服务层适用于拥有大型本地 SQL Server 数据库并想要通过迁移到云或已在使用 Azure SQL 数据库的客户来实现应用程序现代化的客户，并希望大大扩展数据库增长的潜能。 “超大规模”服务层级也适用于那些寻求高性能和高可伸缩性的客户。 使用“超大规模”服务层级，可以：

- 数据库大小最大为 100 TB
- 快速数据库备份，而不考虑数据库大小（备份基于存储快照）
- 快速数据库还原，而不考虑数据库大小（从存储快照还原）
- 日志吞吐量较高，与数据库大小和 Vcore 数无关
- 使用一个或多个只读副本读取横向扩展，这些副本用于读取和热待机。
- 在恒定时间内快速纵向扩展计算，以便提高适应繁重工作负荷的能力；然后在恒定时间内减少。 例如，这类似于在 P6 和 P11 之间向上缩放和向下缩放，但速度要快得多，因为这不是数据操作的大小。

### <a name="what-regions-currently-support-hyperscale"></a>哪些区域当前支持“超大规模”

超大规模服务层当前在[AZURE SQL 数据库超大规模概述](sql-database-service-tier-hyperscale.md#regions)下列出的区域中可用。

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>能否为每个逻辑服务器创建多个“超大规模”数据库

是的。 有关每个逻辑服务器的“超大规模”数据库数量的详细信息和限制，请参阅[逻辑服务器上单一和共用数据库的 SQL 数据库资源限制](sql-database-resource-limits-logical-server.md)。

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>“超大规模”数据库的性能特征有哪些

超大规模体系结构提供高性能和吞吐量，同时支持大数据库大小。 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>什么是“超大规模”数据库的可伸缩性

超大规模可根据工作负荷需求提供快速缩放性。

- **增加/减少**

  借助超大规模，可以在资源（如 CPU 和内存）中纵向扩展主计算大小，然后在固定时间内缩小。 因为是共享存储，所以纵向扩展和缩减不是数据大小操作。  
- **缩小/扩大**

  借助超大规模，你还可以预配一个或多个可用于为读取请求提供服务的其他计算副本。 这意味着，你可以使用这些额外的计算副本作为只读副本，从主计算中卸载你的读取工作负荷。 除了只读，这些副本在故障转移到主副本时也作为热待机。

  可在固定时间内完成每个附加计算副本的预配，这是一个联机操作。 你可以通过将连接字符串上的 `ApplicationIntent` 参数设置为 `ReadOnly`来连接到这些附加的只读计算副本。 与 `ReadOnly` 应用程序意向的任何连接都将自动路由到其他只读计算副本之一。

## <a name="deep-dive-questions"></a>深入探讨问题

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>是否可以在单个逻辑服务器中混合使用超大规模数据库和单一数据库

可以。

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>“超大规模”数据库需要更改应用程序编程模型吗

不，应用程序编程模型保持原样。 你可以像平常一样使用连接字符串，也可以使用其他常规方法与超大规模数据库进行交互。

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>超大规模数据库中的默认事务隔离级别

在主副本上，默认事务隔离级别为 RCSI （读取已提交快照隔离）。 在读取横向扩展辅助副本上，默认隔离级别为 Snapshot。

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>我可以将我的本地或 IaaS SQL Server 许可证引入超大规模

能，[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)适用于“超大规模”。 每个 SQL Server Standard 核心都可以映射到 1 个超大规模 vCore。 每个 SQL Server Enterprise 核心都可以映射到 4 个“超大规模”vCore。 对于次要副本，不需要 SQL 许可证。 Azure 混合权益价格将自动应用于读取横向扩展（辅助）副本。

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>为超大规模设计的工作负荷类型

超大规模支持所有 SQL Server 工作负荷，但它主要针对 OLTP 进行了优化。 还可以引入混合 (HTAP) 和分析（数据市场）工作负荷。

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>如何在 Azure SQL 数据仓库和 Azure SQL 数据库之间进行选择超大规模

如果你当前正在使用 SQL Server 作为数据仓库运行交互式分析查询，则超大规模是一个很好的选择，因为你可以按较低的成本托管小数据仓库和中等大小的数据仓库（例如最大 TB 到 100 TB），并且可以迁移 SQL Server 数据 warehouse 工作负荷到超大规模，并具有最小 T-sql 代码更改。

如果你使用复杂的查询和大于 100 MB/s 的持续引入速率在大规模运行数据分析，或者使用并行数据仓库（PDW）、Teradata 或其他大规模并行处理（MPP）数据仓库，则 SQL 数据仓库可能是最佳选择。
  
## <a name="hyperscale-compute-questions"></a>超大规模计算问题

### <a name="can-i-pause-my-compute-at-any-time"></a>能不能随时暂停计算

目前不能，但可以缩减计算和副本数量，以降低非高峰时段的成本。

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>能否为内存密集型工作负荷预配具有额外 RAM 的计算副本

不能。 要获取更多 RAM，需要升级到更大的计算大小。 有关详细信息，请参阅[超大规模存储和计算大小](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)。

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>能否预配不同大小的多个计算副本

不能。

### <a name="how-many-read-scale-out-replicas-are-supported"></a>支持多少个读取横向扩展副本

默认情况下，会使用一个读取横向扩展副本（两个副本，包括主副本）创建超大规模数据库。 可以使用[Azure 门户](https://portal.azure.com)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)来缩放0到4之间的只读副本数。

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>为实现高可用性，我是否需要预配其他计算副本

在超大规模数据库中，在存储级别提供数据复原功能。 只需一个副本即可提供复原能力。 计算副本关闭后，系统自动创建一个新副本，且不会丢失数据。

但是，如何只有一个副本，故障转移后可能需要一些时间才能在新副本中生成本地缓存。 在缓存重新生成阶段，数据库会直接从页面服务器中提取数据，从而提高存储延迟和降低查询性能。

对于需要高可用性并且影响最小故障转移影响的任务关键型应用程序，你至少应预配2个计算副本，包括主计算副本。 这是默认配置。 这样，就可以使用热备用副本作为故障转移目标。

## <a name="data-size-and-storage-questions"></a>数据大小和存储问题

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>超大规模支持的最大数据库大小

100 TB。

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>“超大规模”数据库事务日志的大小

“超大规模”数据库的事务日志几乎是无穷大的。 不需要担心在具有高日志吞吐量的系统上耗尽日志空间。 但是，对于连续的主动写入工作负荷，日志生成速率可能会受到限制。 峰值持续日志生成速率为 100 MB/s。

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>我的 `tempdb` 随着数据库的增长而扩展

`tempdb` 数据库位于本地 SSD 存储，是根据预配的计算大小配置的。 你的 `tempdb` 经过优化，可提供最大的性能优势。 `tempdb` 大小不可配置，将为你进行管理。

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>数据库的大小是否自动增长，或者是否需要管理数据文件的大小

你的数据库会随着你插入/引入更多数据自动增长。

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>超大规模支持或开头的最小数据库大小

10 GB。

### <a name="in-what-increments-does-my-database-size-grow"></a>数据库的大小按多少增量增长

每个数据文件的增长大小为 10 GB。 多个数据文件可能会同时增长。

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>存储在超大规模本地还是远程

在“超大规模”数据库中，数据文件存储在 Azure 标准存储中。 数据完全缓存在本地 SSD 存储上，位于接近计算副本的页面服务器上。 此外，计算副本在本地 SSD 上和内存中具有数据缓存，以降低从远程页面服务器中提取数据的频率。

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>能否使用“超大规模”数据库管理或定义文件或文件组

不能。 将自动添加数据文件。 创建其他文件组的常见原因不适用于超大规模存储体系结构。

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>能否为我的数据库的数据增长预配硬上限

不能。

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>如何将数据文件与超大规模布局

数据文件由页面服务器控制，每个数据文件有一个页面服务器。 随着数据大小的增长，将添加数据文件和关联的页面服务器。

### <a name="is-database-shrink-supported"></a>是否支持数据库收缩

不能。

### <a name="is-data-compression-supported"></a>支持数据压缩

是，包括行、页和列存储压缩。

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>如果表格巨大，表格数据是否会分布在多个数据文件中

是的。 与给定表格关联的数据页可在多个数据文件中出现，它们均属于相同文件组。 SQL Server 使用按[比例填充策略](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy)将数据分发到数据文件。

## <a name="data-migration-questions"></a>数据迁移问题

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>能否将现有 Azure SQL 数据库迁移到“超大规模”服务层级

是的。 可以将现有 Azure SQL 数据库迁移到“超大规模”服务层级。 这是一种单向迁移。 无法将数据库从“超大规模”层级移到另一个服务层级。 对于概念证明（Poc），我们建议你创建数据库的副本，并将副本迁移到超大规模。
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>能否将我的超大规模数据库移到其他服务层

不能。 目前，无法将超大规模数据库迁移到其他服务层级。

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>迁移到“超大规模”服务层级后，是否会丢失一些功能

是的。 一些 Azure SQL 数据库功能在超大规模中不受支持，包括但不限于长期备份保留。 将数据库迁移到“超大规模”服务层级后，这些功能将停止运行。  我们预期这些限制是暂时性的。

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>我可以将我的本地 SQL Server 数据库或 SQL Server 的虚拟机中的数据库移动到超大规模

是的。 你可以使用所有现有迁移技术迁移到超大规模，包括事务复制和任何其他数据移动技术（大容量复制、Azure 数据工厂、Azure Databricks、SSIS）。 另请参阅支持多种迁移方案的[Azure 数据库迁移服务](../dms/dms-overview.md)。

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>在从本地或虚拟机环境迁移到超大规模时，我的停机时间是多少，如何将其最小化

迁移到超大规模的停机时间与将数据库迁移到其他 Azure SQL 数据库服务层时的停机时间相同。 可以使用[事务复制](replication-to-sql-database.md#data-migration-scenario
)尽量减少大小最多几 TB 的数据库的故障时间迁移。 对于非常大的数据库（10 + TB），可以考虑使用 ADF、Spark 或其他数据移动技术迁移数据。

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>需要多长时间才能将 X 数据量引入超大规模

超大规模能够消耗 100 MB/s 的新数据/更改数据，但将数据移入 Azure SQL 数据库所需的时间也会受到可用网络吞吐量、源读取速度和目标数据库服务级别目标的影响。

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>能否从 blob 存储读取数据并进行快速加载（如 SQL 数据仓库中的 Polybase）

可以让客户端应用程序从 Azure 存储中读取数据，并将数据加载加载到超大规模数据库中（就像在任何其他 Azure SQL 数据库中一样）。 Azure SQL Database 当前不支持 Polybase。 作为提供快速负载的替代方法，你可以使用[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/)，或在[SQL 的 spark 连接器](sql-database-spark-connector.md) [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/)中使用 spark 作业。 SQL 的 Spark 连接器支持批量插入。

还可以使用 BULK INSERT 或 OPENROWSET 批量从 Azure Blob 存储区中读取数据：[对 Azure Blob 存储中的数据进行大容量访问的示例](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)。

“超大规模”数据库中不支持简单恢复或批量日志记录模式。 需要完整恢复模式才能提供高可用性和时间点恢复。 不过，与其他 Azure SQL 数据库服务层相比，超大规模日志体系结构提供更好的数据引入速率。

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>超大规模允许为大量数据并行引入预配多个节点

不能。 超大规模是一种对称的多处理（SMP）体系结构，不是大规模并行处理（MPP）或多主机体系结构。 只能创建多个副本来横向扩展只读工作负荷。

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>支持迁移到超大规模的最早 SQL Server 版本是什么

SQL Server 2005。 有关详细信息，请参阅[迁移到单一数据库或共用数据库](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database)。 对于兼容性问题，请参阅[解决数据库迁移的兼容性问题](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)。

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>超大规模支持从其他数据源（如 Amazon Aurora、MySQL、PostgreSQL、Oracle、DB2 和其他数据库平台）进行迁移

是的。 [Azure 数据库迁移服务](../dms/dms-overview.md)支持许多迁移方案。

## <a name="business-continuity-and-disaster-recovery-questions"></a>业务连续性和灾难恢复问题

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>为超大规模数据库提供了哪些 Sla

请参阅[AZURE SQL 数据库的 SLA](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/)。 对于具有两个或更多辅助计算副本的数据库，其他辅助计算副本可提高可用性，最高可达99.99%。

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Azure SQL 数据库服务是否托管我的数据库备份

是的。

### <a name="how-often-are-the-database-backups-taken"></a>创建数据库备份的频率

没有用于超大规模数据库的传统完整备份、差异备份和日志备份。 相反，有数据文件的常规存储快照。 生成的日志在配置的保留期内只保留原样，允许还原到保留期内的任意时间点。

### <a name="does-hyperscale-support-point-in-time-restore"></a>超大规模是否支持时间点还原

是的。

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>在超大规模中，数据库还原的恢复点目标（RPO）/Recovery 时间目标（RTO）是什么

RPO 为0分钟。不管数据库大小如何，RTO 目标不到10分钟。 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>大型数据库的备份是否影响主计算节点的计算性能

不能。 备份由存储子系统管理，并利用存储快照。 它们不会影响主副本上的用户工作负荷。

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>能否对超大规模数据库执行异地还原

是的。  完全支持异地还原。

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>能否通过超大规模数据库设置异地复制

现在不行。

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>能否进行超大规模数据库备份并将其还原到本地服务器或 VM 中 SQL Server

不能。 超大规模数据库的存储格式与 SQL Server 的任何发布版本不同，你不会控制备份，也不能访问它们。 若要将数据移出超大规模数据库，可以使用任何数据移动技术（即 Azure 数据工厂、Azure Databricks、SSIS 等）提取数据。

## <a name="cross-feature-questions"></a>跨功能问题

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>迁移到“超大规模”服务层级后，是否会丢失一些功能

是的。 一些 Azure SQL 数据库功能在超大规模中不受支持，包括但不限于长期备份保留。 将数据库迁移到“超大规模”服务层级后，这些功能将停止运行。

### <a name="will-polybase-work-with-hyperscale"></a>Polybase 将与超大规模配合使用

不能。 Azure SQL 数据库中不支持 Polybase。

### <a name="does-hyperscale-have-support-for-r-and-python"></a>超大规模是否支持 R 和 Python

不能。 Azure SQL 数据库中不支持 R 和 Python。

### <a name="are-compute-nodes-containerized"></a>计算节点容器化

不能。 超大规模进程在[Service Fabric](https://azure.microsoft.com/services/service-fabric/)节点（vm）上运行，而不是在容器中运行。

## <a name="performance-questions"></a>性能问题

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>可以在超大规模数据库中推送多少写入吞吐量

对于任何超大规模计算大小，事务日志吞吐量限制设置为 100 MB/s。 达到此速率的能力取决于多个因素，包括但不限于工作负荷类型、客户端配置，并在主计算副本上具有足够的计算能力，以便以该速率生成日志。

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>对最大计算有多少 IOPS

IOPS 和 IO 延迟根据工作负荷模式而异。 如果要访问的数据缓存在计算副本上，你将看到与本地 SSD 相同的 IO 性能。

### <a name="does-my-throughput-get-affected-by-backups"></a>吞吐量是否受备份影响

不能。 计算与存储层分离。 这消除了备份对性能的影响。

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>我的吞吐量在预配其他计算副本时受到影响

由于存储是共享的，并且在主副本和辅助计算副本之间没有直接的物理复制，因此在技术上，主副本的吞吐量不会受添加辅助副本的影响。 但是，我们可能会限制持续的主动写入工作负荷，以允许在辅助副本和页面服务器上应用日志，并避免辅助副本上的读取性能不佳。

## <a name="scalability-questions"></a>可伸缩性问题

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>扩展和缩减计算副本需要多长时间

无论数据大小如何，纵向扩展或缩减计算节点都需要 5 到 10 分钟时间。

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>进行纵向扩展/缩减操作时，数据库是否处于脱机状态

不能。 纵向扩展/缩减操作为联机操作。

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>缩放操作过程中，连接是否会断开

向上或向下扩展会导致在缩放操作结束时，将删除现有连接。 添加辅助副本不会导致连接断开。

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>计算副本是否自动扩展或最终用户触发操作

是由最终用户触发的。 不是自动的。  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>随着计算的增加，`tempdb` 数据库的大小也会增长

是的。 当计算增长时，`tempdb` 数据库将自动向上扩展。  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>能否预配多个主要计算副本（例如多主系统），其中多个主要计算头可以驱动更高的并发级别

不能。 只有主计算副本接受读/写请求。 辅助计算副本仅接受只读请求。

## <a name="read-scale-out-questions"></a>阅读横向扩展问题

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>可以预配多少个辅助计算副本

默认情况下，我们为超大规模数据库创建一个辅助副本。 如果要调整副本的数量，可以使用[Azure 门户](https://portal.azure.com)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)来执行此操作。

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>如何实现连接到这些辅助计算副本

你可以通过将连接字符串上的 `ApplicationIntent` 参数设置为 `ReadOnly`来连接到这些附加的只读计算副本。 标记有 `ReadOnly` 的任何连接会自动路由到其他只读计算副本之一。  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>如何实现验证是否已使用 SSMS 或其他客户端工具成功连接到辅助计算副本？

可以执行以下 T-sql 查询： `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`。
如果连接到只读的辅助副本，则会 `READ_ONLY` 结果，如果连接到主副本，则 `READ_WRITE`。 请注意，必须将数据库上下文设置为超大规模数据库的名称，而不是设置为 `master` 数据库的名称。

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>能否为读取横向扩展副本创建专用终结点

不能。 您只能通过指定 `ApplicationIntent=ReadOnly`连接到读取横向扩展副本。

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>系统是否对读取工作负荷进行智能负载均衡

不能。 具有只读意向的连接将重定向到任意读取横向扩展副本。

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>能否独立于主副本扩展/缩减辅助计算副本

不能。 辅助计算副本也用作高可用性故障转移目标，因此它们需要与主副本具有相同的配置，以便在故障转移后提供预期的性能。

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>我是否获取了主计算和其他辅助计算副本的不同 `tempdb` 大小调整

不能。 根据计算大小预配配置 `tempdb` 数据库，辅助计算副本的大小与主计算的大小相同。

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>能否在辅助计算副本上添加索引和视图

不能。 超大规模数据库具有共享存储，这意味着所有计算副本都可以看到相同的表、索引和视图。 如果要为辅助数据库上的读取优化更多的索引，则必须将其添加到主副本上。

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>主要和辅助计算副本之间存在多少延迟

在主副本上提交事务时，根据当前日志生成速率，可以是瞬时的，也可以是低毫秒。

## <a name="next-steps"></a>后续步骤

有关“超大规模”服务层级的详细信息，请参阅[“超大规模”服务层级](sql-database-service-tier-hyperscale.md)。
