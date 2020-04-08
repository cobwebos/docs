---
title: Azure SQL 数据库资源限制 | Microsoft Docs
description: 本文概述了使用弹性池的单一数据库和弹性池的 Azure SQL 数据库资源限制。 它还提供了有关在达到或超过这些资源限制时会发生什么情况的信息。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: afb30a17d7a1450f169402c18f41ce249415e89d
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804820"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>SQL 数据库资源限制和资源治理

本文概述了管理单一数据库和弹性池的 SQL 数据库服务器的 SQL 数据库资源限制。 它提供有关命中或超出这些资源限制时会发生什么情况的信息，并描述了用于强制实施这些限制的资源治理机制。

> [!NOTE]
> 有关托管实例限制，请参阅[托管实例的 SQL 数据库资源限制](sql-database-managed-instance-resource-limits.md)。

## <a name="maximum-resource-limits"></a>最大资源限制

| 资源 | 限制 |
| :--- | :--- |
| 每个服务器的数据库数 | 5000 |
| 任意区域中每个订阅的服务器默认数量 | 20 |
| 任意区域中每个订阅的服务器数上限 | 200 |  
| 每个服务器的 DTU/eDTU 配额 | 54,000 |  
| 每个服务器/实例的 vCore 配额 | 540 |
| 每个服务器的最大池数 | 受限于 DTU 或 vCore 数。 例如，如果每个池是 1000 个 DTU，则一个服务器可以支持 54 个池。|
|||

> [!IMPORTANT]
> 随着数据库的数量接近每个 SQL 数据库服务器的限制，可能出现以下情况：
>
> - 对主数据库运行查询的延迟增加。  这包括资源利用率统计信息的视图，如 sys.resource_stats。
> - 管理操作和呈现门户视点（涉及枚举服务器中的数据库）的延迟增加。

> [!NOTE]
> 要获取比默认数量更多的 DTU/eDTU 配额、vCore 配额或更多服务器，请在 Azure 门户中提交新的支持请求。 有关详细信息，请参阅请求[Azure SQL 数据库的配额增加](quota-increase-request.md)。

### <a name="storage-size"></a>存储大小

对于单一数据库资源存储大小，请参阅[基于 DTU 的资源限制](sql-database-dtu-resource-limits-single-databases.md)或[基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md)，了解每个定价层的存储大小限制。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>如果达到数据库资源限制，会发生什么？

### <a name="compute-dtus-and-edtus--vcores"></a>计算（DTU 和 eDTU/vCore）

