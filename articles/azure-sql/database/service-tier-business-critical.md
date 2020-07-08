---
title: “业务关键”服务层级
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 了解 Azure SQL 数据库和 Azure SQL 托管实例的业务关键服务层。
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
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986684"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>业务关键层-Azure SQL 数据库和 Azure SQL 托管实例 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 业务关键层在 DTU 购买模型中称为 "高级"。 有关基于 vCore 购买模型与基于 DTU 购买模型的比较，请参阅 [Azure SQL 数据库购买模型和资源](purchasing-models.md)。

Azure SQL 数据库和 Azure SQL 托管实例都基于 SQL Server 数据库引擎体系结构，该体系结构针对云环境进行了调整，以确保即使在基础结构发生故障的情况下，也能确保99.99% 的可用性。 使用三个体系结构模型：
- 常规用途/标准 
- 业务关键/高级
- 超大规模

“高级”/“业务关键”服务层级模型基于数据库引擎进程群集。 此体系结构模型依赖于以下事实：即使在执行维护活动期间，也始终存在可用数据库引擎节点的仲裁，并且能尽量减少对工作负荷性能的影响。 超大规模服务层目前仅适用于 Azure SQL 数据库（不是 SQL 托管实例），是一种高度可缩放的存储和计算性能层，它利用 Azure 体系结构来扩大 Azure SQL 数据库中数据库的存储和计算资源，大大超出了常规用途和业务关键服务层可用的限制。

对于最终用户，Azure 将以透明的方式为基础操作系统、驱动程序和 SQL Server 数据库引擎的 Azure 升级和修补程序提供最少的停机时间。 

高级版和业务关键服务层中已启用高级版，并且它专为密集型工作负荷设计，这些工作负荷不会因正在进行维护操作而对性能产生任何影响。

计算和存储在高级模型的单个节点上集成。 此体系结构模型中的高可用性是通过使用类似于 SQL Server [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技术，通过将计算（SQL Server 数据库引擎进程）和存储（本地附加的 SSD）复制到四个节点群集来实现的。

![数据库引擎节点群集](./media/service-tier-business-critical/business-critical-service-tier.png)

SQL Server 数据库引擎进程和基础 .mdf/.ldf 文件都放置在具有本地附加的 SSD 存储的同一节点上，从而降低工作负荷的延迟。 高可用性是使用类似于 SQL Server [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技术实现的。 每个数据库都是一个数据库节点群集，其中包含一个可供客户工作负荷访问的主数据库，还有三个包含数据副本的辅助进程。 主节点会不断地将更改推送到辅助节点，以确保在主节点由于任何原因而失败时，辅助副本上的数据都可用。 故障转移由 SQL Server 数据库引擎处理–一个辅助副本将成为主节点，并创建一个新的辅助副本以确保群集中有足够的节点。 工作负荷自动重定向到新的主节点。

此外，业务关键群集具有内置的[读取扩展](read-scale-out.md)功能，该功能提供免费的内置只读节点，用于运行不会影响主要工作负荷性能的只读查询（例如报告）。

## <a name="when-to-choose-this-service-tier"></a>何时选择此服务层级

业务关键服务层适用于需要底层 SSD 存储中的低延迟响应的应用程序（平均为1-2 毫秒）、在基础结构发生故障时快速恢复，或者需要将报告、分析和只读查询关闭到主数据库的免费的可读辅助副本。

选择“业务关键”服务层级而不是“常规用途”层级的主要原因包括：
-   **低 i/o 延迟要求**–需要从存储层快速响应（平均1-2 毫秒）的工作负荷应使用业务关键层。 
-   **应用程序与数据库之间的频繁通信**。 如果应用程序无法利用应用程序层缓存或[请求批处理](../performance-improve-use-batching.md)，并且需要发送许多必须快速处理的 SQL 查询，则这些应用程序非常适合用于业务关键层。
-   **大量更新**（插入、更新和删除操作）修改内存中的数据页（"脏页"），这些数据页必须通过操作保存到数据文件 `CHECKPOINT` 。 潜在的数据库引擎进程崩溃或故障转移包含大量脏页的数据库可能会增大“常规用途”层级中的恢复时间。 如果工作负荷会导致大量的内存中更改，请使用“业务关键”层级。 
-   **修改数据的长时间运行的事务**。 打开时间较长的事务会阻止日志文件截断，这可能会增加日志大小和[虚拟日志文件（VLF）](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch)的数量。 在故障转移后，很多 Vlf 可能会减慢数据库的恢复速度。
-   具有可重定向到免费辅助只读副本的**报告和分析查询的工作负荷**。
- **更高的复原能力和更快的故障恢复**。 在出现系统故障时，主实例上的数据库将被禁用，其中一个辅助副本将立即成为新的读写主数据库，该数据库可以处理查询。 数据库引擎无需分析和重做日志文件中的事务，并加载内存缓冲区中的所有数据。
- **高级数据损坏保护**。 业务关键层利用后台数据库副本实现业务连续性，因此该服务还利用自动页面修复，这与用于 SQL Server 数据库[镜像和可用性组](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)的技术相同。 如果副本由于数据完整性问题而无法读取某个页面，将从另一个副本检索该页面的全新副本，并替换不可读的页面，而不会造成数据丢失或客户停机。 如果数据库具有异地次要副本，则此功能在“常规用途”层级中适用。
- **更高的可用性**-与常规用途层的99.99% 相比，多个 AZ 配置中业务关键层可保证99.995% 的可用性。
- 使用异地复制配置的**快速异地恢复**业务关键层的恢复点目标（RPO）为5秒，恢复时间目标（RTO）为30秒，100% 的部署时间为30秒。

## <a name="next-steps"></a>后续步骤

- 在[SQL 托管实例](../managed-instance/resource-limits.md#service-tier-characteristics)中查找业务关键层的资源特征（核心数、i/o、内存），在[VCore 模型](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4)或[dtu 模型](resource-limits-dtu-single-databases.md#premium-service-tier)中查找单个数据库，或在[VCore 模型](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4)和[dtu 模型](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits)中查找弹性池。
- 了解[常规用途](service-tier-general-purpose.md)和[超大规模](service-tier-hyperscale.md)层。
- 了解 [Service Fabric](../../service-fabric/service-fabric-overview.md)。
- 有关高可用性和灾难恢复的更多选项，请参阅[业务连续性](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
