---
title: 高可用性 - Azure SQL 数据库服务 | Microsoft Docs
description: 了解 Azure SQL 数据库服务的高可用性功能和特性
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 06/10/2019
ms.openlocfilehash: 226b0c1cb11fc872cb7759e0d0e49275b9c2d9bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568153"
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL 数据库

Azure SQL 数据库中的高可用性体系结构的目标是保证数据库在 99.99% 的时间内可正常运行，无需让用户担心维护操作所造成的影响和服务中断。 Azure 会自动处理关键的维护任务（例如修补、备份、Windows 和 SQL 升级），以及底层硬件、软件或网络故障等计划外的事件。  修补或故障转移底层 SQL 实例时，如果在应用中[使用重试逻辑](sql-database-develop-overview.md#resiliency)，则停机不会产生明显影响。 即使出现最严重的问题，Azure SQL 数据库也能快速恢复，确保数据始终可用。

高可用性解决方案旨在确保提交的数据永远不会由于故障而丢失，维护操作不会影响工作负荷，且数据库不会成为软件体系结构中的单一故障点。 在升级或维护数据库期间，维护或停机时段都不需要停止工作负荷。 

Azure SQL 数据库中使用两种高可用性体系结构模型：

- 基于计算和存储隔离的标准可用性模型。  该模型依赖于远程存储层的高可用性和可靠性。 此体系结构面向可以容忍在维护活动期间出现一定程度的性能下降的预算导向型业务应用程序。
- 基于数据库引擎进程群集的高级可用性模型。 该模型依赖于以下事实：始终存在可用数据库引擎节点的仲裁。 此体系结构面向具有较高 IO 性能和较高事务处理率的任务关键型应用程序，保证维护活动期间尽量减小对工作负荷性能造成的影响。

Azure SQL 数据库在最新稳定版本的 SQL Server 数据库引擎和 Windows OS 上运行，大多数用户不会察觉到正在持续执行升级。

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>“基本”、“标准”和“常规用途”服务层级可用性

这些服务层级利用标准的可用性体系结构。 下图显示了具有隔离的计算和存储层的四个不同节点。

![计算和存储隔离](media/sql-database-high-availability/general-purpose-service-tier.png)

标准可用性模型包括两个层：

- 无状态计算层：运行 `sqlserver.exe` 进程，仅在附加的 SSD 上包含暂时性的缓存数据，例如 TempDB、模型数据库、计划缓存、缓冲池和列存储池。 此无状态节点由 Azure Service Fabric 运行。Service Fabric 初始化 `sqlserver.exe`、控制节点运行状况，并根据需要执行到另一节点的故障转移。
- 有状态数据层：包含存储在 Azure Blob 存储中的数据库文件 (.mdf/.ldf)。 Azure Blob 存储具有内置的数据可用性和冗余功能。 它可以保证即使 SQL Server 进程崩溃，日志文件中的每条记录或者数据文件中的页面也仍会得到保留。

每当升级数据库引擎或操作系统，或者检测到故障时，Azure Service Fabric 会将无状态 SQL Server 进程移到具有足够可用容量的另一个无状态计算节点。 Azure Blob 存储中的数据不受移动操作的影响，数据/日志文件将附加到新初始化的 SQL Server 进程。 此过程保证 99.99% 的可用性，但在过渡期间，繁重工作负荷的性能可能会有一定程度的下降，因为新的 SQL Server 实例是使用冷缓存启动的。

## <a name="premium-and-business-critical-service-tier-availability"></a>“高级”或“业务关键”服务层级可用性

高级和业务关键型服务层级利用高级可用性模型，该模型与单个节点上的计算资源（SQL Server 数据库引擎进程）和存储（本地附加的 SSD）相集成。 实现高可用性的方式是将计算和存储资源复制到其他节点，从而建立由三到四个节点组成的群集。 

![数据库引擎节点群集](media/sql-database-high-availability/business-critical-service-tier.png)

底层数据库文件 (.mdf/.ldf) 放在附加的 SSD 存储中，以便为工作负荷提供延迟极低的 IO。 高可用性是使用类似于 SQL Server [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技术来实现的。 群集包含可供读/写客户工作负荷访问的单个主要副本（SQL Server 进程），最多包含三个次要副本（计算和存储），这些副本包含数据的副本。 主要节点不断地将更改按顺序推送到辅助节点，在提交每个事务之前，它可确保数据已至少同步到一个次要副本。 此过程可以保证当主要节点出于任何原因而崩溃时，始终可以故障转移到某个完全同步的节点。 故障转移由 Azure Service Fabric 启动。 次要副本变成新的主要节点后，会创建另一个次要副本，以确保群集中有足够的节点（仲裁集）。 故障转移后，SQL 连接会自动重定向到新的主要节点。

作为一项额外的优势，高级可用性模型提供用于将只读 SQL 连接重定向到某个次要副本的功能。 此功能称为[读取扩展](sql-database-read-scale-out.md)。它通过主要副本免费提供 100% 的额外计算容量，以减轻分析工作负荷等只读操作的负担。

## <a name="zone-redundant-configuration"></a>区域冗余配置

默认情况下, 将在同一数据中心内创建高级可用性模型的节点群集。 引入[Azure 可用性区域](../availability-zones/az-overview.md)后, SQL 数据库可以将群集中的不同副本放置到同一区域中的不同可用性区域。 若要消除单一故障点，还要将控件环跨区域地复制为三个网关环 (GW)。 到特定网关环的路由受 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) (ATM) 控制。 由于高级或业务关键服务层中的区域冗余配置不会创建其他数据库冗余, 因此你可以无需额外付费即可启用此功能。 通过选择区域冗余配置, 你可以使你的高级或业务关键数据库弹性应对一组更大的故障, 包括灾难性的数据中心中断, 而不会对应用程序逻辑进行任何更改。 还可以将所有现有“高级”或“业务关键”数据库或池转换到区域冗余配置。

由于区域冗余数据库的副本在不同的数据中心具有一定距离, 因此增加的网络延迟可能会增加提交时间, 从而影响某些 OLTP 工作负载的性能。 始终可以通过禁用区域冗余设置返回到单个区域配置。 此过程是一种联机操作, 类似于常规的服务层升级。 在此进程结束时，该数据库或池将从区域冗余环迁移到单个区域环，反之亦然。

> [!IMPORTANT]
> 目前, 只有在 "高级" 和 "业务关键" 服务层中, 才支持区域冗余数据库和弹性池。 使用业务关键层时, 区域冗余配置仅在选择 Gen5 计算硬件时可用。 有关支持区域冗余数据库的区域的最新信息, 请参阅[按区域提供的服务支持](../availability-zones/az-overview.md#services-support-by-region)。  

下图演示了高可用性体系结构的区域冗余版本：

![高可用性体系结构区域冗余](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>加速的数据库恢复 (ADR)

[加速数据库恢复 (ADR)](sql-database-accelerated-database-recovery.md)是一项全新的 SQL 数据库引擎功能, 可大大提高数据库的可用性, 尤其是在存在长时间运行的事务时。 ADR 目前可用于单个数据库、弹性池和 Azure SQL 数据仓库。

## <a name="conclusion"></a>结束语

Azure SQL 数据库提供与 Azure 平台深度集成的内置高可用性解决方案。 它依赖于使用 Service Fabric 来执行故障检测和恢复，依赖于 Azure Blob 存储来实现数据保护，并依赖于可用性区域来提高容错能力。 此外，Azure SQL 数据库利用 SQL Server 的 Always On 可用性组技术来执行复制和故障转移。 将这些技术相结合，应用程序可完全实现混合存储模型的优势并支持最严格的 SLA。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 可用性区域](../availability-zones/az-overview.md)
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)
- 有关高可用性和灾难恢复的更多选项，请参阅[业务连续性](sql-database-business-continuity.md)