当数据库计算利用率（由 DT 和 eDUS 或 vCore 来衡量）变得高时，查询延迟会增加，查询甚至会超时。在这些情况下，查询可能由服务排队，并在资源空闲时提供执行资源。
计算使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的计算大小，为数据库提供更多计算资源。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 优化查询以减少每个查询的资源利用率。 有关详细信息，请参阅[查询优化/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>存储

当使用的数据库空间到达上限时，将无法进行增加数据大小的数据库插入和更新操作，客户端会收到[错误消息](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)。 SELECT 和 DELETE 语句不受影响。

空间使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的大小上限，或添加更多存储资源。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 如果数据库在弹性池内，那么可选择将数据库移出弹性池，从而避免与其他数据库共享存储空间。
- 收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>会话和辅助角色（请求）

会话和辅助工作的最大数量由服务层和计算大小（DTUs/eDT或 vCore）决定。 当到达会话或辅助角色上限时，新的请求将被拒绝，客户端将收到错误消息。 虽然应用程序可以轻松地控制可用的连接数，但并行辅助角色数通常更难以估计和控制。 在负荷高峰期，当数据库资源达到上限，辅助角色由于查询运行时间较长、阻塞链大或查询并行度过高而堆积时，这种情况尤其突出。

会话或辅助角色使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的服务层级或计算大小。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 如果争用计算资源造成了辅助角色使用率上升，请优化查询，以降低每项查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。
- 减小[MAXDOP（](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)最大并行度）设置。
- 优化查询工作负载，以减少查询阻塞的次数和持续时间。

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>按用户工作负载和内部进程消耗资源

每个数据库中按用户工作负载进行的 CPU 和内存消耗报告在[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)和[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) `avg_cpu_percent`视图`avg_memory_usage_percent`、中和列中。 对于弹性池，池级资源消耗报告在[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)视图中。 用户工作负载 CPU 消耗也会通过`cpu_percent`Azure 监视器指标报告，用于池级别的[单个数据库](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)和[弹性池](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)。

Azure SQL 数据库需要计算资源来实现核心服务功能，如高可用性和灾难恢复、数据库备份和恢复、监视、查询存储、自动调优等。系统使用[资源治理](#resource-governance)机制为这些内部进程预留了一定有限部分的资源，使其余资源可用于用户工作负荷。 当内部进程不使用计算资源时，系统会向用户工作负载提供这些资源。

在[系统dm_db_resource_stats和sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)视图、输入[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current)`avg_instance_cpu_percent`和`avg_instance_memory_percent`列中报告承载单个数据库或弹性池的 SQL Server 实例上的用户工作负载和内部进程的总 CPU 和内存消耗。 此数据还通过`sqlserver_process_core_percent`和`sqlserver_process_memory_percent`Azure 监视器指标报告池级别的[单个数据库](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)和[弹性池](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)。

[系统dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)和[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)视图中报告了按用户工作负载和内部进程划分的最新资源消耗的更详细细分。 有关这些视图中引用的资源池和工作负载组的详细信息，请参阅[资源治理](#resource-governance)。 这些视图报告用户工作负荷的资源利用率以及关联资源池和工作负荷组中的特定内部进程。

在性能监视和故障排除的上下文中，考虑**用户 CPU 消耗**`avg_cpu_percent` `cpu_percent`（、 ） 以及用户工作负载和内部进程 （） 的总 CPU`avg_instance_cpu_percent``sqlserver_process_core_percent`**消耗**（）非常重要。

**用户 CPU 消耗**量按每个服务目标中用户工作负载限制的百分比计算。 **100% 的用户 CPU 利用率**表示用户工作负载已达到服务目标的限制。 但是，当**总 CPU 消耗**率达到 70-100% 范围时，即使报告的**用户 CPU 消耗**率仍明显低于 100%，用户工作负载吞吐量也会展平，查询延迟也会增加。 当使用较小的服务目标，适度分配计算资源，但用户工作负载相对密集时，就会发生这种情况，例如[在密集的弹性池](sql-database-elastic-pool-resource-management.md)中。 当内部进程暂时需要额外的资源时，例如创建数据库的新副本时，也会发生较小的服务目标。"

当**总 CPU 消耗**量高时，缓解选项与前面所述选项相同，包括服务目标增加和/或用户工作负载优化。

## <a name="resource-governance"></a>资源调控

为了实施资源限制，Azure SQL 数据库使用基于 SQL Server[资源调控器](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)的资源治理实现，该实现进行了修改并扩展，以在 Azure 中运行 SQL Server 数据库服务。 在服务中的每个 SQL Server 实例上，有多个[资源池](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool)和[工作负载组](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)，在池和组级别设置资源限制，以提供[平衡的数据库即服务](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)。 用户工作负荷和内部工作负荷分为单独的资源池和工作负载组。 主副本和可读辅助副本（包括地理副本）上的用户工作负荷被分类到`SloSharedPool1`资源池和`UserPrimaryGroup.DBId[N]`工作负载组中，其中`N`代表数据库 ID 值。 此外，还有多个资源池和工作负载组用于各种内部工作负荷。

除了使用资源调控器来管理 SQL Server 进程中的资源外，Azure SQL 数据库还使用 Windows[作业对象](https://docs.microsoft.com/windows/win32/procthread/job-objects)进行进程级资源治理，使用 Windows[文件服务器资源管理器 （FSRM）](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview)进行存储配额管理。

Azure SQL 数据库资源治理本质上是分层的。 从上到下，使用操作系统资源治理机制和资源调控器在操作系统资源治理机制和资源调控器在操作系统级别和存储卷级别实施限制，然后在资源池级别使用资源调控器，然后在使用资源调控器在工作负载组级别实施限制。 当前数据库或弹性池有效的资源治理限制在[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)视图中显示。 

### <a name="data-io-governance"></a>数据 IO 治理

数据 IO 治理是 Azure SQL 数据库中用于限制数据库数据文件的读取和写入物理 IO 的过程。 为每个服务级别设置 IOPS 限制，以尽量减少"嘈杂邻居"效果，在多租户服务中提供资源分配公平性，并保持在底层硬件和存储的功能范围内。

对于单个数据库，工作负载组限制应用于针对数据库的所有存储 IO，而资源池限制适用于同一 SQL Server 实例上的所有数据库（包括`tempdb`数据库）上的所有存储 IO。 对于弹性池，工作负载组限制适用于池中的每个数据库，而资源池限制适用于整个弹性池，包括`tempdb`在池中的所有数据库之间共享的数据库。 通常，针对数据库（单个或池）的工作负载可能无法实现资源池限制，因为工作负载组限制低于资源池限制，并且更快地限制 IOPS/吞吐量。 但是，组合工作负载可能会针对同一 SQL Server 实例上的多个数据库达到池限制。

例如，如果查询生成 1000 IOPS 而不进行任何 IO 资源治理，但工作负载组的最大 IOPS 限制设置为 900 IOPS，则查询将无法生成超过 900 个 IOPS。 但是，如果资源池的最大 IOPS 限制设置为 1500 IOPS，并且与资源池关联的所有工作负荷组的总 IO 超过 1500 IOPS，则同一查询的 IO 可能会减少到 900 IOPS 的工作组限制之下。

[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)视图返回的 IOPS 和吞吐量最小/最大值充当限制/上限，而不是保证。 此外，资源治理不保证任何特定的存储延迟。 给定用户工作负载的最佳可实现延迟、IOPS 和吞吐量不仅取决于 IO 资源治理限制，还取决于使用的 IO 大小组合以及基础存储的功能。 SQL Server 使用大小在 512 KB 和 4 MB 之间不同的 I。 为了实施 IOPS 限制，除 Azure 存储中具有数据文件的数据库外，都会考虑每个 IO 的大小。 在这种情况下，大于 256 KB 的 IO 将记为多个 256 KB IO，以便与 Azure 存储 IO 核算保持一致。

对于在 Azure 存储中使用数据文件的基本、标准和通用数据库，如果数据库没有足够的数据`primary_group_max_io`文件来累积提供此数量的 IOPS，或者如果数据在文件之间分布不均匀，或者如果基础 blob 的性能层将 IOPS/吞吐量限制在资源治理限制以下，则可能无法实现该值。 同样，由于基础 Azure 存储 Blob 上的 IOPS 限制，`primary_max_log_rate`因此工作负荷可能无法实现小日志 I。

在[sys.dm_db_resource_stats、sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)和[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)视图中报告的资源利用率值（如[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)`avg_data_io_percent`和`avg_log_write_percent`）按最大资源治理限制的百分比计算。 因此，当资源治理以外的因素限制 IOPS/吞吐量时，即使报告的资源利用率保持在 100% 以下，IOPS/吞吐量也会随着工作负载的增加而展平和延迟增加。 

要查看每个数据库文件的读取和写入 IOPS、吞吐量和延迟，请使用[sys.dm_io_virtual_file_stats（）](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)函数。 此函数针对数据库显示所有 IO，包括未计入`avg_data_io_percent`的背景 IO，而是使用 IOPS 和基础存储的吞吐量，并可能影响观察到的存储延迟。 该函数还显示 IO 资源治理可能为读取和写入分别引入`io_stall_queued_read_ms``io_stall_queued_write_ms`的额外延迟。

### <a name="transaction-log-rate-governance"></a>事务日志速率调控

事务日志速率调控是 Azure SQL 数据库中的一个进程，用于限制批量插入、SELECT INTO 和索引生成等工作负荷的高引入速率。 无论针对数据文件发出多少 IO，系统都会对日志记录生成速率跟踪并实施这些限制，使其保持在亚秒级以下，并限制吞吐量。  目前，事务日志生成速率会线性提高到与硬件相关的临界点，在 vCore 购买模型中，允许的最大日志速率为 96 MB/秒。 

> [!NOTE]
> 向事务日志文件发出的实际物理 IO 不会受到调控或限制。

日志速率的设置应该做到可在各种场合下实现并保持该速率，同时，整个系统可以在尽量减轻对用户负载造成的影响的前提下保持其功能。 日志速率调控可确保事务日志备份保留在已发布的可恢复性 SLA 范围内。  这种调控还可以防止次要副本带来过多的积压工作。

生成日志记录后，将评估每个操作，以确定是否要将其延迟，从而保持最大所需日志速率（MB/秒）。 将日志记录刷新到存储时不会增大延迟，日志速率调控是在日志速率生成期间应用的。

在运行时实施的实际日志生成速率还可能受到反馈机制（暂时降低允许的日志速率，使系统保持稳定）的影响。 日志文件空间管理可避免遇到日志空间不间的情况，可用性组复制机制可以暂时降低总体系统限制。

日志速率调速器流量整形通过以下等待类型（在[sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)和[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)视图中公开） 浮出水面）：

| Wait 类型 | 说明 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 数据库限制 |
| POOL_LOG_RATE_GOVERNOR | 池限制 |
| INSTANCE_LOG_RATE_GOVERNOR | 实例级限制 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 反馈控制。高级/业务关键型工作负荷中的可用性组物理复制不会保持 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 反馈控制。限制速率可以避免出现日志空间不足的情况 |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | 异地复制反馈控制，限制日志速率，以避免高数据延迟和异地秒数不可用|
|||

当日志速率限制阻碍实现所需的可伸缩性时，请考虑以下选项：
- 向上扩展到更高的服务级别，以获得最大 96 MB/s 的日志速率，或切换到其他服务层。 [超大规模](sql-database-service-tier-hyperscale.md)服务层提供 100 MB/s 的日志速率，而不考虑所选服务级别。
- 如果加载的数据是暂时性的（例如 ETL 过程中的暂存数据），可将其载入 tempdb（记录最少量的数据）。 
- 对于分析方案，可将数据载入聚集列存储涵盖的表中。 这样，可以通过压缩来降低所需的日志速率。 此方法确实会增大 CPU 利用率，并且仅适用于可从聚集列存储索引受益的数据集。 

## <a name="next-steps"></a>后续步骤

- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 有关 DTU 和 eDTU 的信息，请参阅 [DTU 和 eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)。
- 有关 tempdb 大小限制的信息，请参阅 [Azure SQL 数据库中的 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。
