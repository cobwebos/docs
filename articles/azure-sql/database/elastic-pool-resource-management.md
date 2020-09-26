---
title: 密集弹性池中的资源管理
description: 管理包含多个数据库的 Azure SQL 数据库弹性池中的计算资源。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 9674c319cfd51726218e2c6a20197ea15d0ee683
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330717"
---
# <a name="resource-management-in-dense-elastic-pools"></a>密集弹性池中的资源管理
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL 数据库 [弹性池](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) 是一种经济高效的解决方案，用于管理具有不同资源使用情况的多个数据库。 弹性池中的所有数据库共享相同的资源分配，如 CPU、内存、工作线程、存储空间、tempdb，假设 **池中只有池的一部分数据库在任何给定时间都将使用计算资源**。 这种假设允许弹性池具有成本效益。 客户无需支付每个数据库可能需要的所有资源的费用，而是需要为在池中所有数据库之间共享的一组较小的资源付费。

## <a name="resource-governance"></a>资源调控

资源共享要求系统严格控制资源使用情况，以最大程度地减少 "干扰邻居" 的效果，在这种情况下，资源消耗较高的数据库会影响同一弹性池中的其他数据库。 同时，系统必须为高可用性和灾难恢复 (HADR) 、备份和还原、监视、查询存储、自动优化等功能提供充足的资源，才能可靠地运行。

