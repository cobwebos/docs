---
title: 高可用性 - Azure SQL 数据库服务 | Microsoft Docs
description: 了解 Azure SQL 数据库服务的高可用性功能和特性
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 10/14/2019
ms.openlocfilehash: ab3971b4fb6065701d693debf55242be7b15295e
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965973"
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL 数据库

Azure SQL 数据库中的高可用性体系结构的目标是确保数据库在99.99% 的时间内启动并运行，而无需担心维护操作和中断的影响。 Azure 会自动处理关键的服务任务，例如修补、备份、Windows 和 SQL 升级，以及根本硬件、软件或网络故障等计划外事件。  当对基础 SQL 实例进行修补或故障转移时，如果在应用程序中[使用重试逻辑](sql-database-develop-overview.md#resiliency)，则停机时间并不明显。 即使出现最严重的问题，Azure SQL 数据库也能快速恢复，确保数据始终可用。

高可用性解决方案旨在确保提交的数据永远不会因故障而丢失，维护操作不会影响工作负荷，并且数据库在软件体系结构中不会出现单点故障。 在升级或维护数据库期间，维护或停机时段都不需要停止工作负荷。 

在 Azure SQL 数据库中使用了两种高可用性体系结构模型：

- 基于计算和存储分离的标准可用性模型。  它依赖于远程存储层的高可用性和可靠性。 此体系结构面向基于预算的业务应用程序，这些应用程序可以容忍维护活动期间的性能下降。
- 基于数据库引擎进程群集的高级可用性模型。 它依赖于始终存在可用数据库引擎节点的仲裁。 此体系结构面向任务关键型应用程序，具有高 IO 性能、高事务速率，并保证在维护活动期间对工作负荷的性能影响降至最低。

Azure SQL Database 在最新稳定版本的 SQL Server 数据库引擎和 Windows 操作系统上运行，大多数用户不会注意到升级是连续执行的。

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>“基本”、“标准”和“常规用途”服务层级可用性

这些服务层利用标准可用性体系结构。 下图显示了四个不同的节点，分别为计算层和存储层。

![计算和存储隔离](media/sql-database-high-availability/general-purpose-service-tier.png)

标准可用性模型包括两个层：

- 无状态计算层，用于运行 `sqlservr.exe` 进程，并且仅包含暂时性和缓存的数据，例如 TempDB、附加的 SSD 上的模型数据库、计划缓存、缓冲池和内存中的列存储池。 此无状态节点由 Azure Service Fabric 操作，用于初始化 `sqlservr.exe`，控制节点的运行状况，并在必要时执行故障转移到另一个节点。
- 具有存储在 Azure Blob 存储中的数据库文件（.mdf/.ldf）的有状态数据层。 Azure blob 存储具有内置的数据可用性和冗余功能。 它可以保证即使 SQL Server 处理崩溃，数据文件中的日志文件或页中的每个记录仍将保留。

只要数据库引擎或操作系统升级，或检测到故障，Azure Service Fabric 就会将无状态的 SQL Server 进程移到具有足够可用容量的另一个无状态计算节点。 Azure Blob 存储中的数据不受移动的影响，并且数据/日志文件会附加到新初始化的 SQL Server 进程。 此过程保证99.99% 的可用性，但在转换过程中，较大的工作负荷可能会遇到性能下降的情况，因为新的 SQL Server 实例以冷缓存开头。

## <a name="premium-and-business-critical-service-tier-availability"></a>“高级”或“业务关键”服务层级可用性

高级和业务关键服务层利用高级可用性模型，该模型将计算资源（SQL Server 数据库引擎进程）和存储（本地附加的 SSD）集成到一个节点上。 通过将计算和存储复制到创建三个到四个节点的群集的其他节点，实现高可用性。 

![数据库引擎节点群集](media/sql-database-high-availability/business-critical-service-tier.png)

基础数据库文件（.mdf/.ldf）放置在附加的 SSD 存储上，以向工作负荷提供非常低的延迟 IO。 高可用性是使用类似于 SQL Server [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技术实现的。 群集包括单个主副本（SQL Server 进程），该副本可供读写客户工作负荷访问，最多包含三个包含数据副本的辅助副本（计算和存储）。 主节点会按顺序不断推送对辅助节点的更改，并确保在提交每个事务之前，将数据同步到至少一个辅助副本。 此过程可确保，如果主节点由于任何原因崩溃，总会有一个完全同步的节点会故障转移到。 故障转移由 Azure Service Fabric 发起。 辅助副本成为新的主节点后，将创建另一个辅助副本以确保群集具有足够的节点（仲裁集）。 故障转移完成后，SQL 连接将自动重定向到新的主节点。

作为一个额外的好处，高级可用性模型包括将只读 SQL 连接重定向到某个辅助副本的能力。 此功能称为 "[读取横向扩展](sql-database-read-scale-out.md)"。它提供100% 的额外计算能力，无需支付额外费用，就无法从主副本中脱离加载只读操作，如分析工作负荷。

## <a name="hyperscale-service-tier-availability"></a>超大规模服务层可用性

[分布式函数体系结构](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture)中介绍了超大规模服务层体系结构。 

![超大规模功能体系结构](./media/sql-database-hyperscale/hyperscale-architecture.png)

超大规模中的可用性模型包含四个层：

- 一种无状态计算层，用于运行 `sqlservr.exe` 进程，并只包含暂时性和缓存的数据，例如在附加的 SSD 上的非覆盖 RBPEX 缓存、TempDB、模型数据库等。 此无状态层包括主要计算副本和可用作故障转移目标的多个辅助计算副本（可选）。
- 页面服务器形成的无状态存储层。 此层是在计算副本上运行的 `sqlservr.exe` 进程的分布式存储引擎。 每个页面服务器仅包含暂时性和缓存的数据，例如在附加的 SSD 上覆盖 RBPEX 缓存，以及在内存中缓存数据页。 每个页面服务器在主动-主动配置中都有成对的页面服务器，以提供负载平衡、冗余和高可用性。
- 由计算节点构成的有状态事务日志存储层，运行日志服务进程、事务日志登陆区域和事务日志长期存储。 登陆区域和长期存储使用 Azure 存储，它为事务日志提供可用性和[冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)，确保已提交事务的数据持久性。
- 一种具有数据库文件（.mdf/ndf）的有状态数据存储层，存储在 Azure 存储中，由页面服务器更新。 该层使用 Azure 存储的数据可用性和[冗余](https://docs.microsoft.com/azure/storage/common/storage-redundancy)功能。 它保证数据文件中的每个页面都将保留，即使超大规模体系结构崩溃的其他层中的进程或计算节点发生故障。

所有超大规模层中的计算节点在 Azure Service Fabric 上运行，用于控制每个节点的运行状况，并根据需要对可用节点执行故障转移。

有关超大规模中的高可用性的详细信息，请参阅[超大规模中的数据库高可用性](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale)。

## <a name="zone-redundant-configuration"></a>区域冗余配置

默认情况下，将在同一数据中心内创建高级可用性模型的节点群集。 引入[Azure 可用性区域](../availability-zones/az-overview.md)后，SQL 数据库可以将业务关键数据库的不同副本放置到同一区域中的不同可用性区域。 若要消除单一故障点，还要将控件环跨区域地复制为三个网关环 (GW)。 到特定网关环的路由受 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) (ATM) 控制。 由于高级或业务关键服务层中的区域冗余配置不会创建其他数据库冗余，因此你可以无需额外付费即可启用此功能。 通过选择区域冗余配置，你可以使你的高级或业务关键数据库弹性应对一组更大的故障，包括灾难性的数据中心中断，而不会对应用程序逻辑进行任何更改。 还可以将所有现有“高级”或“业务关键”数据库或池转换到区域冗余配置。

由于区域冗余数据库的副本在不同的数据中心具有一定距离，因此增加的网络延迟可能会增加提交时间，从而影响某些 OLTP 工作负载的性能。 始终可以通过禁用区域冗余设置返回到单个区域配置。 此过程是一种联机操作，类似于常规的服务层升级。 在此进程结束时，该数据库或池将从区域冗余环迁移到单个区域环，反之亦然。

> [!IMPORTANT]
> 目前，只有在 "高级" 和 "业务关键" 服务层中，才支持区域冗余数据库和弹性池。 使用业务关键层时，区域冗余配置仅在选择 Gen5 计算硬件时可用。 有关支持区域冗余数据库的区域的最新信息，请参阅[按区域提供的服务支持](../availability-zones/az-overview.md#services-support-by-region)。  
> 此功能在托管实例中不可用。

下图演示了高可用性体系结构的区域冗余版本：

![高可用性体系结构区域冗余](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>加速的数据库恢复 (ADR)

[加速数据库恢复（ADR）](sql-database-accelerated-database-recovery.md)是一项全新的 SQL 数据库引擎功能，可大大提高数据库的可用性，尤其是在存在长时间运行的事务时。 ADR 目前可用于单个数据库、弹性池和 Azure SQL 数据仓库。

## <a name="testing-application-fault-resiliency"></a>测试应用程序故障复原

高可用性是适用于数据库应用程序的 Azure SQL 数据库平台的基本组成部分。 但是，我们认识到，你可能想要测试在计划内或计划外事件期间启动的自动故障转移操作如何影响应用程序，然后再将其部署到生产环境。 你可以调用一个特殊的 API 来重新启动数据库或弹性池，进而会触发故障转移。 在区域冗余数据库或弹性池的情况下，API 调用将导致将客户端连接重定向到可用性区域中与旧主数据库的可用性区域不同的新主站点。 因此，除了测试故障转移对现有数据库会话的影响，还可以验证是否由于网络延迟的变化而更改了端到端性能。 由于重新启动操作是入侵的，很多用户可能会对平台造成压力，因此每个数据库或弹性池每30分钟只允许进行一次故障转移呼叫。 

可以使用 REST API 或 PowerShell 启动故障转移。 有关 REST API，请参阅[数据库故障](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover)转移和[弹性池故障转移](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover)。 对于 PowerShell，请参阅[AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover)和[AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)。 也可以使用[az REST](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest)命令从 Azure CLI 进行 REST API 调用。

> [!IMPORTANT]
> 故障转移命令当前在超大规模服务层和托管实例中不可用。

## <a name="conclusion"></a>结束语

Azure SQL 数据库具有内置的高可用性解决方案，与 Azure 平台深度集成。 它依赖于故障检测和恢复 Service Fabric，在用于数据保护的 Azure Blob 存储上，以及在可用性区域上以获得更高的容错能力。 此外，Azure SQL 数据库利用 SQL Server 的 Always On 可用性组技术来进行复制和故障转移。 这些技术的结合使应用程序能够完全实现混合存储模型的优势，并支持最苛刻的 Sla。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 可用性区域](../availability-zones/az-overview.md)
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)
- 有关高可用性和灾难恢复的更多选项，请参阅[业务连续性](sql-database-business-continuity.md)
