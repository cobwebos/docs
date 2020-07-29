---
title: Azure 中逻辑服务器的资源限制
description: 本文概述了 azure SQL 数据库和 Azure Synapse Analytics 使用的 Azure 中的逻辑服务器的资源限制。 它还提供了有关在达到或超过这些资源限制时会发生什么情况的信息。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 06/10/2020
ms.openlocfilehash: eac5814eb977a01135ad2fcd9551b3475673dbca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84691724"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Azure SQL 数据库和 Azure Synapse Analytics 服务器的资源限制
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文概述了 Azure SQL 数据库和 Azure Synapse Analytics 使用的逻辑服务器的资源限制。 它提供有关在达到或超过这些资源限制时所发生情况的信息，并描述用于强制执行这些限制的资源调控机制。

> [!NOTE]
> 有关 Azure SQL 托管实例限制，请参阅[托管实例的 SQL 数据库资源限制](../managed-instance/resource-limits.md)。

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
> 随着数据库的数量接近每个服务器的限制，可能出现以下情况：
>
> - 对主数据库运行查询的延迟增加。  这包括资源利用率统计信息的视图，如 sys.resource_stats。
> - 管理操作和呈现门户视点（涉及枚举服务器中的数据库）的延迟增加。

> [!NOTE]
> 若要获取更高的 DTU/eDTU 配额、vCore 配额或超过默认数量的服务器，请在 Azure 门户中提交新的支持请求。 有关详细信息，请参阅[AZURE SQL 数据库的请求配额增加](quota-increase-request.md)。

### <a name="storage-size"></a>存储大小

对于单一数据库资源存储大小，请参阅[基于 DTU 的资源限制](resource-limits-dtu-single-databases.md)或[基于 vCore 的资源限制](resource-limits-vcore-single-databases.md)，了解每个定价层的存储大小限制。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>如果达到数据库资源限制，会发生什么？

### <a name="compute-cpu"></a>计算 CPU

当数据库计算 CPU 利用率变高时，查询延迟会增加，查询甚至可能会超时。在这些情况下，查询可能会通过服务排队，并在资源可用时提供给执行资源。
计算使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的计算大小，为数据库提供更多计算资源。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 优化查询，以减少每个查询的 CPU 资源利用率。 有关详细信息，请参阅[查询优化/提示](performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>存储

当使用的数据库空间到达上限时，将无法进行增加数据大小的数据库插入和更新操作，客户端会收到[错误消息](troubleshoot-common-errors-issues.md)。 SELECT 和 DELETE 语句不受影响。

空间使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的大小上限，或添加更多存储资源。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 如果数据库位于弹性池中，则可以将数据库移到池中，使其存储空间不与其他数据库共享。
- 收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](file-space-manage.md)

### <a name="sessions-and-workers-requests"></a>会话和辅助角色（请求）

会话和辅助角色的数目上限由服务层级和计算大小（DTU/eDTU 或 vCore）决定。 当到达会话或辅助角色上限时，新的请求将被拒绝，客户端将收到错误消息。 虽然应用程序可以轻松地控制可用的连接数，但并行辅助角色数通常更难以估计和控制。 在负荷高峰期，当数据库资源达到上限，辅助角色由于查询运行时间较长、阻塞链大或查询并行度过高而堆积时，这种情况尤其突出。

