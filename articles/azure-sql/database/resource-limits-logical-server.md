---
title: Azure 中逻辑服务器的资源限制
description: 本文概述了 Azure SQL 数据库和 Azure Synapse Analytics 所使用的 Azure 中逻辑服务器的资源限制。 它还提供了有关在达到或超过这些资源限制时会发生什么情况的信息。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 09/15/2020
ms.openlocfilehash: 32c5135629bf56645ea39a8eee991de3cae6dca0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325328"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Azure SQL 数据库和 Azure Synapse Analytics 服务器的资源限制
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文概述了 Azure SQL 数据库和 Azure Synapse Analytics 所使用的逻辑服务器的资源限制。 它提供有关在达到或超过这些资源限制时所发生情况的信息，并描述用于强制执行这些限制的资源调控机制。

> [!NOTE]
> 有关 Azure SQL 托管实例限制，请参阅[托管实例的 SQL 数据库资源限制](../managed-instance/resource-limits.md)。

## <a name="maximum-resource-limits"></a>最大资源限制

| 资源 | 限制 |
| :--- | :--- |
| 每个服务器的数据库数 | 5000 |
| 任意区域中每个订阅的服务器默认数量 | 20 个 |
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
> 若要获取更高的 DTU/eDTU 配额、vCore 配额或超过默认数量的服务器，请在 Azure 门户中提交新的支持请求。 有关详细信息，请参阅 [AZURE SQL 数据库的请求配额增加](quota-increase-request.md)。

### <a name="storage-size"></a>存储大小

对于单一数据库资源存储大小，请参阅[基于 DTU 的资源限制](resource-limits-dtu-single-databases.md)或[基于 vCore 的资源限制](resource-limits-vcore-single-databases.md)，了解每个定价层的存储大小限制。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>如果达到数据库资源限制，会发生什么？

### <a name="compute-cpu"></a>计算 CPU

当数据库计算 CPU 使用率变高时，查询延迟也会增加，查询甚至可能超时。在上述情况下，服务可能对查询排队，并在资源可用时向查询提供资源以用于执行。
计算使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的计算大小，为数据库提供更多计算资源。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 优化查询，以降低每个查询的 CPU 资源利用率。 有关详细信息，请参阅[查询优化/提示](performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>存储

当使用的数据库空间到达上限时，将无法进行增加数据大小的数据库插入和更新操作，客户端会收到[错误消息](troubleshoot-common-errors-issues.md)。 SELECT 和 DELETE 语句不受影响。

空间使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的大小上限，或添加更多存储资源。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 如果数据库在弹性池内，可选择将数据库移出弹性池，从而避免与其他数据库共享存储空间。
- 收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](file-space-manage.md)

### <a name="sessions-and-workers-requests"></a>会话和辅助角色（请求）

会话和辅助角色的数目上限由服务层级和计算大小（DTU/eDTU 或 vCore）决定。 当到达会话或辅助角色上限时，新的请求将被拒绝，客户端将收到错误消息。 虽然应用程序可以轻松地控制可用的连接数，但并行辅助角色数通常更难以估计和控制。 在负荷高峰期，当数据库资源达到上限，辅助角色由于查询运行时间较长、阻塞链大或查询并行度过高而堆积时，这种情况尤其突出。

