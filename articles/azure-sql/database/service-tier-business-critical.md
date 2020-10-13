---
title: “业务关键”服务层级
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 了解 Azure SQL 数据库和 Azure SQL 托管实例的“业务关键”服务层级。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 0067811316a8afd26828050d81215ecb5748c841
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85986684"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>“业务关键”层级 - Azure SQL 数据库和 Azure SQL 托管实例 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> “业务关键”层级在 DTU 购买模型中称为“高级”。 有关基于 vCore 购买模型与基于 DTU 购买模型的比较，请参阅 [Azure SQL 数据库购买模型和资源](purchasing-models.md)。

Azure SQL 数据库和 Azure SQL 托管实例都基于 SQL Server 数据库引擎体系结构，该体系结构已根据云环境做出调整，以确保即使在发生基础结构故障时，也仍能提供 99.99% 的可用性。 使用了三种体系结构模型：
- 常规用途/标准 
- 业务关键/高级
- 超大规模

“高级”/“业务关键”服务层级模型基于数据库引擎进程群集。 此体系结构模型依赖于以下事实：即使在执行维护活动期间，也始终存在可用数据库引擎节点的仲裁，并且能尽量减少对工作负荷性能的影响。 “超大规模”服务层级目前仅适用于 Azure SQL 数据库（非 SQL 托管实例），是一个高度可缩放的存储和计算性能层，它利用 Azure 体系结构来横向扩展 Azure SQL 数据库中数据库的存储和计算资源，远远超出了“常规用途”和“业务关键”服务层级的可用限制。

Azure 以透明方式升级和修补底层操作系统、驱动程序和 SQL Server 数据库引擎，同时尽量减少最终用户的停机时间。 

“高级”或“业务关键”服务层级中已启用高级可用性，此功能面向密集型工作负载，此类工作负载无法承受由于持续维护操作而造成的任何性能影响。

计算和存储集成在高级模型中的单个节点上。 此体系结构模型中的高可用性是通过使用类似于 SQL Server [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技术复制部署在四节点集群中的计算（SQL Server 数据库引擎进程）和存储（本地连接的 SSD）来实现的。

![数据库引擎节点群集](./media/service-tier-business-critical/business-critical-service-tier.png)

SQL Server 数据库引擎进程和底层 .mdf/.ldf 文件都放置在同一个节点上，该节点在本地附加了 SSD 存储，使工作负载保持较低的延迟。 高可用性是使用类似于 SQL Server [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技术来实现的。 每个数据库都是由数据库节点组成的群集，该群集中的一个主数据库可由客户工作负载访问，还有三个辅助进程包含数据副本。 主节点不断地将更改推送到辅助节点，以确保在主节点出于任何原因失败时，可在次要副本上提供数据。 故障转移由 SQL Server 数据库引擎处理–一个辅助副本将成为主节点，并创建一个新的辅助副本以确保群集中有足够的节点。 工作负荷自动重定向到新的主节点。

此外，业务关键群集具有内置的[读取扩展](read-scale-out.md)功能，该功能提供免费的内置只读节点，用于运行不会影响主要工作负荷性能的只读查询（例如报告）。

## <a name="when-to-choose-this-service-tier"></a>何时选择此服务层级

“业务关键”服务层级为具有以下特点的应用程序而设计：需要来自基础 SSD 存储的低延迟响应（平均 1-2 毫秒）、在底层基础结构发生故障时需要快速恢复或需要将报表、分析和只读查询分流到主数据库的免费可读次要副本。

选择“业务关键”服务层级而不是“常规用途”层级的主要原因包括：
-   **低 i/o 延迟要求** –需要从存储层快速响应的工作负荷 (1-2 毫秒，平均) 应使用业务关键层。 
-   应用程序与数据库之间频繁通信。 无法利用应用层缓存或[请求批处理](../performance-improve-use-batching.md)，并需要发送大量必须得到快速处理的 SQL 查询的应用程序非常适合使用“业务关键”层级。
-   **大量更新** （插入、更新和删除操作）会修改内存中的数据页 (脏页) 必须通过操作保存到数据文件 `CHECKPOINT` 。 潜在的数据库引擎进程崩溃或故障转移包含大量脏页的数据库可能会增大“常规用途”层级中的恢复时间。 如果工作负荷会导致大量的内存中更改，请使用“业务关键”层级。 
-   存在修改数据的长时间运行的事务。 长时间打开的事务会阻止日志文件截断，这可能会增加日志大小和[虚拟日志文件 (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch) 的数量。 如果存在大量的 VLF，可能会在故障转移后减慢数据库的恢复速度。
-   工作负载包含可重定向到免费辅助只读副本的报告和分析查询。
- 提高复原能力，并在故障后更快地恢复。 发生系统故障时，主实例上的数据库将被禁用，某个次要副本将立即成为新的读写主数据库，该数据库随时可以处理查询。 数据库引擎不需要分析和重做日志文件中的事务，也不需要加载内存缓冲区中的所有数据。
- 高级数据损坏防护。 “业务关键”层级在幕后利用数据库副本来实现业务连续性，因此服务还可以利用自动页面修复，这与 SQL Server 数据库[镜像和可用性组](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)使用的技术相同。 如果副本由于数据完整性问题而无法读取某个页面，将从另一个副本检索该页面的全新副本，并替换不可读的页面，而不会造成数据丢失或客户停机。 如果数据库具有异地次要副本，则此功能在“常规用途”层级中适用。
- 更高的可用性 - 采用多 AZ 配置的“业务关键”层级保证 99.995% 的可用性，相比之下，“常规用途”层级保证 99.99% 的可用性。
- 快速异地恢复 - 在部署后的所有时间，采用异地复制配置的“业务关键”层级保证 5 秒恢复点目标 (RPO) 和 30 秒恢复时间目标 (RTO)。

## <a name="next-steps"></a>后续步骤

- 在 [SQL 托管实例](../managed-instance/resource-limits.md#service-tier-characteristics)中查找业务关键层级的资源特征（核心数、I/O 数、内存数），在 [vCore 模型](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4)或 [DTU 模型](resource-limits-dtu-single-databases.md#premium-service-tier)中查找单一数据库，在 [vCore 模型](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4)和 [DTU 模型](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits)中查找弹性池。
- 了解[常规用途](service-tier-general-purpose.md)和[超大规模](service-tier-hyperscale.md)层。
- 了解 [Service Fabric](../../service-fabric/service-fabric-overview.md)。
- 有关高可用性和灾难恢复的更多选项，请参阅[业务连续性](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
