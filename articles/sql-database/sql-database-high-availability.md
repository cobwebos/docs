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
manager: craigg
ms.date: 04/17/2019
ms.openlocfilehash: ec9f5aa8163ea9bb838b1a95ab8ad49233a72643
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698223"
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL 数据库

Azure SQL 数据库中的高可用性体系结构的目标是时间的保证您的数据库已启动并正在运行的 99.99%，而无需担心的维护操作和服务中断的影响。 Azure 会自动处理关键的维护任务，例如修补、 备份、 Windows 和 SQL 升级，以及计划外的事件，例如基础硬件、 软件或网络故障。  当基础 SQL 实例进行修补或故障转移时，停机时间并不明显如果您[采用重试逻辑](sql-database-develop-overview.md#resiliency)应用程序中。 即使出现最严重的问题，Azure SQL 数据库也能快速恢复，确保数据始终可用。

高可用性解决方案旨在确保提交的数据永远不会因维护操作不会影响工作负荷的故障而丢失，并且数据库将不可单一软件体系结构中的故障点。 在升级或维护数据库期间，维护或停机时段都不需要停止工作负荷。 

有一些使用 Azure SQL 数据库中的两个高可用性体系结构模型：

- 根据计算和存储分离的标准可用性模型。  它依赖的远程存储层的高可用性和可靠性。 此体系结构面向面向预算的业务应用程序可以容忍某些维护活动时的性能下降。
- 基于数据库引擎进程在群集的高级可用性模型。 它依赖于这一事实是始终可用的数据库引擎节点的仲裁。 此体系结构针对关键任务应用程序具有高 IO 性能、 高事务率和保证工作负荷进行维护活动时的最小性能影响。

Azure SQL 数据库在 SQL Server 数据库引擎和 Windows OS 的最新稳定版本上运行和大多数用户不会察觉到连续执行升级。

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>基本、标准和常规用途服务层可用性

这些服务层利用标准可用性体系结构。 下图显示了具有单独的计算和存储层的四个不同节点。

![计算和存储隔离](media/sql-database-high-availability/general-purpose-service-tier.png)

标准可用性模型包含两个层：

- 运行一个无状态计算层`sqlserver.exe`处理并包含附加的 SSD 上仅暂时性和缓存数据，例如 TempDB、 model 数据库、 计划缓存中，缓冲池和列存储池。 此无状态的节点运营的 Azure Service Fabric，初始化`sqlserver.exe`，控制节点的运行状况并执行故障转移到另一个节点，如有必要。
- 数据库文件 (.mdf/.ldf) 存储在 Azure Blob 存储中的一个有状态数据层。 Azure blob 存储具有内置数据可用性和冗余功能。 它可保证即使 SQL Server 进程崩溃将保留在日志文件或数据文件中的页中的每个记录。

每次数据库引擎或操作系统升级时，或检测到故障，Azure Service Fabric 将无状态的 SQL Server 进程到另一个无状态计算节点具有足够的可用容量。 在移动后，不会影响 Azure Blob 存储中的数据和数据/日志文件附加到新初始化的 SQL Server 进程。 此过程可保证 99.99%的可用性，但繁重的工作负荷可能会遇到一些在转换期间的性能下降，因为使用冷缓存启动新的 SQL Server 实例。

## <a name="premium-and-business-critical-service-tier-availability"></a>“高级”或“业务关键”服务层可用性

高级和业务关键服务层利用集成的高级可用性模型计算资源 （SQL Server 数据库引擎进程） 和单个节点上的存储 (本地附加的 SSD)。 复制到其他节点创建三到四个节点群集的计算和存储，从而实现高可用性。 

![数据库引擎节点群集](media/sql-database-high-availability/business-critical-service-tier.png)

基础数据库文件 (.mdf/.ldf) 放置在附加的 SSD 存储，以提供极低延迟 IO 工作负荷。 使用类似于 SQL Server 的技术实现高可用性[Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)。 群集包含一个主副本 （SQL Server 进程） 的读写客户工作负荷，并最多三个辅助副本 （计算和存储） 可访问包含数据的副本。 主节点不断地将更改推送到订单中的辅助节点，并确保提交的每个事务之前的数据同步到至少一个辅助副本。 此过程可保证，如果主节点由于任何原因发生故障，则始终完全同步的节点故障转移到。 Azure Service Fabric 启动故障转移。 一旦辅助副本将变成新的主节点，创建了另一个辅助副本以确保群集具有足够的节点 （仲裁集）。 故障转移完成后，SQL 连接会自动重定向到新的主节点上。

作为一项额外权益，高级可用性模型包括只读 SQL 连接重定向到一个辅助副本的能力。 此功能被称为[读取横向扩展](sql-database-read-scale-out.md)。它提供了额外的 100%计算，无需额外付费的容量来减轻只读操作，如从主副本的分析工作负荷。

## <a name="zone-redundant-configuration"></a>区域冗余配置

默认情况下，同一数据中心内创建高级可用性模型的节点的群集。 通过引入[Azure 可用性区域](../availability-zones/az-overview.md)，SQL 数据库可以在群集到同一个区域中不同可用性区域中放置不同的副本。 若要消除单一故障点，还要将控件环跨区域地复制为三个网关环 (GW)。 到特定网关环的路由受 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) (ATM) 控制。 由于高级或业务关键服务层中的区域冗余配置不会创建其他数据库冗余，则可以启用它，无需额外付费。 通过选择区域冗余配置，您可以对高级或业务关键数据库可复原更广的故障，包括灾难性的数据中心服务中断，而无需对应用程序逻辑的任何更改。 还可以将所有现有“高级”或“业务关键”数据库或池转换到区域冗余配置。

由于区域冗余数据库与它们之间一定距离的不同数据中心内有副本，增加了的网络延迟可能增加的提交时间，并因此影响某些 OLTP 工作负荷的性能。 始终可以通过禁用区域冗余设置返回到单个区域配置。 此过程是类似于常规服务层升级的联机操作。 在此进程结束时，该数据库或池将从区域冗余环迁移到单个区域环，反之亦然。

> [!IMPORTANT]
> 区域冗余数据库和弹性池目前仅支持高级和业务关键服务层。 默认情况下，备份和审核记录都存储在 RA-GRS 存储中，因此在发生区域范围的服务中断时可能不会自动可用。 

下图演示了高可用性体系结构的区域冗余版本：

![高可用性体系结构区域冗余](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>加速的数据库恢复 (ADR)

[加速数据库恢复 (ADR)](sql-database-accelerated-database-recovery.md)新 SQL 数据库引擎功能的极大地提高了数据库的可用性，尤其是在存在长时间的情况下运行的事务。 ADR 目前可用于单个数据库、弹性池和 Azure SQL 数据仓库。

## <a name="conclusion"></a>结束语

Azure SQL 数据库功能的内置的高可用性解决方案，与 Azure 平台深度集成。 它是依赖于故障检测和恢复的 Service Fabric、 Azure Blob 存储用于数据保护和可用性区域提高容错性。 此外，Azure SQL 数据库利用从 SQL Server 复制和故障转移的 Alwayson 可用性组技术。 这些技术组合使应用程序来完全实现的混合存储优势的模型，支持最严格的 Sla。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 可用性区域](../availability-zones/az-overview.md)
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)
- 有关高可用性和灾难恢复的更多选项，请参阅[业务连续性](sql-database-business-continuity.md)
