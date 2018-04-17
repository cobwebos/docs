---
title: 高可用性 - Azure SQL 数据库服务 | Microsoft Docs
description: 了解 Azure SQL 数据库服务的高可用性功能和特性
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 69d004ae4c2408e5749d0a7d21b996cec8dba722
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL 数据库
自 Azure SQL 数据库 PaaS 服务推出以来，Microsoft 已向客户承诺在该服务中内置高可用性 (HA)，客户无需操作和添加特殊的逻辑，或者围绕 HA 做出决策。 Microsoft 对 HA 系统的配置和操作保持完全控制，并为客户提供 SLA。 HA SLA 适用于区域中的 SQL 数据库，对于超出 Microsoft 可合理控制的因素（例如，自然灾害、战争、恐怖活动、暴动、政府措施，或者 Microsoft 数据中心外部（包括客户站点，或者客户站点与 Microsoft 数据中心之间的位置）的网络或设备故障）所造成的整个区域范围的故障，它不能提供保护。

为了简化 HA 的问题范围，Microsoft 做出以下假设：
1.  硬件和软件故障不可避免
2.  操作人员会犯下导致故障的错误
3.  计划内维护操作会导致服务中断 

尽管上述每种事件不太频繁出现，但在云规模下，这些事件即使不会每天发生，也会每周发生。 

## <a name="fault-tolerant-sql-databases"></a>容错的 SQL 数据库
客户主要对其自身数据库的复原能力感兴趣，而对整个 SQL 数据库服务的复原能力不太感兴趣。 如果“我的数据库”属于 0.01% 的已停止数据库，则服务在 99.99% 的时间保持运行就会变得毫无意义。 每个数据库需有容错能力，故障缓解永远不应导致已提交事务的丢失。 

SQL 数据库使用基于直接附加磁盘/VHD 的本地存储 (LS) 和基于 Azure 高级存储页 Blob 的远程存储 (RS) 来存储数据。 
- 本地存储在针对具有较高 IOPS 要求的任务关键型 OLTP 应用程序设计的“高级”或“业务关键（预览版）”数据库和弹性池中使用。 
- 远程存储用于基本和标准服务层，这些层针对要求存储和计算能力可单独缩放的预算导向型业务工作负载而设计。 这些层使用用于存储数据库和日志文件的单个页 Blob，并使用内置的存储复制和故障转移机制。

在这两种情况下，SQL 数据库的复制、故障检测和故障转移机制完全自动化，无需人工干预即可运行。 此体系结构旨在确保已提交的数据永远不会丢失，并且数据持久性优先于其他所有要求。

主要优势：
- 客户无需配置或维护复杂的硬件、软件、操作系统或虚拟化环境，就能获取复制数据库的整个优势。
- 关系数据库的完整 ACID 属性由系统维护。
- 故障转移完全自动化，不会丢失任何已提交的数据。
- 服务动态管理主副本连接的路由，无需应用程序逻辑。
- 提供较高程度的自动冗余，且不收取额外的费用。

> [!NOTE]
> 所述的高可用性体系结构随时可能更改，恕不另行通知。 

## <a name="data-redundancy"></a>数据冗余

SQL 数据库中的高可用性解决方案基于 SQL Server 的 [AlwaysON 可用性组](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)技术，同时适用于 LS 和 RS 数据库，在两者中的差异很小。 在 LS 配置中，AlwaysON 可用性组技术用于实现持久性；在 RS 中，它用于实现可用性（降低 RTO）。 

## <a name="local-storage-configuration"></a>本地存储配置

在此类配置中，每个数据库由管理服务 (MS) 在控件环中联机。 跨越同一数据中心内三个独立物理子系统的租户环中包含一个主要副本以及至少两个次要副本（仲裁集）。 所有读取和写入内容由网关 (GW) 发送到主要副本，写入内容以异步方式复制到次要副本。 SQL 数据库使用基于仲裁的提交方案，在此方案中，会先将数据写入主要副本以及至少一个次要副本，然后提交事务。

当节点发生故障时，[Service Fabric](../service-fabric/service-fabric-overview.md) 故障转移系统会自动重新生成副本；当节点脱离和加入系统时，它会保留仲裁集成员资格。 计划内维护经过谨慎的协调，可防止仲裁集减少到最小副本计数（通常为 2）以下。 此模型非常适合“高级”和“业务关键（预览版）”数据库，但需要在计算和存储组件上配置冗余，因此会产生较高的成本。

## <a name="remote-storage-configuration"></a>远程存储配置

对于远程存储配置（基本和标准层），只会在远程 Blob 存储中维护一个副本，使用存储系统的功能来实现持久性、冗余和位衰减检测。 

下图演示了这种高可用性体系结构：
 
