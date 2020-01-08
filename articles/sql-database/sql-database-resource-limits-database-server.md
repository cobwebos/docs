---
title: Azure SQL 数据库资源限制 |Microsoft Docs
description: 本文概述了单一数据库和弹性池的 Azure SQL 数据库资源限制。 它还提供了有关在达到或超过这些资源限制时会发生什么情况的信息。
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
ms.openlocfilehash: da8c194b7911d2eeda8e0c903cb7412186aacfcb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638249"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>SQL 数据库资源限制和资源调控

本文概述了管理单一数据库和弹性池的 SQL 数据库服务器的 SQL 数据库资源限制。 它提供有关在达到或超过这些资源限制时所发生情况的信息，并介绍用于强制执行这些限制的资源调控机制。

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

> [!NOTE]
> 若要获取更多的 DTU/eDTU 配额、vCore 配额或超过默认数量的服务器，可以在具有问题类型 "配额" 的订阅的 Azure 门户中提交新的支持请求。 每个服务器的 DTU/eDTU 配额和数据库限制会限制每个服务器的弹性池数目。

> [!IMPORTANT]
> 随着数据库的数量接近每个 SQL 数据库服务器的限制，可能出现以下情况：
>
> - 对主数据库运行查询的延迟增加。  这包括资源利用率统计信息的视图，如 sys.resource_stats。
> - 管理操作和呈现门户视点（涉及枚举服务器中的数据库）的延迟增加。

### <a name="storage-size"></a>存储大小

对于单一数据库资源存储大小，请参阅[基于 DTU 的资源限制](sql-database-dtu-resource-limits-single-databases.md)或[基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md)，了解每个定价层的存储大小限制。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>如果达到数据库资源限制，会发生什么？

### <a name="compute-dtus-and-edtus--vcores"></a>计算（DTU 和 eDTU/vCore）

当数据库计算使用率（以 Dtu 和 Edtu 或 Vcore 度量）变得较高时，查询延迟会增加，并且查询甚至可能会超时。在这些情况下，查询可能会通过服务排队，并在资源可用时提供给执行资源。
计算使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的计算大小，为数据库提供更多计算资源。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 优化查询，减少每个查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>存储空间

当使用的数据库空间到达上限时，将无法进行增加数据大小的数据库插入和更新操作，客户端会收到[错误消息](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)。 SELECT 和 DELETE 语句将继续成功。

空间使用率变高时，风险缓解选项包括：

- 增加数据库或弹性池的最大大小，或者添加更多存储。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 如果数据库在弹性池内，那么可选择将数据库移出弹性池，从而避免与其他数据库共享存储空间。
- 收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>会话和辅助角色（请求）

会话和工作线程的最大数量由服务层和计算大小（Dtu/Edtu 或 Vcore）决定。 当到达会话或辅助角色上限时，新的请求将被拒绝，客户端将收到错误消息。 虽然应用程序可以轻松地控制可用的连接数，但并行辅助角色数通常更难以估计和控制。 当达到数据库资源限制，并且由于长时间运行查询、大阻塞链或过多查询并行性而导致工作线程堆积时，这种情况尤其如此。

会话或辅助角色使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的服务层级或计算大小。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 如果争用计算资源造成了辅助角色使用率上升，请优化查询，以降低每项查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

## <a name="resource-governance"></a>资源调控

为了强制资源限制，Azure SQL Database 使用基于 SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)、已修改和扩展的资源调控实现在 Azure 中运行 SQL Server 的数据库服务。 在服务中的每个 SQL Server 实例上，都有多个[资源池](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool)和[工作负荷组](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)，同时将资源限制设置为池和组级别，以提供[均衡的数据库即服务](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)。 用户工作负荷和内部工作负荷归类为单独的资源池和工作负荷组。 主副本和可读辅助副本上的用户工作负荷（包括异地副本）归类为 `SloSharedPool1` 资源池和 `UserPrimaryGroup.DBId[N]` 工作负荷组，其中 `N` 代表数据库 ID 值。 此外，还有多个资源池和工作负荷组用于各种内部工作负荷。

