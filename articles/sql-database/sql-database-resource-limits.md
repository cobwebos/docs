---
title: Azure SQL 数据库资源限制概述 | Microsoft Docs
description: 本页介绍了 Azure SQL 数据库中单一数据库的一些常见资源限制（基于 DTU）。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 6f6fa1ebc086530f138d32ee5a9c799b5bfbbdeb
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412104"
---
# <a name="overview-azure-sql-database-resource-limits"></a>Azure SQL 数据库资源限制概述 

本文概述了 Azure SQL 数据库资源限制，并介绍了达到或超过这些资源限制时会发生什么。

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>服务器和数据库的最大数目是多少？

| 最大值 | 值 |
| :--- | :--- |
| 每个服务器的数据库数 | 5000 |
| 任意区域中每个订阅的服务器默认数量 | 20 |
| 任意区域中每个订阅的服务器数上限 | 200 |
| 每个服务器的 DTU/eDTU 配额 | 54,000 |
| 每个服务器的 vCore 配额 | 540 |
| 每个服务器的最大池数 | 受限于 DTU 或 vCore 数 |
|||

> [!NOTE]
> 若要获得超过默认数量的 DTU/eDTU 配额、vCore 配额或服务器，可以在 Azure 门户中为订阅提交问题类型为“配额”的新支持请求。 每个服务器的 DTU/eDTU 配额和数据库限制约束了每个服务器的弹性池数。 

> [!IMPORTANT]
> 随着数据库的数量接近每个服务器的限制，可能出现以下情况：
> - 对主数据库运行查询的延迟增加。  这包括资源利用率统计信息的视图，如 sys.resource_stats。
> - 管理操作和呈现门户视点（涉及枚举服务器中的数据库）的延迟增加。

## <a name="what-happens-when-database-resource-limits-are-reached"></a>如果达到数据库资源限制，会发生什么？

### <a name="compute-dtus-and-edtus--vcores"></a>计算（DTU 和 eDTU/vCore）

如果数据库计算资源利用率（衡量依据为 DTU 和 eDTU 或 vCore）变高，查询延迟会增加，甚至可能会出现超时。在上述情况下，服务可能对查询排队，并在资源可用时向查询提供资源以用于执行。
计算使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的性能级别，为数据库提供更多计算资源。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 优化查询，减少每个查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

### <a name="storage"></a>存储

当使用的数据库空间到达上限时，将无法进行增加数据大小的数据库插入和更新操作，客户端会收到[错误消息](sql-database-develop-error-messages.md)。 数据库的选择和删除操作不受影响。

空间使用率变高时，风险缓解选项包括：

- 提高数据库或弹性池的大小上限，或添加更多存储资源。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 如果数据库在弹性池内，那么可选择将数据库移出弹性池，从而避免与其他数据库共享存储空间。
- 收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>会话和辅助角色（请求） 

会话和辅助角色的数目上限由服务层和性能级别（DTU 和 eDTU）决定。 当到达会话或辅助角色上限时，新的请求将被拒绝，客户端将收到错误消息。 虽然应用程序可以轻松地控制可用的连接数，但并行辅助角色数通常更难以估计和控制。 在负荷高峰期，当数据库资源达到上限，辅助角色由于较长时间运行查询而堆积时，这种情况尤其突出。 

会话或辅助角色使用率变高时，风险缓解选项包括：
- 增加数据库或弹性池的服务层或性能级别。 请参阅[缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放弹性池资源](sql-database-elastic-pool-scale.md)。
- 如果争用计算资源造成了辅助角色使用率上升，请优化查询，以降低每项查询的资源使用率。 有关详细信息，请参阅[查询优化/提示](sql-database-performance-guidance.md#query-tuning-and-hinting)。

## <a name="next-steps"></a>后续步骤

- 有关常见问题的解答，请参阅 [SQL 数据库常见问题解答](sql-database-faq.md)。
- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md)。
- 有关 DTU 和 eDTU 的信息，请参阅 [DTU 和 eDTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)。
- 有关 tempdb 大小限制的信息，请参阅 https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database。