Azure SQL 数据库使用多种资源调控机制实现了这些目标，其中包括用于处理级别资源管理的 Windows [作业对象](https://docs.microsoft.com/windows/win32/procthread/job-objects) 、Windows [文件服务器资源管理器 (FSRM) ](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) 用于存储配额管理，以及用于实现 SQL 数据库中的资源调控的 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 的已修改和扩展版本。

弹性池的主要设计目标是经济高效的。 出于此原因，系统有意允许客户创建 _密集_ 的池，这些池具有接近或达到允许的最大数据库数的池，但有适中的计算资源分配。 出于同一原因，系统不会为其内部进程保留所有可能需要的资源，但允许在内部进程和用户工作负荷之间共享资源。

此方法允许客户使用密集弹性池来实现充足的性能和显著的成本节约。 但是，如果对密集池中的多个数据库的工作负荷足够大，资源争用就会变得很明显。 资源争用可降低用户工作负荷性能，并可能对内部进程产生负面影响。

> [!IMPORTANT]
> 在具有多个活动数据库的密集池中，将池中的数据库数增加到为 [DTU](resource-limits-dtu-elastic-pools.md) 和 [vCore](resource-limits-vcore-elastic-pools.md) 弹性池记录的最大值可能不可行。
>
> 不会导致资源争用和性能问题的数据库数量将取决于并发活动数据库的数量，以及每个数据库中用户工作负荷的资源消耗情况。 此数字随着用户工作负荷的变化而变化。

当密集打包池中发生资源争用时，客户可以选择以下一项或多项操作来缓解此操作：

- 优化查询工作负载，以减少资源消耗，或在一段时间内将资源消耗分散到多个数据库。
- 通过将一些数据库移到另一个池，或使其成为独立数据库来减小池密度。
- 向上缩放池以获取更多资源。

有关如何实现最后两个操作的建议，请参阅本文后面的 [操作建议](#operational-recommendations) 。 减少资源争用会使用户工作负荷和内部进程受益，并使系统能够可靠地维护预期的服务级别。

## <a name="monitoring-resource-consumption"></a>监视资源消耗

若要避免由于资源争用引起的性能下降，使用密集弹性池的客户应主动监视资源消耗，并在增加资源争用开始影响工作负荷时采取及时操作。 持续监视非常重要，因为池中的资源使用情况随时间推移而变化，因为用户工作负荷发生变化、数据卷和分发的更改、池密度中的更改以及 Azure SQL 数据库服务的更改。

Azure SQL 数据库提供了多个与此类监视相关的指标。 超过每个指标的建议平均值表示池中的资源争用，应使用前面提到的其中一项操作解决此问题。

|指标名称|说明|推荐的平均值|
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|与弹性池关联的 SQL 进程的 CPU 使用率，由基础操作系统衡量。 在每个数据库的 [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 视图中提供，在数据库的 [sys.databases. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中提供 `master` 。 此指标还会发出到 Azure Monitor，其中命名为[named](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent` ，可在 Azure 门户中查看。 此值对于同一弹性池中的每个数据库都是相同的。|低于70%。 偶尔会出现最多90% 的短暂的情况。|
|`max_worker_percent`|[工作线程]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) 利用率。 为池中的每个数据库以及池本身提供。 对于数据库级别的工作线程数和池级别存在不同的限制，因此建议同时在这两个级别上监视此指标。 在每个数据库的 [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 视图中提供，在数据库的 [sys.databases. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中提供 `master` 。 此指标还会发出到 Azure Monitor，其中命名为[named](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent` ，可在 Azure 门户中查看。|低于80%。 峰值最大为100% 将导致连接尝试和查询失败。|
|`avg_data_io_percent`|读写物理 IO 的 IOPS 利用率。 为池中的每个数据库以及池本身提供。 在数据库级别的 IOPS 数和池级别上有不同的限制，因此建议同时在这两个级别上监视此指标。 在每个数据库的 [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 视图中提供，在数据库的 [sys.databases. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中提供 `master` 。 此指标还会发出到 Azure Monitor，其中命名为[named](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent` ，可在 Azure 门户中查看。|低于80%。 偶尔会出现最多100% 的短暂的情况。|
|`avg_log_write_percent`|事务日志写入 IO 的吞吐量利用率。 为池中的每个数据库以及池本身提供。 数据库级别的日志吞吐量有不同的限制，在池级别，建议在这两个级别上监视此指标。 在每个数据库的 [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 视图中提供，在数据库的 [sys.databases. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) 视图中提供 `master` 。 此指标还会发出到 Azure Monitor，其中命名为[named](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent` ，可在 Azure 门户中查看。 如果此指标接近100%，则所有数据库修改 (插入、更新、删除、合并语句、SELECT .。。 INTO、BULK INSERT 等，) 速度将更慢。|低于90%。 偶尔会出现最多100% 的短暂的情况。|
|`oom_per_second`|内存不足 (OOM 在弹性池中) 错误，这是内存压力的指示器。 在 [sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) 视图中提供。 请参阅示例查询 [示例](#examples) 以计算此指标。|0|
|`avg_storage_percent`|弹性池内所有数据库中的数据所用的总存储空间。 不包括数据库文件中的空白区域。 在数据库的 " [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) " 视图中提供 `master` 。 此指标还会发出到 Azure Monitor，其中命名为[named](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent` ，可在 Azure 门户中查看。|低于80%。 对于没有数据增长的池，可以接近100%。|
|`avg_allocated_storage_percent`|弹性池中所有数据库的存储中的数据库文件使用的总存储空间。 在数据库文件中包含空空间。 在数据库的 " [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) " 视图中提供 `master` 。 此指标还会发出到 Azure Monitor，其中命名为[named](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `allocated_data_storage_percent` ，可在 Azure 门户中查看。|低于90%。 对于没有数据增长的池，可以接近100%。|
|`tempdb_log_used_percent`|数据库中的事务日志空间利用率 `tempdb` 。 即使在同一弹性池中的其他数据库中不显示在一个数据库中创建的临时对象， `tempdb` 也是同一池中所有数据库的共享资源。 从池中的一个数据库开始，长时间运行或孤立的事务 `tempdb` 可能会消耗大量的事务日志，并导致同一池中其他数据库中的查询失败。 从 [sys.databases dm_db_log_space_usage](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) 和 [database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) 视图派生。 此指标还发出到 Azure Monitor，可以在 Azure 门户中查看。 请参阅示例查询 [示例](#examples) ，返回此指标的当前值。|低于50%。 最多可接受80% 的偶然高峰。|
|||

除了这些指标以外，Azure SQL 数据库还提供了一个视图，该视图返回实际的资源调控限制，以及在资源池级别以及工作负荷组级别返回资源利用率统计信息的其他视图。

|视图名称|说明|  
|-----------------|--------------------------------|  
|[sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|返回当前数据库或弹性池中的资源调控机制所使用的实际配置和容量设置。|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|返回有关当前资源池状态、资源池的当前配置和累计资源池统计信息的信息。|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|返回工作负荷组的累计工作负荷组统计信息和当前配置。 此视图可以与列 dm_resource_governor_resource_pools 联接， `pool_id` 以获取资源池信息。|
|sys.dm_resource_governor_resource_pools_history_ex|返回过去32分钟的资源池利用率统计信息。 每一行都表示一个20秒的间隔。 这些 `delta_` 列在间隔期间返回每个统计信息中的更改。|
|sys.dm_resource_governor_workload_groups_history_ex|返回过去32分钟的工作负荷组利用率统计信息。 每一行都表示一个20秒的间隔。 这些 `delta_` 列在间隔期间返回每个统计信息中的更改。|
|||

这些视图可用于监视资源利用率，并以近乎实时的速度对资源争用进行故障排除。 主副本和可读辅助副本上的用户工作负荷（包括异地副本）归类为 `SloSharedPool1` 资源池和 `UserPrimaryGroup.DBId[N]` 工作负荷组，其中 `N` 代表数据库 ID 值。

除了监视当前资源利用率外，使用密集池的客户还可以在单独的数据存储中维护历史资源利用率数据。 可在预测分析中使用此数据，根据历史和季节性趋势主动管理资源利用率。

## <a name="operational-recommendations"></a>操作建议

**留出足够的资源预留空间**。 如果发生资源争用和性能下降，则缓解操作可能涉及将一些数据库移出受影响的弹性池，或按前面所述扩展池。 但是，这些操作需要额外的计算资源才能完成。 具体而言，对于高级池和业务关键池，这些操作需要传输要移动的数据库的所有数据，或者，如果池已向上扩展，则需要为弹性池中的所有数据库传输数据。 数据传输是一项长时间运行且消耗大量资源的操作。 如果池的资源紧张，缓解操作本身会进一步降低性能。 在极端情况下，由于所需资源不可用，可能无法通过数据库移动或池扩展来解决资源争用。 在这种情况下，在受影响的弹性池上暂时减少查询工作负荷可能是唯一的解决方案。

使用密集池的客户应密切监视资源利用率趋势（如前文所述），并采取缓解措施，同时指标保留在建议范围内，并且弹性池中仍有足够的资源。

资源利用率取决于多个因素，这些因素随着时间的推移每个数据库和每个弹性池发生变化。 在密集池中实现最佳的价格/性能比需要持续监视和重新平衡，即，将数据库从更充分利用的池移动到使用率较低的池，并根据需要创建新的池来适应增加的工作负荷。

**不要移动 "热" 数据库**。 如果池级别的资源争用主要由大量使用不足的数据库引起，则将这些数据库移动到使用率较低的池或使其成为独立数据库可能很有吸引力。 但是，不建议在数据库保持利用率较高的情况下执行此操作，因为移动操作会进一步降低性能（这两种情况都适用于正在移动的数据库和整个池）。 相反，请等待高使用率下降，或移动利用率较低的数据库以减少池级别的资源压力。 但在这种情况下，移动数据库并不会带来任何好处，因为它不会在池级别上大大降低资源利用率。

**在 "隔离" 池中创建新数据库**。 在频繁创建新数据库的情况下（例如，使用每个数据库的租户模型的应用程序），存在于现有弹性池的新数据库会意外地消耗大量资源并影响池中的其他数据库和内部进程。 若要缓解这种风险，请创建一个具有大量资源分配的单独 "隔离" 池。 将此池用于资源消耗未知的新数据库。 一旦某个数据库处于业务周期的此池中（如一周或一个月），并且其资源消耗量已知，则可以将其移动到具有足够容量的池，以容纳此额外的资源使用情况。

**监视已使用和已分配的空间**。 当分配的池空间 (池中所有数据库的存储中所有数据库文件的总大小时) 达到最大池大小，则可能会出现空间不足错误。 如果分配的空间趋势高，且跟踪达到最大池大小，则缓解选项包括：
- 将一些数据库移出池以减少分配的总空间
- [收缩](file-space-manage.md) 数据库文件以减少文件中的空分配空间
- 将池扩展到最大池大小较大的服务目标

如果 "已用池空间" (池中所有数据库中的数据的总大小，则不会在文件中包含空格) 高趋势，并按进度达到最大池大小，缓解措施包括：
- 将一些数据库移出池以减少总已用空间
- 将 (存档) 数据移出数据库，或删除不再需要的数据
- 实现 [数据压缩](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression)
- 将池扩展到最大池大小较大的服务目标

**避免服务器过于密集**。 Azure SQL 数据库每个服务器最多 [支持](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) 5000 个数据库。 如果客户使用具有数千个数据库的弹性池，则可以考虑将多个弹性池置于单个服务器上，并将数据库总数降低到支持的限制。 但是，具有数千个数据库的服务器带来了运营挑战。 需要枚举服务器上的所有数据库（例如，在门户中查看数据库）的操作将变慢。 操作错误，如服务器级别登录名或防火墙规则的错误修改，会影响更多的数据库。 意外删除服务器将需要 Microsoft 支持部门的帮助来恢复已删除服务器上的数据库，并将导致所有受影响的数据库出现长时间的中断。

建议将每个服务器的数据库数限制为小于所支持的最大值。 在许多情况下，每个服务器最多可以使用1000-2000 个数据库。 为了降低意外删除服务器的可能性，建议对服务器或其资源组进行 [删除锁定](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) 。

过去，涉及在同一服务器上移动数据库的某些情况比在服务器之间移动数据库的速度更快。 目前，不管源服务器和目标服务器如何，都将以相同的速度移动所有数据库。

## <a name="examples"></a>示例

### <a name="monitoring-memory-utilization"></a>监视内存使用率

此查询计算 `oom_per_second` 过去32分钟内每个资源池的度量值。 可以在弹性池中的任何数据库中执行此查询。

```sql
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```

### <a name="monitoring-tempdb-log-space-utilization"></a>监视 `tempdb` 日志空间利用率

此查询将返回度量值的当前值 `tempdb_log_used_percent` ， `tempdb` 并显示事务日志相对于其最大允许大小的相对利用率。 可以在弹性池中的任何数据库中执行此查询。

```sql
SELECT (lsu.used_log_space_in_bytes / df.log_max_size_bytes) * 100 AS tempdb_log_space_used_percent
FROM tempdb.sys.dm_db_log_space_usage AS lsu
CROSS JOIN (
           SELECT SUM(CAST(max_size AS bigint)) * 8 * 1024. AS log_max_size_bytes
           FROM tempdb.sys.database_files
           WHERE type_desc = N'LOG'
           ) AS df
;
```

## <a name="next-steps"></a>后续步骤

- 有关弹性池的简介，请参阅 [弹性池有助于管理和缩放 AZURE SQL 数据库中的多个数据库](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)。
- 有关优化查询工作负荷以降低资源利用率的信息，请参阅 [监视和优化]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)，以及 [监视和性能优化](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview)。