![高可用性体系结构](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection-and-recovery"></a>故障检测和恢复 
大规模分布式系统需要一个能够快速可靠地检测故障且尽量靠近客户的故障检测系统。 对于 SQL 数据库而言，此系统基于 Azure Service Fabric。 

对于主要副本，它能立即查明主要副本是否以及何时发生故障且工作无法继续进行，因为所有读取和写入操作首先都在主要副本上发生。 将次要副本提升为主要副本状态的过程会导致 30 秒恢复时间目标 (RTO) 和 0 恢复点目标 (RPO)。 为了缓解 30 秒 RTO 造成的影响，最佳做法是在连接失败时，以较小的等待时间重试连接多次。

当次要副本发生故障时，数据库将缩减到最少量的仲裁集，且没有备用的副本。 Service Fabric 启动的重新配置过程类似于主要副本发生故障后启动的过程，因此，只需在经过片刻的等待后，就能确定故障是否为永久性的并创建另一个次要副本。 如果出现暂时性的服务中断状态（例如操作系统故障或升级），不会立即生成新副本，使故障节点能够重启。 

对于远程存储配置，SQL 数据库在升级期间使用 AlwaysON 功能来故障转移数据库。 为此，在执行计划内升级事件过程中，将会提前运转一个新的 SQL 实例，用于从远程存储附加和恢复数据库文件。 如果发生进程崩溃或其他计划外事件，Windows Fabric 会管理实例可用性，并附加远程数据库文件（最后一个恢复步骤）。

## <a name="zone-redundant-configuration-preview"></a>区域冗余配置（预览版）

默认情况下，本地存储配置的仲裁集是在相同的数据中心中创建的。 通过引入 [Azure 可用性区域](../availability-zones/az-overview.md)，可以将程序集中的不同副本放到同一区域中的不同可用性区域中。 若要消除单一故障点，还要将控件环跨区域地复制为三个网关环 (GW)。 到特定网关环的路由受 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) (ATM) 控制。 区域冗余配置不会创建其他数据库冗余，因此在“高级”或“业务关键（预览版）”服务层中使用可用性区域不需要承担任何额外的费用。 通过选择区域冗余数据库，可以让“高级”或“业务关键（预览版）”数据库能应对范围更广的故障，包括灾难性的数据中心服务中断，且不会对应用程序逻辑产生任何更改。 还可以将所有现有“高级”或“业务关键（预览版）”数据库或池转换到区域冗余配置。

由于区域冗余仲裁集的副本位于不同数据中心，且互相之间都有一定距离，因此增加的网络延迟可能会延长提交时间，从而影响某些 OLTP 工作负载的性能。 始终可以通过禁用区域冗余设置返回到单个区域配置。 此进程是一种数据操作，并且与常规的服务等级目标 (SLO) 更新类似。 在此进程结束时，该数据库或池将从区域冗余环迁移到单个区域环，反之亦然。

> [!IMPORTANT]
> 只有“高级”和“业务关键（预览版）”服务层才支持区域冗余数据库和弹性池。 在公共预览版中，备份和审核记录都存储在 RA-GRS 存储中，因此在发生区域范围的服务中断时可能不会自动可用。 

下图演示了高可用性体系结构的区域冗余版本：
 
![高可用性体系结构区域冗余](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>读取横向扩展
如上所述，在单区域和区域冗余配置中，“高级”和“业务关键（预览版）”服务层都利用仲裁集和 AlwaysON 技术来实现高可用性。 AlwasyON 的优势之一是副本始终处于事务一致状态。 因为副本具有与主数据库相同的性能级别，因此，应用程序可以利用该额外容量为只读工作负荷提供服务（读取横向扩展），不需要额外付费。 这样，只读查询将与主要的读写工作负荷相隔离，不会影响其性能。 读取横向扩展功能适用于其中包括逻辑上独立的只读工作负荷（例如分析）的应用程序，因此可以利用此额外的容量而不需要连接到主数据库。 

若要将读取横向扩展功能用于特定的数据库，必须在创建数据库时或者在之后通过更改其配置来显式启用此功能，可以采用以下方式执行此操作：使用 PowerShell 调用 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) 或 [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) 命令，或者通过 Azure 资源管理器 REST API 使用[数据库 - 创建或更新](/rest/api/sql/databases/createorupdate)方法。

为某个数据库启用读取横向扩展后，会根据在应用程序的连接字符串中配置的 `ApplicationIntent` 属性将连接到该数据库的应用程序定向到该数据库的读写副本或只读副本。 有关 `ApplicationIntent` 属性的信息，请参阅[指定应用程序意向](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent) 

读取横向扩展功能支持会话级一致性。 如果只读会话在由于副本不可用而出现连接错误后重新连接，则可以将其重定向到另一个副本。 它可能会导致处理陈旧的数据集，虽然可能性不大。 同样，如果应用程序使用读写会话写入数据，并立即使用只读会话读取该数据，则新数据可能不会立即可见。

## <a name="conclusion"></a>结束语
Azure SQL 数据库与 Azure 平台深度集成，严重依赖于使用 Service Fabric 来执行故障检测和恢复，并严重依赖于使用 Azure 存储 Blob 来执行数据保护，以及使用可用性区域提高容错性。 同时，Azure SQL 数据库充分利用 SQL Server 现成产品的 AlwaysOn 技术来执行复制和故障转移。 将这些技术相结合，应用程序可完全实现混合存储模型的优势并支持最严格的 SLA。 

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 可用性区域](../availability-zones/az-overview.md)
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) 