除了使用 Resource Governor 在 SQL Server 进程中控制资源以外，Azure SQL 数据库还使用 Windows[作业对象](https://docs.microsoft.com/windows/win32/procthread/job-objects)进行进程级别的资源调控，并使用 Windows[文件服务器资源管理器（FSRM）](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview)进行存储配额管理。

Azure SQL 数据库资源调控本质上是分层的。 从上到下，将使用操作系统资源调控机制并 Resource Governor，并在资源池级别使用 Resource Governor，然后在工作负荷组级别使用来在操作系统级别强制实施限制，并使用Resource Governor。 当前数据库或弹性池生效的资源调控限制显示在[sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)视图中。 

### <a name="data-io-governance"></a>数据 IO 管理

数据 IO 管理是 Azure SQL 数据库中的一个过程，用于限制对数据库的数据文件的读取和写入物理 IO。 为每个服务级别设置 IOPS 限制，以最大程度地减少 "干扰邻居" 的效果，在多租户服务中提供资源分配公平，并保持在底层硬件和存储的功能中。

对于单一数据库，工作负荷组限制适用于针对数据库的所有存储 IO，而资源池限制适用于同一 SQL Server 实例（包括 `tempdb` 数据库）的所有数据库的所有存储 IO。 对于弹性池，工作负荷组限制适用于池中的每个数据库，而资源池限制适用于整个弹性池，包括 `tempdb` 数据库，该数据库在池中的所有数据库之间共享。 通常，工作负载对数据库（单个或共用）的工作负荷可能无法实现资源池限制，因为工作负荷组限制低于资源池限制，并更快地限制 IOPS/吞吐量。 但是，对于同一 SQL Server 实例上的多个数据库，可以通过组合工作负荷达到池限制。

例如，如果查询在没有任何 IO 资源调控的情况下生成 1000 IOPS，但工作负荷组的最大 IOPS 限制设置为 900 IOPS，则查询将无法生成超过900的 IOPS。 但是，如果将 "资源池最大 IOPS 限制" 设置为 1500 IOPS，并且与资源池关联的所有工作负荷组的 IO 总数超过 1500 IOPS，则相同查询的 IO 可能会降低到最大的工作组限制（以 900 IOPS 为限）。

[Sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)视图返回的 IOPS 和吞吐量最小/最小值可用作限制/上限，而不是保证。 此外，资源调控不保证任何特定的存储延迟。 给定用户工作负荷最能实现的延迟、IOPS 和吞吐量不仅取决于 IO 资源调控限制，还取决于所使用的 IO 大小和基础存储的功能。 SQL Server 使用大小介于 512 KB 和 4 MB 之间的 Io。 出于强制 IOPS 限制的目的，将对每个 IO 进行考虑，而不考虑其大小，但 Azure 存储中包含数据文件的数据库除外。 在这种情况下，大于 256 KB 的 IOs 会被视为多个 256 KB Io，以与 Azure 存储 IO 记帐保持一致。

对于使用 Azure 存储中的数据文件的基本、标准和常规用途数据库，如果数据库没有足够的数据文件要累积的数据文件提供此 IOPS 数，或者如果数据未在文件之间均匀分布，或者基础 blob 的性能层限制了低于资源调控限制的 IOPS/吞吐量，则可能无法实现 `primary_group_max_io` 值。 同样，对于频繁的事务提交生成的小日志 Io，工作负荷可能无法实现 `primary_max_log_rate` 值，因为基础 Azure 存储 blob 的 IOPS 限制。

资源利用率值（如 `avg_data_io_percent` 和 `avg_log_write_percent`）在[sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、 [sys.databases resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)和[sys.databases elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)视图中报告，其计算方式为最大资源调控限制的百分比。 因此，当资源管理限制 IOPS/吞吐量以外的其他因素时，如果工作负荷增加，则可以查看 IOPS/吞吐量修整和延迟增加，即使报告的资源利用率仍低于100%。 

若要查看每个数据库文件的读取和写入 IOPS、吞吐量和延迟，请使用[sys. dm_io_virtual_file_stats （）](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)函数。 此函数对数据库的所有 IO （包括不会考虑 `avg_data_io_percent`的后台 IO）进行图面，但使用基础存储的 IOPS 和吞吐量，并可能影响观察到的存储延迟。 此函数还会在 "`io_stall_queued_read_ms`" 和 "`io_stall_queued_write_ms`" 列中，为读取和写入的 IO 资源调控引入额外的延迟。

### <a name="transaction-log-rate-governance"></a>事务日志速率管理

事务日志速率管理是 Azure SQL 数据库中的一个过程，用于限制工作负荷（例如大容量插入、SELECT INTO 和索引生成）的高引入率。 在亚秒级级别跟踪和强制执行日志记录生成速率，而不考虑可以对数据文件发出的 Io 数。  当前，事务日志生成速率以线性方式扩展到与硬件相关的点，并且最大对数率允许使用 vCore 购买模型 96 MB/s。 

> [!NOTE]
> 实际的物理 Io 到事务日志文件不受管辖或限制。

设置日志速率是为了能够在各种情况下实现和持续，而整体系统可以保持其功能，同时对用户负载产生最小的影响。 日志速率监管可确保事务日志备份保留在已发布的可恢复性 Sla 中。  此监管还会阻止辅助副本上出现过多的积压。

随着日志记录的生成，会对每个操作进行评估并评估其是否应该延迟，以便保持最大的所需对数率（每秒 MB/秒）。 将日志记录刷新到存储时，将不会添加延迟，而是在生成日志时应用日志速率调控。

在运行时施加的实际日志生成速率也可能受到反馈机制的影响，暂时降低允许的对数率，使系统能够稳定。 日志文件空间管理，避免在日志空间不足的情况下运行，并可暂时降低总体系统限制。

日志速率调控器流量造型通过以下等待类型（在[dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV 中公开）进行呈现：

| 等待类型 | 说明 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 数据库限制 |
| POOL_LOG_RATE_GOVERNOR | 池限制 |
| INSTANCE_LOG_RATE_GOVERNOR | 实例级别限制 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 高级/业务关键的反馈控制、可用性组物理复制不能保持 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 反馈控制，限制速率以避免超出日志空间条件 |
|||

如果遇到的日志速率限制牵制所需的可伸缩性，请考虑以下选项：
- 向上缩放到更高的服务级别，以获取最大 96 MB/秒的日志速率。 
- 如果加载的数据是暂时性的，例如 ETL 过程中的临时数据，则可以将其加载到 tempdb （这是最小日志记录）。 
- 对于分析方案，请加载到聚集列存储涵盖的表中。 这会减少压缩所需的日志速率。 此方法会增加 CPU 使用率，并且仅适用于受益于聚集列存储索引的数据集。 

## <a name="next-steps"></a>后续步骤

- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。
- 有关 DTU 和 eDTU 的信息，请参阅 [DTU 和 eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)。
- 有关 tempdb 大小限制的信息，请参阅 [Azure SQL 数据库中的 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。