会话或辅助角色使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的服务层级或计算大小。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 如果争用计算资源造成了辅助角色使用率上升，请优化查询，以降低每项查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](performance-guidance.md#query-tuning-and-hinting)。
- 减小 [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)（最大并行度）设置。
- 优化查询工作负荷，以减少查询受阻的发生次数和持续时间。

### <a name="memory"></a>内存

与其他资源（CPU、辅助角色、存储）不同，达到内存限制并不会对查询性能产生负面影响，并且不会导致错误和失败。 如[内存管理体系结构指南](https://docs.microsoft.com/sql/relational-databases/memory-management-architecture-guide)中所述，SQL Server 数据库引擎通常按设计使用所有可用内存。 内存主要用于缓存数据，以避免更昂贵的存储访问。 因此，更高的内存使用率通常可以提高查询性能，因为从内存中读取的速度更快，而不是从存储中读取。

数据库引擎启动后，当工作负荷开始从存储读取数据时，数据库引擎会主动地将数据缓存在内存中。 在此初始增加了一段时间后，这种情况很常见并应查看 `avg_memory_usage_percent` `avg_instance_memory_percent` [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)中的和列是否接近100%，特别是对于不处于空闲状态且无法完全容纳在内存中的数据库。

除数据缓存外，内存在数据库引擎的其他组件中使用。 如果有内存需求，并且数据缓存已使用了所有可用内存，则数据库引擎将动态收缩数据缓存大小以使内存可用于其他组件，并在其他组件释放内存时动态增加数据缓存。

在极少数情况下，充分要求的工作负荷可能会导致内存不足的情况，导致内存不足错误。 这可能发生在0% 和100% 之间的任何内存利用率级别。 这更有可能出现在比例较小的内存限制和/或工作负荷使用更多内存进行查询处理的较小计算大小上，如[密集弹性池中](elastic-pool-resource-management.md)。

当遇到内存不足错误时，缓解选项包括：
- 提高数据库或弹性池的服务层级或计算大小。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 优化查询和配置以减少内存利用率。 下表中介绍了常见的解决方案。

|解决方案|说明|
| :----- | :----- |
|减小内存授予的大小|有关内存授予的详细信息，请参阅[了解 SQL Server 内存授予](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595)博客文章。 避免过多的内存授予的常见解决方案是使[统计信息](https://docs.microsoft.com/sql/relational-databases/statistics/statistics)保持最新。 这会使查询引擎更准确地估计内存占用量，从而避免不必要的大内存授予。</br></br>在使用兼容级别140和更高版本的数据库中，数据库引擎可以使用[批处理模式内存授予反馈](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?view=sql-server-ver15#batch-mode-memory-grant-feedback)来自动调整内存授予大小。 在使用兼容级别150和更高版本的数据库中，数据库引擎同样使用[行模式内存授予反馈](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?view=sql-server-ver15#row-mode-memory-grant-feedback)，以便进行更常见的行模式查询。 此内置功能有助于避免内存不足的错误，因为不必要的内存授予。|
|减小查询计划缓存的大小|数据库引擎将查询计划缓存在内存中，以避免为每个查询执行编译查询计划。 若要避免由于缓存计划仅使用一次而导致查询计划缓存膨胀，请启用 OPTIMIZE_FOR_AD_HOC_WORKLOADS[数据库范围的配置](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)。|
|减小锁定内存的大小|数据库引擎使用内存进行[锁定](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine)。 如果可能，请避免可能获取大量锁并导致高锁内存占用量的大型事务。|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>用户工作负荷和内部进程的资源消耗量

将在 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) 和 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) 视图的 `avg_cpu_percent` 和 `avg_memory_usage_percent` 列中报告每个数据库中的用户工作负荷的 CPU 和内存消耗量。 对于弹性池，将在 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中报告池级别的资源消耗量。 对于池级别的[单一数据库](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)和[弹性池](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)，还会通过 Azure Monitor 指标 `cpu_percent` 报告用户工作负荷的 CPU 消耗量。

Azure SQL Database 需要计算资源来实现核心服务功能，例如高可用性和灾难恢复、数据库备份和还原、监视、查询存储、自动优化等。系统使用[资源调控](#resource-governance)机制为这些内部过程留出一定部分的总体资源，从而使资源的剩余部分可用于用户工作负荷。 在内部进程不使用计算资源时，系统会将其提供给用户工作负荷。

每个用户工作负荷和内部进程的 CPU 和内存消耗情况都在[sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)和[sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current)视图、 `avg_instance_cpu_percent` 和 `avg_instance_memory_percent` 列中报告。 对于池级别的[单一数据库](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)和[弹性池](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)，还会通过 Azure Monitor 指标 `sqlserver_process_core_percent` 和 `sqlserver_process_memory_percent` 报告此数据。

[Sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)和[dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)视图中报告了用户工作负荷和内部进程的最近资源消耗的详细细目。 有关这些视图中引用的资源池和工作负荷组的详细信息，请参阅[资源调控](#resource-governance)。 这些视图按用户工作负荷和关联资源池和工作负荷组中的特定内部过程报告资源利用率。

在性能监视和故障排除的上下文中，请务必考虑用户的**cpu 消耗**（ `avg_cpu_percent` 、 `cpu_percent` ）和用户工作负荷和内部进程的**总 CPU 消耗**（ `avg_instance_cpu_percent` 、 `sqlserver_process_core_percent` ）。

用户 CPU 消耗量的计算值为每个服务目标中用户工作负荷限制的一个百分比  。 用户 CPU 利用率为 100% 表示用户工作负荷达到了服务目标的限制  。 但是，当**总 CPU 使用率**达到70-100% 范围时，可以看到用户工作负荷吞吐量进行平展和查询延迟增加，即使报告的**用户 CPU 消耗**明显低于100%。 当使用较小的服务目标同时进行计算资源的中等分配，而不是相对重要的用户工作负荷（如[密集弹性池](elastic-pool-resource-management.md)）时，更有可能出现这种情况。 当内部进程临时需要更多的资源时（例如，在创建数据库的新副本时），使用较小的服务目标也可能发生这种情况。

如果**总 CPU 消耗**很高，则缓解选项与前面所述的相同，并且包括服务目标增加和/或用户工作负荷优化。

## <a name="resource-governance"></a>资源调控

为了强制资源限制，Azure SQL Database 使用基于 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)、已修改和扩展的资源调控实现在 Azure SQL 数据库中运行。 在 SQL 数据库中，多个[资源池](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool)和[工作负荷组](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)，同时将资源限制设置为池和组级别，提供了一个[平衡的数据库即服务](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)。 用户工作负荷和内部工作负荷归类为单独的资源池和工作负荷组。 主副本和可读辅助副本上的用户工作负荷（包括异地副本）归类为 `SloSharedPool1` 资源池和 `UserPrimaryGroup.DBId[N]` 工作负荷组，其中 `N` 代表数据库 ID 值。 此外，还有多个资源池和工作负荷组用于各种内部工作负荷。

除了使用 Resource Governor 来控制 SQL 进程中的资源，Azure SQL 数据库还使用 Windows[作业对象](https://docs.microsoft.com/windows/win32/procthread/job-objects)进行进程级别的资源调控，并使用 Windows[文件服务器资源管理器（FSRM）](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview)进行存储配额管理。

Azure SQL 数据库资源调控本质上是分层的。 从上到下，将使用操作系统资源调控机制并 Resource Governor，并使用 Resource Governor，然后在使用 Resource Governor 的工作负荷组级别，在操作系统级别和存储卷级别强制实施限制。 当前数据库或弹性池生效的资源调控限制显示在[sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)视图中。

### <a name="data-io-governance"></a>数据 IO 管理

数据 IO 管理是 Azure SQL 数据库中的一个过程，用于限制对数据库的数据文件的读取和写入物理 IO。 为每个服务级别设置 IOPS 限制，以最大程度地减少 "干扰邻居" 的效果，在多租户服务中提供资源分配公平，并保持在底层硬件和存储的功能中。

对于单一数据库，工作负荷组限制适用于数据库的所有存储 IO，而资源池限制适用于同一 SQL 池中的所有数据库（包括数据库）的所有存储 IO `tempdb` 。 对于弹性池，工作负荷组限制适用于池中的每个数据库，而资源池限制适用于整个弹性池，包括 `tempdb` 数据库，该数据库在池中的所有数据库之间共享。 通常，工作负载对数据库（单个或共用）的工作负荷可能无法实现资源池限制，因为工作负荷组限制低于资源池限制，并更快地限制 IOPS/吞吐量。 但是，对于同一池上的多个数据库，合并工作负荷可能会达到池限制。

例如，如果查询在没有任何 IO 资源调控的情况下生成 1000 IOPS，但工作负荷组的最大 IOPS 限制设置为 900 IOPS，则查询将无法生成超过900的 IOPS。 但是，如果将 "资源池最大 IOPS 限制" 设置为 1500 IOPS，并且与资源池关联的所有工作负荷组的 IO 总数超过 1500 IOPS，则相同查询的 IO 可能会降低到最大的工作组限制（以 900 IOPS 为限）。

[Sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)视图返回的 IOPS 和吞吐量最小/最小值可用作限制/上限，而不是保证。 而且，资源调控并不保证任何特定的存储延迟。 给定用户工作负荷最能实现的延迟、IOPS 和吞吐量不仅取决于 IO 资源调控限制，还取决于所使用的 IO 大小和基础存储的功能。 SQL 数据库使用的 Io 大小大小介于 512 KB 到 4 MB 之间。 出于强制 IOPS 限制的目的，将对每个 IO 进行考虑，而不考虑其大小，但 Azure 存储中包含数据文件的数据库除外。 在这种情况下，大于 256 KB 的 IOs 会被视为多个 256-KB Io，以符合 Azure 存储 IO 记帐。

对于 "基本"、"标准" 和 "常规用途" 数据库（使用 Azure 存储中的数据文件）， `primary_group_max_io` 如果数据库没有足够的数据文件来累积来提供此数量的 IOPS，或者如果数据不是在文件之间均匀分布，或者如果基础 blob 的性能层限制了低于资源调控限制的 IOPS/吞吐量，则可能无法实现此值。 同样，对于频繁事务提交生成的小型日志 Io， `primary_max_log_rate` 工作负荷可能无法实现该值，因为基础 Azure 存储 blob 的 IOPS 限制。

`avg_data_io_percent` `avg_log_write_percent` 在[sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、 [resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)和[sys.databases elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)视图中报告的资源利用率值（如和）是以最大资源调控限制的百分比来计算的。 因此，当工作负荷增加时，即使报告的资源利用率低于100%，在其他因素（而不是资源调控限制 IOPS/吞吐量）的情况下，也可以看到 IOPS/吞吐量平展和延迟增加。

若要查看每个数据库文件的读取和写入 IOPS、吞吐量和延迟，请使用[sys. dm_io_virtual_file_stats （）](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)函数。 此函数会对数据库的所有 IO （包括不会进行的后台 IO）进行图面， `avg_data_io_percent` 但会使用基础存储的 IOPS 和吞吐量，并可能影响观察到的存储延迟。 此函数还会在和列中分别为读取和写入提供 IO 资源调控所引入的其他 `io_stall_queued_read_ms` 延迟 `io_stall_queued_write_ms` 。

### <a name="transaction-log-rate-governance"></a>事务日志速率调控

事务日志速率调控是 Azure SQL 数据库中的一个进程，用于限制批量插入、SELECT INTO 和索引生成等工作负荷的高引入速率。 无论针对数据文件发出多少 IO，系统都会对日志记录生成速率跟踪并实施这些限制，使其保持在亚秒级以下，并限制吞吐量。  目前，事务日志生成速率会线性提高到与硬件相关的临界点，在 vCore 购买模型中，允许的最大日志速率为 96 MB/秒。

> [!NOTE]
> 向事务日志文件发出的实际物理 IO 不会受到调控或限制。

日志速率的设置应该做到可在各种场合下实现并保持该速率，同时，整个系统可以在尽量减轻对用户负载造成的影响的前提下保持其功能。 日志速率调控可确保事务日志备份保留在已发布的可恢复性 SLA 范围内。  这种调控还可以防止次要副本带来过多的积压工作。

生成日志记录后，将评估每个操作，以确定是否要将其延迟，从而保持最大所需日志速率（MB/秒）。 将日志记录刷新到存储时，将不会添加延迟，而是在生成日志时应用日志速率调控。

在运行时实施的实际日志生成速率还可能受到反馈机制（暂时降低允许的日志速率，使系统保持稳定）的影响。 日志文件空间管理可避免遇到日志空间不间的情况，可用性组复制机制可以暂时降低总体系统限制。

日志速率调控器流量造型通过以下等待类型（在[sys. dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)和[dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)视图中公开）进行呈现：

| Wait 类型 | 说明 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 数据库限制 |
| POOL_LOG_RATE_GOVERNOR | 池限制 |
| INSTANCE_LOG_RATE_GOVERNOR | 实例级限制 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 反馈控制。高级/业务关键型工作负荷中的可用性组物理复制不会保持 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 反馈控制。限制速率可以避免出现日志空间不足的情况 |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | 异地复制反馈控制，限制对数率以避免高数据延迟和异地辅助数据库不可用|
|||

当日志速率限制阻碍实现所需的可伸缩性时，请考虑以下选项：

- 向上缩放到更高的服务级别，以获取最大 96 MB/秒的对数率，或切换到不同的服务层。 无论选择哪种服务级别，[超大规模](service-tier-hyperscale.md)服务层提供 100 MB/秒的日志速率。
- 如果加载的数据是暂时性的（例如 ETL 过程中的暂存数据），可将其载入 tempdb（记录最少量的数据）。
- 对于分析方案，可将数据载入聚集列存储涵盖的表中。 这样，可以通过压缩来降低所需的日志速率。 此方法确实会增大 CPU 利用率，并且仅适用于可从聚集列存储索引受益的数据集。

## <a name="next-steps"></a>后续步骤

- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 有关 DTU 和 eDTU 的信息，请参阅 [DTU 和 eDTU](purchasing-models.md#dtu-based-purchasing-model)。
- 有关 tempdb 大小限制的信息，请参阅 [Azure SQL 数据库中的 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。
 