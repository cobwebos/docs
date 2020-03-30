---
title: 密集弹性池中的资源管理 |微软文档
description: 管理具有许多数据库的 Azure SQL 弹性池中的计算资源。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 03/13/2019
ms.openlocfilehash: 014f9edca1706c39930c6e48bb64cd8873bcace9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473721"
---
# <a name="resource-management-in-dense-elastic-pools"></a>密集弹性池中的资源管理

Azure SQL 数据库[弹性池](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)是一种经济高效的解决方案，用于管理资源使用不同的许多数据库。 弹性池中的所有数据库共享相同的资源分配，如 CPU、内存、工作线程、存储空间、tempdb，前提是**只有池中的数据库子集将在任何给定时间使用计算资源**。 此假设允许弹性池具有成本效益。 客户无需为每个数据库可能可能需要的所有资源付费，而是为池中所有数据库之间共享的少量资源付费。

## <a name="resource-governance"></a>资源调控

资源共享要求系统仔细控制资源使用情况，以尽量减少"嘈杂邻居"效应，其中资源消耗高的数据库会影响同一弹性池中的其他数据库。 同时，系统必须为高可用性和灾难恢复 （HADR）、备份和恢复、监视、查询存储、自动调优等功能提供足够的资源，才能可靠地运行。

Azure SQL 数据库通过使用多个资源治理机制实现这些目标，包括用于进程级资源的治理的 Windows[作业对象](https://docs.microsoft.com/windows/win32/procthread/job-objects)、用于存储配额管理的 Windows[文件服务器资源管理器 （FSRM）](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview)以及用于在 Azure SQL 数据库中运行的每个 SQL Server 实例中实现资源治理的修订和扩展版本的 SQL Server[资源管理器](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)。

弹性池的主要设计目标是具有成本效益。 因此，系统有意允许客户创建_密集的_池，即数据库数接近或达到允许的最大池，但计算资源分配适中。 出于同样的原因，系统不为其内部进程保留所有可能需要的资源，但允许内部进程和用户工作负载之间的资源共享。

这种方法允许客户使用密集的弹性池，以实现足够的性能和重大成本节约。 但是，如果密集池中许多数据库的工作负载足够密集，则资源争用将变得十分严重。 资源争用会会降低用户工作负载性能，并可能对内部进程产生负面影响。

> [!IMPORTANT]
> 在具有许多活动数据库的密集池中，将池中的数据库数量增加到[DTU](sql-database-dtu-resource-limits-elastic-pools.md)和[vCore](sql-database-vcore-resource-limits-elastic-pools.md)弹性池记录的最大数据库数可能不可行。
> 
> 放置在密集池中而不引起资源争用和性能问题的数据库数取决于并发活动数据库的数量以及每个数据库中用户工作负载的资源消耗。 此数字可能会随着用户工作负载的变化而变化。

当资源争用发生在密集拥挤的池中时，客户可以选择以下一个或多个操作来缓解这种情况：
- 调整查询工作负载以减少资源消耗，或随着时间的推移跨多个数据库分散资源消耗。
- 通过将某些数据库移动到其他池，或者使它们成为独立数据库来降低池密度。
- 向上扩展池以获取更多资源。

有关如何实现最后两个操作的建议，请参阅本文后面的[操作建议](#operational-recommendations)。 减少资源争用既有利于用户工作负载和内部进程，也使系统能够可靠地保持预期的服务级别。

## <a name="monitoring-resource-consumption"></a>监视资源消耗

为了避免资源争用导致性能下降，使用密集弹性池的客户应主动监视资源消耗，并在资源争用增加时及时采取行动，从而影响工作负载。 持续监视很重要，因为由于用户工作负荷的变化、数据卷和分布的变化、池密度的变化以及 Azure SQL 数据库服务的变化，池中的资源使用量会随时间而变化。 

Azure SQL 数据库提供了与此类监视相关的几个指标。 超过每个指标的建议平均值表示池中的资源争用，应使用前面提到的操作之一进行解决。

|指标名称|描述|推荐平均值| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|与弹性池关联的 SQL Server 进程的 CPU 利用率，由基础操作系统衡量。 在每个数据库中的[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)视图中以及`master`数据库中的[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)视图中可用。 此指标也会发送到 Azure 监视器，其中[命名为](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)`sqlserver_process_core_percent`，可以在 Azure 门户中查看。 此值对于同一弹性池中的每个数据库都相同。|低于70%。 偶尔短峰值高达 90% 可能是可以接受的。|
|`max_worker_percent`|[辅助线程]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide)利用率。 为池中的每个数据库以及池本身提供。 数据库级别和池级别对辅助线程的数量有不同的限制，因此建议在这两个级别监视此指标。 在每个数据库中的[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)视图中以及`master`数据库中的[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)视图中可用。 此指标也会发送到 Azure 监视器，其中[命名为](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)`workers_percent`，可以在 Azure 门户中查看。|低于80%。 峰值高达 100% 将导致连接尝试和查询失败。|
|`avg_data_io_percent`|读取和写入物理 IO 的 IOPS 利用率。 为池中的每个数据库以及池本身提供。 数据库级别和池级别对 IOPS 的数量有不同的限制，因此建议在这两个级别监视此指标。 在每个数据库中的[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current)视图中以及`master`数据库中的[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)视图中可用。 此指标也会发送到 Azure 监视器，其中[命名为](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)`physical_data_read_percent`，可以在 Azure 门户中查看。|低于80%。 偶尔短峰值高达 100% 是可以接受的。|
|`avg_log_write_percent`|事务日志写入 IO 的吞吐量利用率。 为池中的每个数据库以及池本身提供。 数据库级别和池级别的日志吞吐量存在不同的限制，因此建议在这两个级别监视此指标。 在每个数据库中的[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)视图中以及`master`数据库中的[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)视图中可用。 此指标也会发送到 Azure 监视器，其中[命名为](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)`log_write_percent`，可以在 Azure 门户中查看。 当此指标接近 100% 时，所有数据库修改（插入、更新、删除、合并语句、选择 ... 进、装插入等）会变慢。|低于90%。 偶尔短峰值高达 100% 是可以接受的。|
|`oom_per_second`|弹性池中内存不足 （OOM） 错误的速度，这是内存压力的指示器。 可在[dm_resource_governor_resource_pools_history_ex 视图中](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current)使用。 有关示例查询的示例，请参阅[示例](#examples)查询以计算此指标。|0|
|`avg_storage_percent`|弹性池级别的存储空间利用率。 在`master`数据库中的[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)视图中可用。 此指标也会发送到 Azure 监视器，其中[命名为](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools)`storage_percent`，可以在 Azure 门户中查看。|低于80%。 对于没有数据增长的池，可以接近 100%。|
|`tempdb_log_used_percent`|`tempdb`数据库中的事务日志空间利用率。 尽管在一个数据库中创建的临时对象在同一弹性池中的其他数据库中不可见，`tempdb`但是同一池中所有数据库的共享资源。 从池中的一个数据库启动`tempdb`的长运行或空闲事务可能会占用大部分事务日志，并导致同一池中其他数据库中的查询失败。 可在[系统dm_db_log_space_usage](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql)视图中。 此指标也会发送到 Azure 监视器，并且可以在 Azure 门户中查看。 有关示例查询的示例，请参阅[示例](#examples)查询以返回此指标的当前值。|低于50%。 偶尔的峰值高达 80% 是可以接受的。|
|||

除了这些指标之外，Azure SQL 数据库还提供一个视图，该视图返回实际资源治理限制，以及返回资源池级别和工作负载组级别的资源利用率统计信息的其他视图。

|视图名称|描述|  
|-----------------|--------------------------------|  
|[系统dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|返回当前数据库或弹性池中资源治理机制使用的实际配置和容量设置。|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|返回有关当前资源池状态、资源池当前配置和累积资源池统计信息的信息。|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|返回累积工作负载组统计信息和工作负载组的当前配置。 此视图可以与`pool_id`列上的 sys.dm_resource_governor_resource_pools联接，以获取有关资源池信息的信息。|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|返回过去 32 分钟的资源池利用率统计信息。 每行表示 20 秒间隔。 列`delta_`在间隔内返回每个统计值中的更改。|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|返回过去 32 分钟的工作负载组利用率统计信息。 每行表示 20 秒间隔。 列`delta_`在间隔内返回每个统计值中的更改。|
|||

这些视图可用于监视资源利用率，并近乎实时地排除资源争用问题。 主副本和可读辅助副本（包括地理副本）上的用户工作负荷被分类到`SloSharedPool1`资源池和`UserPrimaryGroup.DBId[N]`工作负载组中，其中`N`代表数据库 ID 值。

除了监视当前资源利用率外，使用密集池的客户还可以在单独的数据存储中维护历史资源利用率数据。 这些数据可用于预测分析，以便根据历史和季节趋势主动管理资源利用率。

## <a name="operational-recommendations"></a>业务建议

**留出足够的资源空间**。 如果发生资源争用和性能下降，缓解可能涉及将某些数据库移出受影响的弹性池，或向上扩展池，如前面所述。 但是，这些操作需要额外的计算资源才能完成。 特别是，对于高级池和业务关键型池，这些操作需要传输要移动的数据库的所有数据，或者如果扩展池，则传输弹性池中的所有数据库的所有数据。 数据传输是一项长时间运行且资源密集型的操作。 如果池已经处于高资源压力下，缓解操作本身将进一步降低性能。 在极端情况下，可能无法通过数据库移动或池扩展来解决资源争用，因为所需的资源不可用。 在这种情况下，暂时减少受影响的弹性池上的查询工作负载可能是唯一的解决方案。

使用密集池的客户应如前面所述，密切监视资源利用率趋势，并在指标保持在建议范围内且弹性池中仍有足够资源时采取缓解措施。

资源利用率取决于每个数据库和每个弹性池随时间变化的多个因素。 在密集池中实现最佳性价比需要持续监控和再平衡，即将数据库从利用率更高的池迁移到利用率较低的池，并根据需要创建新池，以适应增加的工作负载。

**不要移动"热"数据库**。 如果池级别的资源争用主要是由少数高度利用的数据库引起的，则将这些数据库移动到利用率较低的池或使它们成为独立数据库可能很诱人。 但是，不建议在数据库仍然高度使用时执行此操作，因为移动操作将进一步降低移动数据库和整个池的性能。 相反，请等到高利用率降低，或者移动利用率较低的数据库，以缓解池级别的资源压力。 但是，在这种情况下，移动利用率极低的数据库不会带来任何好处，因为它不会实质性地降低池级别的资源利用率。

**在"隔离"池中创建新数据库**。 在频繁创建新数据库（例如使用每个数据库的租户应用程序）的情况下，现有弹性池中的新数据库可能会意外消耗大量资源并影响其他数据库和池中的内部进程。 为了减轻这种风险，创建一个单独的"隔离"池，并分配充足的资源。 对于具有未知资源消耗模式的新数据库，使用此池。 一旦数据库在此池中停留了一段时间（例如一周或一个月）并且其资源消耗已知，就可以将其移动到具有足够容量的池中，以适应此额外的资源使用情况。

**避免逻辑服务器过于密集**。 Azure SQL 数据库[支持](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)每个逻辑服务器最多 5000 个数据库。 使用具有数千个数据库的弹性池的客户可以考虑在单个服务器上放置多个弹性池，数据库总数达到支持的限制。 但是，具有数千个数据库的逻辑服务器会带来操作挑战。 需要枚举服务器上的所有数据库的操作（例如查看门户中的数据库）将较慢。 操作错误（如服务器级登录名或防火墙规则的不正确修改）将影响大量数据库。 意外删除逻辑服务器需要 Microsoft 支持的帮助来恢复已删除的服务器上的数据库，并且将导致所有受影响的数据库长时间中断。

我们建议将每个逻辑服务器的数据库数限制为低于支持的最大数据库数。 在许多情况下，每个服务器最多使用 1000-2000 个数据库是最佳选择。 为了减少意外删除服务器的可能性，我们建议在逻辑服务器或其资源组上放置[删除锁](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)。

过去，某些涉及在同一逻辑服务器上的弹性池中、外或之间的弹性池中移动数据库的情况比在逻辑服务器之间移动数据库时要快。 目前，无论源服务器和目标逻辑服务器如何，所有数据库移动都以相同的速度执行。

## <a name="examples"></a>示例

### <a name="monitoring-memory-utilization"></a>监视内存利用率

此查询计算每个资源`oom_per_second`池的指标，在过去 32 分钟中。 此查询可以在弹性池中的任何数据库中执行。

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>监视`tempdb`日志空间利用率

此查询返回指标的`tempdb_log_used_percent`当前值。 此查询可以在弹性池中的任何数据库中执行。

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>后续步骤

* 有关弹性池的简介，请参阅[弹性池可帮助您管理和扩展多个 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)。
* 有关调整查询工作负载以减少资源利用率的信息，请参阅[监视和调优]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)以及[监视和性能调优](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview)。