会话或辅助角色使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的服务层级或计算大小。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 如果争用计算资源造成了辅助角色使用率上升，请优化查询，以降低每项查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](performance-guidance.md#query-tuning-and-hinting)。
- 减小 [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines)（最大并行度）设置。
- 优化查询工作负荷，以减少查询受阻的发生次数和持续时间。

### <a name="memory"></a>内存

与其他资源（CPU、辅助角色、存储）不同，达到内存限制不会对查询性能产生负面影响，也不会导致错误和失败。 正如[内存管理体系结构指南](https://docs.microsoft.com/sql/relational-databases/memory-management-architecture-guide)中详细描述的那样，SQL Server 数据库引擎通常使用所有可用内存，这是设计使然。 内存主要用于缓存数据，以避免更昂贵的存储访问。 因此，较高的内存利用率通常会提高查询性能，因为从内存中读取的速度更快，而从存储中读取的速度更慢。

在数据库引擎启动之后，当工作负载开始从存储中读取数据时，数据库引擎会积极地在内存中缓存数据。 经过这一初始增长期之后，通常可看到 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 中的 `avg_memory_usage_percent` 和 `avg_instance_memory_percent` 列接近或等于 100%，尤其是对于非空闲且不能完全装入内存的数据库。

除了数据缓存之外，内存还用于数据库引擎的其他组件。 当有内存需求且所有可用内存已被数据缓存占用时，数据库引擎将动态收缩数据缓存大小以使内存可供其他组件使用，并在其他组件释放内存时动态增加数据缓存。

在极少数情况下，要求十分高的工作负载可能会导致内存不足，从而导致内存不足错误。 这可能发生在内存使用率介于 0% 和 100% 之间的任何级别。 这更有可能出现在比例较小的内存限制和/或工作负荷使用更多内存进行查询处理的较小计算大小上，如 [密集弹性池中](elastic-pool-resource-management.md)。

遇到内存不足错误时，缓解选项包括：
- 提高数据库或弹性池的服务层级或计算大小。 请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。
- 优化查询和配置以降低内存使用率。 下表中介绍了常见的解决方案。

|解决方案|说明|
| :----- | :----- |
|减少内存授予的大小|有关内存授予的详细信息，请参阅[了解 SQL Server 内存授予](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595)博客文章。 避免过大内存授予的一个常见解决方案是使[统计信息](https://docs.microsoft.com/sql/relational-databases/statistics/statistics)保持最新状态。 这可使查询引擎更准确地估计内存消耗，从而避免内存授予过大。</br></br>在使用兼容级别 140 及更高级别的数据库中，数据库引擎可使用[批处理模式内存授予反馈](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback)自动调整内存授予大小。 在使用兼容级别 150 及更高级别的数据库中，数据库引擎类似地使用[行模式内存授予反馈](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)，用于更常见的行模式查询。 此内置功能有助于避免由于内存授予过大而导致的内存不足错误。|
|减小查询计划缓存的大小|数据库引擎在内存中缓存查询计划，以避免为每次查询执行编译查询计划。 若要避免由于仅使用一次的缓存计划而导致的查询计划缓存膨胀，请启用 OPTIMIZE_FOR_AD_HOC_WORKLOADS [数据库范围内的配置](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)。|
|减小锁定内存的大小|数据库引擎将内存用于[锁定](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine)。 如果可能，请避免可能获取大量锁定并导致高锁定内存消耗的大型事务。|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>用户工作负荷和内部进程的资源消耗量

将在 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 视图的 `avg_cpu_percent` 和 `avg_memory_usage_percent` 列中报告每个数据库中的用户工作负荷的 CPU 和内存消耗量。 对于弹性池，将在 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中报告池级别的资源消耗量。 对于池级别的[单一数据库](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)和[弹性池](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)，还会通过 Azure Monitor 指标 `cpu_percent` 报告用户工作负荷的 CPU 消耗量。

Azure SQL Database 需要计算资源来实现核心服务功能，例如高可用性和灾难恢复、数据库备份和还原、监视、查询存储、自动优化等。系统使用 [资源调控](#resource-governance) 机制为这些内部过程留出一定部分的总体资源，从而使资源的剩余部分可用于用户工作负荷。 当内部进程不使用计算资源时，系统会将其提供给用户工作负载使用。

将在 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 和 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 视图的 `avg_instance_cpu_percent` 和 `avg_instance_memory_percent` 列中报告用户工作负载和内部进程的总 CPU 和内存消耗量。 对于池级别的[单一数据库](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases)和[弹性池](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)，还会通过 Azure Monitor 指标 `sqlserver_process_core_percent` 和 `sqlserver_process_memory_percent` 报告此数据。

[Sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)和[dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)视图中报告了用户工作负荷和内部进程的最近资源消耗的详细细目。 有关这些视图中引用的资源池和工作负荷组的详细信息，请参阅 [资源调控](#resource-governance)。 这些视图按用户工作负荷和关联资源池和工作负荷组中的特定内部过程报告资源利用率。

在性能监视和故障排除上下文中，必须考虑用户 CPU 消耗量（`avg_cpu_percent`、`cpu_percent`），以及用户工作负载和内部进程的 CPU 总消耗量（`avg_instance_cpu_percent`、`sqlserver_process_core_percent`） 。

用户 CPU 消耗量的计算值为每个服务目标中用户工作负荷限制的一个百分比。 用户 CPU 利用率为 100% 表示用户工作负荷达到了服务目标的限制。 但是，当 CPU 总消耗量达到 70-100% 范围时，即使报告的用户 CPU 消耗量明显低于 100%，也可能会看到用户工作负载吞吐量保持平稳，但查询延迟增大 。 当使用较小的服务目标同时进行计算资源的中等分配，而不是相对重要的用户工作负荷（如 [密集弹性池](elastic-pool-resource-management.md)）时，更有可能出现这种情况。 当内部进程临时需要更多的资源时（例如，在创建数据库的新副本时），使用较小的服务目标也可能发生这种情况。

当 CPU 总消耗量较高时，缓解措施与前面所述相同，也包括增大服务目标和/或优化用户工作负载。

## <a name="resource-governance"></a>资源调控

为了强制资源限制，Azure SQL Database 使用基于 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)、已修改和扩展的资源调控实现在 Azure SQL 数据库中运行。 在 SQL 数据库中，多个 [资源池](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) 和 [工作负荷组](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)，同时将资源限制设置为池和组级别，提供了一个 [平衡的数据库即服务](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)。 用户工作负荷和内部工作负荷归类为单独的资源池和工作负荷组。 主副本和可读辅助副本上的用户工作负荷（包括异地副本）归类为 `SloSharedPool1` 资源池和 `UserPrimaryGroup.DBId[N]` 工作负荷组，其中 `N` 代表数据库 ID 值。 此外，还有多个资源池和工作负荷组用于各种内部工作负荷。

除了使用 Resource Governor 来控制 SQL 进程中的资源，Azure SQL 数据库还使用 Windows [作业对象](https://docs.microsoft.com/windows/win32/procthread/job-objects) 来实现进程级别的资源调控，并使用 Windows [文件服务器资源管理器 (FSRM) ](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) 进行存储配额管理。

Azure SQL 数据库资源调控本质上是分层的。 从上到下，将使用操作系统资源调控机制并 Resource Governor，并使用 Resource Governor，然后在使用 Resource Governor 的工作负荷组级别，在操作系统级别和存储卷级别强制实施限制。 当前数据库或弹性池生效的资源调控限制显示在 [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 视图中。

### <a name="data-io-governance"></a>数据 IO 管理

数据 IO 管理是 Azure SQL 数据库中的一个过程，用于限制对数据库的数据文件的读取和写入物理 IO。 为每个服务级别设置 IOPS 限制，以最大程度地减少 "干扰邻居" 的效果，在多租户服务中提供资源分配公平，并保持在底层硬件和存储的功能中。

对于单一数据库，工作负荷组限制适用于数据库的所有存储 IO，而资源池限制适用于同一 SQL 池中的所有数据库（包括数据库）的所有存储 IO `tempdb` 。 对于弹性池，工作负荷组限制适用于池中的每个数据库，而资源池限制适用于整个弹性池，包括 `tempdb` 数据库，该数据库在池中的所有数据库之间共享。 通常情况下，工作负荷可能无法根据 (单个或共用) 的数据库来实现资源池限制，因为工作负荷组限制比资源池限制更少，并且更快地限制 IOPS/吞吐量。 但是，对于同一池上的多个数据库，合并工作负荷可能会达到池限制。

例如，如果查询在没有任何 IO 资源调控的情况下生成 1000 IOPS，但工作负荷组的最大 IOPS 限制设置为 900 IOPS，则查询将无法生成超过900的 IOPS。 但是，如果将 "资源池最大 IOPS 限制" 设置为 1500 IOPS，并且与资源池关联的所有工作负荷组的 IO 总数超过 1500 IOPS，则相同查询的 IO 可能会降低到最大的工作组限制（以 900 IOPS 为限）。

[Sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)视图返回的 IOPS 和吞吐量最小/最小值可用作限制/上限，而不是保证。 而且，资源调控并不保证任何特定的存储延迟。 给定用户工作负荷最能实现的延迟、IOPS 和吞吐量不仅取决于 IO 资源调控限制，还取决于所使用的 IO 大小和基础存储的功能。 SQL 数据库使用的 Io 大小大小介于 512 KB 到 4 MB 之间。 出于强制 IOPS 限制的目的，将对每个 IO 进行考虑，而不考虑其大小，但 Azure 存储中包含数据文件的数据库除外。 在这种情况下，大于 256 KB 的 IOs 会被视为多个 256-KB Io，以符合 Azure 存储 IO 记帐。

对于 "基本"、"标准" 和 "常规用途" 数据库（使用 Azure 存储中的数据文件）， `primary_group_max_io` 如果数据库没有足够的数据文件来累积来提供此数量的 IOPS，或者如果数据不是在文件之间均匀分布，或者如果基础 blob 的性能层限制了低于资源调控限制的 IOPS/吞吐量，则可能无法实现此值。 同样，对于频繁事务提交生成的小型日志 Io， `primary_max_log_rate` 工作负荷可能无法实现该值，因为基础 Azure 存储 blob 的 IOPS 限制。

`avg_data_io_percent` `avg_log_write_percent` 在[sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、 [resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)和[sys.databases elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)视图中报告的资源利用率值（如和）是以最大资源调控限制的百分比来计算的。 因此，当工作负荷增加时，即使报告的资源利用率低于100%，在其他因素（而不是资源调控限制 IOPS/吞吐量）的情况下，也可以看到 IOPS/吞吐量平展和延迟增加。

若要查看每个数据库文件的读取和写入 IOPS、吞吐量和延迟，请使用 [dm_io_virtual_file_stats ( # B1 ](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 函数。 此函数会对数据库的所有 IO （包括不会进行的后台 IO）进行图面， `avg_data_io_percent` 但会使用基础存储的 IOPS 和吞吐量，并可能影响观察到的存储延迟。 此函数还会在和列中分别为读取和写入提供 IO 资源调控所引入的其他 `io_stall_queued_read_ms` 延迟 `io_stall_queued_write_ms` 。

### <a name="transaction-log-rate-governance"></a>事务日志速率调控

事务日志速率调控是 Azure SQL 数据库中的一个进程，用于限制批量插入、SELECT INTO 和索引生成等工作负荷的高引入速率。 无论针对数据文件发出多少 IO，系统都会对日志记录生成速率跟踪并实施这些限制，使其保持在亚秒级以下，并限制吞吐量。  目前，事务日志生成速率会线性提高到与硬件相关的临界点，在 vCore 购买模型中，允许的最大日志速率为 96 MB/秒。

> [!NOTE]
> 向事务日志文件发出的实际物理 IO 不会受到调控或限制。

日志速率的设置应该做到可在各种场合下实现并保持该速率，同时，整个系统可以在尽量减轻对用户负载造成的影响的前提下保持其功能。 日志速率调控可确保事务日志备份保留在已发布的可恢复性 SLA 范围内。  这种调控还可以防止次要副本带来过多的积压工作。

生成日志记录后，将评估每个操作，以确定是否要将其延迟，从而保持最大所需日志速率（MB/秒）。 将日志记录刷新到存储时，将不会添加延迟，而是在生成日志时应用日志速率调控。

在运行时实施的实际日志生成速率还可能受到反馈机制（暂时降低允许的日志速率，使系统保持稳定）的影响。 日志文件空间管理可避免遇到日志空间不间的情况，可用性组复制机制可以暂时降低总体系统限制。

日志速率调控器流量造型通过以下等待类型显示， (在 [dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 和 [sys.databases dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)) 视图中公开）：

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

- 向上缩放到更高的服务级别，以获取最大 96 MB/秒的对数率，或切换到不同的服务层。 无论选择哪种服务级别， [超大规模](service-tier-hyperscale.md) 服务层提供 100 MB/秒的日志速率。
- 如果加载的数据是暂时性的（例如 ETL 过程中的暂存数据），可将其载入 tempdb（记录最少量的数据）。
- 对于分析方案，可将数据载入聚集列存储涵盖的表中。 这样，可以通过压缩来降低所需的日志速率。 此方法确实会增大 CPU 利用率，并且仅适用于可从聚集列存储索引受益的数据集。

### <a name="storage-space-governance"></a>存储空间调控

在高级和业务关键服务层中，数据和事务日志文件存储在托管数据库或弹性池的计算机的本地 SSD 卷上。 这会提供较高的 IOPS 和吞吐量，并降低 IO 延迟。 此本地卷的大小取决于硬件功能，并是有限的。 在给定计算机上，客户数据库使用本地卷空间，包括 `tempdb` 操作系统、管理软件、监视数据、日志等。创建、删除数据库并增加/减少其空间使用量时，计算机上的本地空间消耗会随着时间的推移而波动。 

如果系统检测到计算机上的可用空间不足，并且数据库或弹性池存在空间不足的风险，它会将数据库或弹性池移到具有足够可用空间的其他计算机上，从而实现最大的配置服务目标大小限制。 这种移动是以联机方式进行的，类似于数据库缩放操作，并且具有类似的 [影响](single-database-scale.md#impact)，其中包括在操作结束时) 故障转移的简短 (秒。 此故障转移会终止打开的连接并回滚事务，这可能会在该时间使用数据库影响应用程序。

由于数据以物理方式复制到另一台计算机上，因此移动较大的数据库可能需要花费大量时间。 在这段时间内，如果大型用户数据库或弹性池的本地空间消耗，或者 `tempdb` 数据库增长速度非常快，则用尽空间的风险会增加。 系统以均衡的方式启动数据库移动，以防出现空间不足的错误，并避免不必要的故障转移。

## <a name="next-steps"></a>后续步骤

- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 有关 DTU 和 eDTU 的信息，请参阅 [DTU 和 eDTU](purchasing-models.md#dtu-based-purchasing-model)。
- 有关 tempdb 大小限制的信息，请参阅 [Azure SQL 数据库中的 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。
 