---
title: 使用 Azure SQL 数据库设计高可用性服务 | Microsoft Docs
description: 了解使用 Azure SQL 数据库对应用程序设计高可用性服务。
keywords: 云灾难恢复, 灾难恢复解决方案, 应用数据备份, 异地复制, 业务连续性规划
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ebe6d2b4d3210ad7c02ec2d26a311645660aeab8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647062"
---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>使用 Azure SQL 数据库设计高可用性服务

在 Azure SQL 数据库上生成和部署高度的可用服务时，可使用[故障转移组和活动异地复制](sql-database-geo-replication-overview.md)在发生区域中断和灾难性故障时进行复原。 它还可实现快速恢复到辅助数据库。 本文主要介绍常见应用程序模式并讨论每种模式的优势和选择时应考虑的因素。 有关弹性池的活动异地复制的信息，请参阅[弹性池灾难恢复策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

> [!NOTE]
> 如果正在使用“高级”或“业务关键（预览版）”数据库和弹性池，可以通过将它们转换为区域冗余部署配置（当前为预览版），使它们能够适应区域性的中断。 请参阅[区域冗余数据库](sql-database-high-availability.md)。  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>方案 1：使用两个 Azure 区域来实现业务连续性，同时将停机时间减至最小
在此方案中，应用程序具有以下特征： 
*   应用程序在一个 Azure 区域中处于活动状态
*   所有数据库会话需要数据读取和写入权限 (RW)
*   必须并置 Web 层和数据层以减少延迟和流量成本 
*   从根本上讲，相比数据丢失，停机时间对于那些应用程序来说是更高的业务风险

在这种情况下，当所有应用程序组件需要一同故障转移时，将针对处理区域灾难对应用程序部署拓扑进行优化。 下图展示了此拓扑。 对于地理冗余，应用程序的资源部署到区域 A 和 B。但是，只有当区域 A 失败后才会利用区域 B 中的资源。 两个区域之间会配置故障转移组，用于管理数据库连接、复制和故障转移。 两个区域中的 Web 服务配置为通过读写侦听器 &lt;failover-group-name&gt;.database.windows.net 访问数据库 (1)。 设置流量管理器以使用[优先级路由方法](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2)。  

> [!NOTE]
> [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)在这篇文章中仅供说明之用。 可以使用任何支持优先级路由方法的负载均衡解决方案。    
>

下图显示了在发生服务中断之前的此配置：

![方案 1. 中断前的配置。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

主要区域服务中断后，SQL 数据库服务会检测到主数据库不可访问，并基于自动故障转移策略的参数触发到次要区域的故障转移 (1)。 可以配置一个宽限期来控制断电检测和故障转移本身之间的时间，具体取决于应用程序 SLA。 流量管理器可能会在故障转移组触发数据库故障转移前启动终结点故障转移。 在这种情况下，Web 应用程序无法立即重新连接到数据库。 但在数据库故障转移完成后，会立即自动实现重新连接。 当失败的区域还原并恢复联机状态时，旧的主数据库自动作为新的辅助数据库进行重新连接。 下图显示故障转移后的配置。
 
> [!NOTE]
> 故障转移后提交的事物会在重新连接时丢失。 故障转移完成后，区域 B 中的应用程序能重新连接并重新开始处理用户请求。 现在 Web 应用程序和主数据库均位于区域 B 并始终存在于同一位置。 n>

![方案 1. 故障转移后的配置](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

如果区域 B 中发生中断，那么主数据库和辅助数据库之间的复制进程会挂起，但是两者之间的链接不会受影响 (1)。 管理的通信流检测出到区域 B 的连接中断，于是将终结点 Web 应用 2 标记为“降级”(2)。 在这种情况下应用程序性能不会受影响，但数据库已暴露，所以如果区域 A 跟着失败时会产生更高的数据丢失风险。

> [!NOTE]
> 对于灾难恢复，建议将应用程序部署配置限于两个区域。 这是因为大多数 Azure 地理位置仅有两个区域。 如果两个区域同时发生灾难性故障，此配置不会为你的应用程序提供保护。 在此类失败的不可能事件中，可以使用[异地恢复操作](sql-database-disaster-recovery.md#recover-using-geo-restore)在第三个区域中恢复数据库。
>

 中断问题缓解后，辅助数据库会立即自动重新与主数据库同步。 同步期间，主数据库的性能可能受影响。 具体影响取决于新主数据库自故障转移开始后获取的数据量。 下图说明了次要区域中的服务中断：

![方案 1. 次要区域中发生中断后的配置。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

此设计模式的主要**优点**是：

* 将同一 Web 应用程序部署到两个区域中时无需任何特定于区域的配置，也无需使用更多逻辑来管理故障转移。 
* 应用程序性能不受故障转移影响，因为 Web 应用程序和数据库始终共存。

区域 B 中的应用程序资源大多时间利用不足，这是需要进行权衡的主要考量。

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>方案 2：可实现业务连续性并提供最高数据保存性能的 Azure 区域
此选项最适合具有以下特征的应用程序：

* 丢失任何数据都会对业务带来高风险。 如果中断是由灾难性故障引起的，则数据库故障转移只能作为最后考虑的方法。
* 应用程序支持只读和读写操作模式，可在“只读模式”下运行一段时间。

此模式下，读写连接开始出现超时错误时，应用程序会切换到只读模式。 Web 应用程序部会署到这两个区域，且包含一个读写侦听器终结点连接和另一个不同的只读侦听器终结点连接 (1)。 流量管理器配置文件应使用[优先级路由](../traffic-manager/traffic-manager-configure-priority-routing-method.md)。 应为每个区域中的应用程序终结点启用[终结点监视](../traffic-manager/traffic-manager-monitoring.md) (2)。

下图说明了在发生服务中断之前的此配置：

![方案 2. 中断前的配置。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

流量管理器检测到区域 A 的连接故障时，会自动将用户流量切换到区域 B 中的应用程序实例。此模式下，必须将数据丢失宽限期设置为足够大的值（例如，24 小时）。 如果在该时间段内解决了中断问题，该措施可确保防止数据丢失。 区域 B 中的 Web 应用程序激活时，读写操作会失败。 此时，应切换到只读模式 (1)。 此模式下，请求会自动路由至辅助数据库。 如果中断是由灾难性故障引起的，则很难在宽限期内缓解故障。 超过宽限期时，故障转移组会触发故障转移。 之后，读写侦听器变为可用状态，其连接恢复正常 (2)。 下图显示恢复过程的两个阶段。

> [!NOTE]
> 如果在宽限期内解决了主要区域中的中断问题，流量管理器会检测到主要区域的连接恢复，并将用户流量切换回区域 A 中的应用程序实例。如上图所示，此应用程序实例使用区域 A 中的主数据库在读写模式下进行恢复和运行。
>

![方案 2. 灾难恢复阶段。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

如果区域 B 中发生中断，流量管理器检测到区域 B 中的终结点 web-app-2 故障并将其标记为“降级”(1)。 与此同时，故障转移组将只读侦听器切换到区域 A (2)。 此中断不会影响最终用户体验，但是中断期间主数据库会暴露。 下图说明了次要区域中的失败：

![方案 2. 次要区域中断。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

解决中断问题后，辅助数据库立即与主数据库同步，只读侦听器切换回区域 B 中的辅助数据库。同步期间，主数据库性能可能略受影响，具体取决于需同步的数据量。

此设计模式具有多个**优点**：

* 它在临时服务中断期间可避免数据丢失。
* 停机时间仅取决于流量管理器检测到连接故障的速度，此速度是可配置的。

权衡是应用程序必须能够在只读模式下运行。

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>方案 3：应用程序重新定位到其他地理位置而不发生数据丢失，且停机时间几乎为零 
在此方案中，应用程序具有以下特征： 
* 最终用户从不同的地理位置访问应用程序
* 应用程序包含只读工作负载，这些工作负载不依赖于与最新更新的完全同步
* 应针对大多用户支持同一地理位置的数据写入访问权限 
* 读取延迟对最终用户体验而言很关键 


若要满足这些需求，需要保证用户设备始终连接至部署到同一地理位置的应用程序以实现只读操作，例如浏览数据、分析等。然而，大多时候都在同一地理位置处理 OLTP 操作。 例如，工作时间在同一个地理位置处理 OLTP 操作，而非工作时间可能会在另一个地理位置处理这些操作。 如果终端用户活动大多发生在工作时间，那么可以保证大多时间对于大多用户，均可实现最佳性能。 下图显示了此拓扑。 
 
应用程序的资源应部署到每个有大量使用需求的地理位置。 例如，如果在美国、欧盟和东南亚，应用程序使用率很高，则应在所有这些区域部署该应用程序。 主数据库应在工作时间结束时从一个地理区域动态转至下一个区域。 此方法称为“循日”。 OLTP 工作负载始终通过读写侦听器 &lt;failover-group-name&gt;.database.windows.net 连接到数据库 (1)。 只读工作负载直接使用数据库服务器终结点 &lt;server-name&gt;.database.windows.net 连接到本地数据库 (2)。 使用[性能路由方法](../traffic-manager/traffic-manager-configure-performance-routing-method.md)配置流量管理器。 它确保最终用户设备连接到最近的 Web 服务。 设置流量管理器时应为每个 Web 服务终结点启用终结点监视 (3)。

> [!NOTE]
> 故障转移组配置定义要用于故障转移的区域。 由于新的主区域位于另一个地理位置，所以对于 OLTP 和只读工作负载，故障转移会导致更长的延迟，直到受影响的区域恢复联机状态为止。
>

![方案 3. 美国东部主服务器配置。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

一天结束时（例如当地时间晚上 11 点），应将活跃数据库切换至下一个区域（北欧）。 此任务可通过使用 [Azure 计划服务](../scheduler/scheduler-intro.md)实现完全的自动化。  此任务涉及以下步骤：
* 使用友好故障转移将故障转移组中的主服务器切换至北欧 (1)
* 删除美国东部和北欧之间的故障转移组
* 使用同一名称在北欧和亚太之间创建一个新的故障转移组 (2)。 
* 将北欧的主服务器和亚太的辅助服务器添加到此故障转移组 (3)。


下图说明了在计划故障转移后的新配置：

![方案 3. 将主服务器切换至北欧。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

假如，北欧发生中断，故障转移组启动自动数据库故障转移，可有效将应用程序提前移至下一个区域 (1)。  在此情况下，在北欧回到联机状态前，美国东部是唯一的辅助服务器区域。 剩下两个区域通过转换角色为三个地理区域中的所有用户提供服务。 须相应调整 Azure 计划程序。 由于剩余的区域从欧洲获取额外的用户流量，所以应用程序性能不仅受额外延迟的影响，还受增加的最终用户连接的影响。 北欧的中断问题缓解后，当地的辅助数据库会立即与当前主数据库同步。 下图说明了北欧的服务中断：

![方案 3. 北欧中断。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> 可减少欧洲最终用户体验因长时间延迟而降级的时间。 为此，应该积极部署应用程序副本并在另一个本地区域（西欧）创建辅助数据库，作为北欧脱机应用程序实例的替换方案。 当后者回到联机状态时，可以决定是继续使用西欧还是删除当地应用程序副本并重新使用北欧，
>

此设计的关键优势是：
* 只读应用程序工作负载可以随时访问最近区域的数据。 
* 读写应用程序工作负载在每个区域活动最频繁的时段可访问最近区域的数据
* 由于应用程序被部署到多个区域，所以能够在一个区域中断后继续运行，而不产生显著的停运时间。 

但存在一些需要权衡的考量因素：
* 区域中断导致地理位置受延迟的影响时间更长。 读写工作负载和只读工作负载均由另一个地理位置的应用程序提供。 
* 只读工作负载须连接到每个区域中的另一个终结点。 


## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>业务连续性规划：选择用于云灾难恢复的应用程序设计
特定的云灾难恢复策略可组合或扩展这些设计模式以最好地满足应用程序需求。  如上所述，选择的策略取决于要提供给客户的 SLA 和应用程序部署拓扑。 为了帮助用户进行决策，下表基于恢复点目标 (RPO) 和估计的恢复时间 (ERT) 比较了相关选项。

| 模式 | RPO | ERT |
|:--- |:--- |:--- |
| 使用归置的数据库访问权限进行灾难恢复的主动-被动部署 |读写访问 < 5 秒 |故障检测时间 + DNS TTL |
| 实现应用程序负载均衡的主动-主动部署 |读写访问 < 5 秒 |故障检测时间 + DNS TTL |
| 实现保留数据的主动-被动部署 |只读访问 < 5 秒 | 只读访问 = 0 |
||读写访问 = 0 | 读写访问 = 故障检测时间 + 数据丢失宽限期 |
|||

## <a name="next-steps"></a>后续步骤
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
* 若要了解异地复制和故障转移组的详细信息，请参阅[活动异地复制](sql-database-geo-replication-overview.md)  
* 有关弹性池的活动异地复制的信息，请参阅[弹性池灾难恢复策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
