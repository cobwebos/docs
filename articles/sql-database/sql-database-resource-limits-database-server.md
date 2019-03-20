---
title: Azure SQL 数据库服务器资源限制 | Microsoft Docs
description: 本文概述了使用弹性池的单一数据库和弹性池的 Azure SQL 数据库服务器资源限制。 它还提供了有关在达到或超过这些资源限制时会发生什么情况的信息。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake,josack
manager: craigg
ms.date: 03/01/2019
ms.openlocfilehash: 5b11f9bc25cd0fcc8a83a2eeaf5cc1746a63200e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093882"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Azure SQL 数据库服务器的 SQL 数据库资源限制

本文概述了管理单一数据库和弹性池的 SQL 数据库服务器的 SQL 数据库服务器资源限制。 它还提供了有关在达到或超过这些资源限制时会发生什么情况的信息。

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
> 若要获得超过默认数量的 DTU/eDTU 配额、vCore 配额或服务器，可以在 Azure 门户中为订阅提交问题类型为“配额”的新支持请求。 每个服务器的 DTU/eDTU 配额和数据库限制约束了每个服务器的弹性池数。
> [!IMPORTANT]
> 随着数据库的数量接近每个 SQL 数据库服务器的限制，可能出现以下情况：
> - 对主数据库运行查询的延迟增加。  这包括资源利用率统计信息的视图，如 sys.resource_stats。
> - 管理操作和呈现门户视点（涉及枚举服务器中的数据库）的延迟增加。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>如果达到数据库资源限制，会发生什么？

### <a name="compute-dtus-and-edtus--vcores"></a>计算（DTU 和 eDTU/vCore）

如果数据库计算资源利用率（衡量依据为 DTU 和 eDTU 或 vCore）变高，查询延迟会增加，甚至可能会出现超时。在上述情况下，服务可能对查询排队，并在资源可用时向查询提供资源以用于执行。
计算使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的计算大小，为数据库提供更多计算资源。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 优化查询，减少每个查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>存储

当使用的数据库空间到达上限时，将无法进行增加数据大小的数据库插入和更新操作，客户端会收到[错误消息](sql-database-develop-error-messages.md)。 数据库的选择和删除操作不受影响。

空间使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的大小上限，或添加更多存储资源。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 如果数据库在弹性池内，那么可选择将数据库移出弹性池，从而避免与其他数据库共享存储空间。
- 收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>会话和辅助角色（请求）

会话和辅助角色的数目上限由服务层和计算大小（DTU 和 eDTU）决定。 当到达会话或辅助角色上限时，新的请求将被拒绝，客户端将收到错误消息。 虽然应用程序可以轻松地控制可用的连接数，但并行辅助角色数通常更难以估计和控制。 在负荷高峰期，当数据库资源达到上限，辅助角色由于较长时间运行查询而堆积时，这种情况尤其突出。

会话或辅助角色使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的服务层或计算大小。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 如果争用计算资源造成了辅助角色使用率上升，请优化查询，以降低每项查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

## <a name="transaction-log-rate-governance"></a>事务日志速率调控 
事务日志速率监管是用于限制高引入速率的工作负荷，例如大容量的 Azure SQL 数据库中的进程插入、 SELECT INTO 和索引生成。 这些限制进行跟踪，并在生成日志记录的速率为次秒级级别强制实施，而不考虑如何消耗了大量 Io 限制吞吐量可能会对数据文件发出。  事务日志生成速率当前增加而呈线性增长到依赖于硬件的点，与最大日志速率允许正在使用的购买模型的 vCore 48 MB/秒。 

> [!NOTE]
> 为事务日志文件的实际物理 Io 不控制或限制。 

日志速率设置，以便它们能够实现并持续在各种方案，而整个系统可以维护对用户负载的最小化影响其功能。 日志速率的监管可确保备份保留在已发布的可恢复性 Sla 内的事务日志。  这种管控还可以防止在辅助副本上过多的积压工作。

生成日志记录后，每个操作计算，并评估它是否应延迟以维护所需的最大日志速率 （每秒 MB/秒）。 延迟时不会添加日志记录刷新到存储，而是在本身日志速率生成过程中应用日志速率管理。

实际的日志生成在运行时所规定的费率可能也会受反馈机制，暂时减少允许的日志费率，以便系统可以达到稳定状态。 日志文件空间管理，从而避免运行到带日志空间状况和可用性组的复制机制可能会暂时降低整体系统限制。 

日志速率调控器流量整形通过以下等待类型显示 (在中公开[sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| 等待类型 | 说明 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 数据库限制 |
| POOL_LOG_RATE_GOVERNOR | 池限制 |
| INSTANCE_LOG_RATE_GOVERNOR | 实例级别限制 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 反馈控件，在高级/企业严重未保持可用性组物理复制 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 反馈控制，限制率，以避免日志空间不足的情况 |
|||

当遇到牵制所需的可伸缩性日志速率限制，请考虑以下选项：
- 为了获得最大的 48 MB/s 日志速率增加到更大的层。 
- 如果正在加载的数据是暂时性的即暂存数据在 ETL 过程中，它可以被加载到 tempdb （这最小日志记录）。 
- 对于分析方案，将加载到涵盖的聚集列存储表。 这将必要的日志的速率减少由于压缩。 此方法也会增加 CPU 使用率并特性仅适用于数据集，它们都受益于聚集列存储索引。 

## <a name="next-steps"></a>后续步骤

- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md)。
- 有关 DTU 和 eDTU 的信息，请参阅 [DTU 和 eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)。
- 有关 tempdb 大小限制的信息，请参阅 [Azure SQL 数据库中的 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。
