---
title: "使用 Azure SQL 数据库设计高可用性服务 | Microsoft Docs"
description: "了解使用 Azure SQL 数据库对应用程序设计高可用性服务。"
keywords: "云灾难恢复, 灾难恢复解决方案, 应用数据备份, 异地复制, 业务连续性规划"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: e8a346ac-dd08-41e7-9685-46cebca04582
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 04/21/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: b1b67a83a25159414a80382030903d300aad71f7
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>使用 Azure SQL 数据库设计高可用性服务

在 Azure SQL 数据库上构建和部署高度可用的服务时，使用[故障转移组和活动异地复制](sql-database-geo-replication-overview.md)提供区域故障和灾难性中断的复原能力并启用快速恢复到辅助数据库。 本文重点介绍常见应用程序模式，并根据应用程序部署要求、要针对的服务级别协议、流量延迟和成本探讨每个选项的优势和不足之处。 有关弹性池的活动异地复制的信息，请参阅[弹性池灾难恢复策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>设计模式 1：使用归置数据库进行云灾难恢复的主动-被动部署
此选项最适合具有以下特征的应用程序：

* 单个 Azure 区域中的活动实例
* 对数据的读写 (RW) 访问具有很强依赖性
* 由于延迟和流量成本，Web 应用程序与数据库之间的跨区域连接是不可接受的    

在这种情况下，当所有应用程序组件均受到影响并且需要作为一个单元进行故障转移时，将针对处理区域灾难对应用程序部署拓扑进行优化。 对于地理冗余，应用程序逻辑和数据库会复制到另一个区域，但在正常情况下它们不用于应用程序工作负荷。 次要区域中的应用程序应配置为使用辅助数据库的 SQL 连接字符串。 流量管理器设置为使用[故障转移路由方法](../traffic-manager/traffic-manager-configure-failover-routing-method.md)。  

> [!NOTE]
> [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)在这篇文章中仅供说明之用。 你可以使用任何支持故障转移路由方法的负载均衡解决方案。    
>

下图显示了在发生服务中断之前的此配置。

![SQL 数据库异地复制配置。 云灾难恢复。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

主要区域服务中断后，SQL 数据库服务会检测到主数据库不可访问，并基于自动故障转移策略的参数触发到辅助数据库的故障转移。 根据应用程序 SLA，可决定配置在检测到服务中断和故障转移之间的宽限期。 配置宽限期在出现灾难性中断和无法快速恢复区域可用性时可降低数据丢失风险。 如果流量管理器在故障转移组触发数据库故障转移之前启动终结点故障转移，则 Web 应用程序将无法重新连接到数据库。 数据库故障转移完成时，即可自动成功完成应用程序重新连接尝试。 

> [!NOTE]
> 若要实现应用程序和数据库的完全协调故障转移，应设计适合自己的监视方法，并手动故障转移 Web 应用程序终结点和数据库。
>

应用程序终结点和数据库的故障转移完成后，应用程序会重新开始处理区域 B 中的用户请求，并且由于主数据库现位于区域 B 中，应用程序将与数据库继续共存。以下关系图中说明了这种情况。 在所有关系图中，实线表示活动连接，虚线表示暂停的连接，停止符号表示操作触发。

![异地复制：故障转移到辅助数据库。 应用数据备份。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

如果服务中断发生在次要区域中，主数据库和辅助数据库之间的复制链接将暂停，但由于主数据库未受影响，因此不会触发故障转移。 这种情况下，应用程序可用性未发生改变，但是暴露了应用程序的运行，因此在两个区域连续失败的情况下应用程序具有更高风险。

> [!NOTE]
> 我们仅建议使用单个 DR 区域进行部署配置。 这是因为大多数 Azure 地理位置都有两个区域。 这些配置不会保护你的应用程序免受这两个区域的灾难性故障的影响。 在此类失败的不可能事件中，你可以使用[异地恢复操作](sql-database-disaster-recovery.md#recover-using-geo-restore)在第三个区域中恢复数据库。
>

服务中断缓解后，辅助数据库自动与主数据库重新同步。 在同步过程中，主数据库的性能可能会略微受影响，具体取决于需要进行同步的数据量。 下图说明了次要区域中的服务中断。

![辅助数据库与主数据库同步。 云灾难恢复。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)

此设计模式的主要**优点**是：

* 无需任何区域特定配置和其他响应故障转移的逻辑即可将同一 Web 应用程序部署到这两个区域。 
* 应用程序性能不受故障转移影响，因为 Web 应用程序和数据库始终共存。

主要**不足**是次要区域中的冗余应用程序实例仅用于灾难恢复。

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>设计模式 2：实现应用程序负载均衡的主动-主动部署
此云灾难恢复选项最适合具有以下特征的应用程序：

* 数据库读取与写入的比率较高
* 对于最终用户体验而言，数据库读取延迟比写入延迟更加重要 
* 可使用不同的连接字符串将只读逻辑与读写逻辑分开
* 只读逻辑不依赖于正在与最新更新完全同步的数据  

如果应用程序具有这些特征，在不同区域中的多个应用程序实例间对最终用户连接进行负载均衡可显著改善最终用户整体体验。 应选择其中两个区域作为 DR 对，故障转移组应包含这些区域中的数据库。 若要实现负载均衡，每个区域应具有一个应用程序活动实例，并将读写 (RW) 逻辑连接到故障转移组的读写侦听器终结点。 这可保证在主数据库受服务中断影响时自动启动故障转移。 Web 应用程序中的只读逻辑 (RO) 应直接连接到该区域中的数据库。 流量管理器应设置为使用[性能路由](../traffic-manager/traffic-manager-configure-performance-routing-method.md)，并为每个应用程序实例启用[终结点监视](../traffic-manager/traffic-manager-monitoring.md)。

如模式 #1 中所示，你应考虑部署类似的监视应用程序。 但与模式 #1 不同的是，该监视应用程序不负责触发终结点故障转移。

> [!NOTE]
> 虽然此模式使用多个辅助数据库，但只有区域 B 中的辅助数据库将用于故障转移，并成为故障转移组的一部分。
>

应针对性能路由配置流量管理器以引导应用程序实例的用户连接最接近用户的地理位置。 下图说明了在发生服务中断之前的此配置。

![无服务中断：性能路由到最近的应用程序。 异地复制。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

如果区域 A 中检测到数据库中断，则故障转移组将自动启动将区域 A 中的主数据库故障转移到区域 B 中辅助数据库。它还会将读写侦听器终结点更新到区域 B，使 Web 应用程序中的读写连接不受影响。 流量管理器从路由表中排除脱机终结点，但继续将最终用户流量路由到剩余的联机实例。 只读 SQL 连接字符串不受影响，因为它们始终指向同一区域中的数据库。 

下图说明了在故障转移后的新配置。

![故障转移后的配置。 云灾难恢复。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

如果其中一个次要区域发生服务中断，流量管理器自动从路由表中删除该区域的脱机终结点。 该区域中辅助数据库的复制通道处于挂起状态。 由于此时剩余区域的用户流量增多，因此在服务中断期间应用程序的性能会受到影响。 服务中断的情况得到缓解后，受影响区域中的辅助数据库立即与主数据库进行同步。 在同步过程中，主数据库的性能可能会略微受影响，具体取决于需要进行同步的数据量。 下图说明了区域 B 中的服务中断。

![次要区域中的服务中断。 云灾难恢复 - 异地复制。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

此设计模式的主要**优点**是，可以缩放多个辅助数据库上的应用程序工作负荷，获得最佳最终用户性能。 此选项的**不足**是：

* 应用程序实例与数据库之间的读写连接具有不同的延迟和成本
* 在服务中断期间，应用程序性能会受到影响

> [!NOTE]
> 可以使用类似的方法卸载专用工作负荷，例如报告作业、商业智能工具或备份。 通常这些工作负荷占用大量数据库资源，因此建议你为它们指定其中一个辅助数据库，并使性能级别与预期的工作负荷匹配。
>

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>设计模式 3：实现保留数据的主动-被动部署
此选项最适合具有以下特征的应用程序：

* 丢失任何数据都会对业务带来高风险。 数据库故障转移仅用作出现灾难性服务中断时的最后解决措施。
* 应用程序支持只读和读写操作模式，可在“只读模式”下运行一段时间。

此模式下，读写连接开始出现超时错误时，应用程序会切换到只读模式。 Web 应用程序部会署到这两个区域，且包含一个读写侦听器终结点连接和另一个不同的只读侦听器终结点连接。 流量管理器应设置为使用[故障转移路由](../traffic-manager/traffic-manager-configure-failover-routing-method.md)，并为每个区域中的应用程序终结点启用[终结点监视](../traffic-manager/traffic-manager-monitoring.md)。

下图说明了在发生服务中断之前的此配置。

![故障转移之前的主动-被动部署。 云灾难恢复。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

流量管理器检测到区域 A 的连接故障时，会自动将用户流量切换到区域 B 中的应用程序实例。此模式下，必须将数据丢失宽限期设置为足够大的值（例如，24 小时）。 如果在该时间段内解决了中断问题，该措施可确保防止数据丢失。 区域 B 中的 Web 应用程序激活时，读写操作将失败。 此时，应切换到只读模式。 此模式下，请求会自动路由至辅助数据库。 在出现灾难性故障的情况下，在宽限期内无法解决中断问题，故障转移组将触发故障转移。 读写侦听器可用后，对它的调用将不再发生故障。 下图对此进行了说明。

![服务中断：只读模式下的应用程序。 云灾难恢复。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

如果在宽限期内解决了主要区域中的中断问题，流量管理器会检测到主要区域的连接恢复，并将用户流量切换回区域 A 中的应用程序实例。此应用程序实例使用区域 A 中的主数据库在读写模式下进行恢复和运行。

如果在区域 B 中出现服务中断，流量管理器会检测到区域 B 中应用程序终结点出现故障，故障转移组会将只读侦听器切换到区域 A。此服务中断不会影响最终用户体验，但服务中断期间会公开主数据库。 下图对此进行了说明。

![服务中断：辅助数据库。 云灾难恢复。](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

解决中断问题后，辅助数据库立即与主数据库同步，只读侦听器切换回区域 B 中的辅助数据库。同步期间，主数据库性能可能略受影响，具体取决于需同步的数据量。

此设计模式具有多个**优点**：

* 它在临时服务中断期间可避免数据丢失。
* 停机时间仅取决于流量管理器检测到连接故障的速度，此速度是可配置的。

缺点：

* 应用程序必须能够在只读模式下运行。

> [!NOTE]
> 在区域中发生永久服务中断时，请手动激活数据库故障转移并接受数据丢失。 应用程序将在次要区域中正常工作，并对数据库具有读写访问权限。
>

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>业务连续性规划：选择用于云灾难恢复的应用程序设计
特定的云灾难恢复策略可组合或扩展这些设计模式以最好地满足你的应用程序需求。  如上所述，选择的策略取决于要提供给客户的 SLA 和应用程序部署拓扑。 为了帮助用户进行决策，下表基于估计的数据丢失或恢复点目标 (RPO) 和估计的恢复时间 (ERT) 比较了相关选项。

| 模式 | RPO | ERT |
|:--- |:--- |:--- |
| 使用归置的数据库访问权限进行灾难恢复的主动-被动部署 |读写访问 < 5 秒 |故障检测时间 + DNS TTL |
| 实现应用程序负载均衡的主动-主动部署 |读写访问 < 5 秒 |故障检测时间 + DNS TTL |
| 实现保留数据的主动-被动部署 |只读访问 < 5 秒 | 只读访问 = 0 |
||读写访问 = 0 | 读写访问 = 故障检测时间 + 数据丢失宽限期 |
|||

## <a name="next-steps"></a>后续步骤
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)
* 若要了解更快的恢复选项，请参阅[活动异地复制](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自动备份进行存档，请参阅[数据库复制](sql-database-copy.md)
* 有关弹性池的活动异地复制的信息，请参阅[弹性池灾难恢复策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

