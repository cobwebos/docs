---
title: "高可用性 - Azure SQL 数据库服务 | Microsoft Docs"
description: "了解 Azure SQL 数据库服务的高可用性功能和特性"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
ms.assetid: 
ms.service: sql-database
ms.custom: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 12/13/2017
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: c0a140c959f14c2e8ceaddad5d323f0900be5d2f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL 数据库
自 Azure SQL 数据库 PaaS 服务推出以来，Microsoft 已向客户承诺在该服务中内置高可用性 (HA)，客户无需操作和添加特殊的逻辑，或者围绕 HA 做出决策。 Microsoft 为客户提供 SLA，并对 HA 系统的配置和操作保持完全控制。 HA SLA 适用于区域中的 SQL 数据库，对于超出 Microsoft 可合理控制的因素（例如，自然灾害、战争、恐怖活动、暴动、政府措施，或者 Microsoft 数据中心外部（包括客户站点，或者客户站点与 Microsoft 数据中心之间的位置）的网络或设备故障）所造成的整个区域范围的故障，它不能提供保护。

为了简化 HA 的问题范围，Microsoft 做出以下假设：
1.  硬件和软件故障不可避免
2.  操作人员会犯下导致故障的错误
3.  计划内维护操作会导致服务中断 

尽管上述每种事件不太频繁出现，但在云规模下，这些事件即使不会每天发生，也会每周发生。 

## <a name="fault-tolerant-sql-databases"></a>容错的 SQL 数据库
客户主要对其自身数据库的复原能力感兴趣，而对整个 SQL 数据库服务的复原能力不太感兴趣。 如果“我的数据库”属于 0.01% 的已停止数据库，则服务在 99.99% 的时间保持运行就会变得毫无意义。 每个数据库需有容错能力，故障缓解永远不应导致已提交事务的丢失。 

SQL 数据库使用基于直接附加磁盘/VHD 的本地存储 (LS) 和基于 Azure 高级存储页 Blob 的远程存储 (RS) 来存储数据。 
- 本地存储在针对具有较高 IOPS 要求的 OLTP 应用程序设计的高级数据库和池中使用。 
- 远程存储用于基本和标准服务层，这些层针对要求存储和计算能力可单独缩放的小型、低活动或大型数据库而设计。 这些层使用用于存储数据库和日志文件的单个页 Blob，并使用内置的存储复制和故障转移机制。

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

SQL 数据库中的高可用性解决方案基于 SQL Server 的 [AlwaysON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) 技术，同时适用于 LS 和 RS 数据库，在两者中的差异很小。 在 LS 配置中，AlwaysON 用于实现持久性；在 RS 中，它用于实现可用性（降低 RTO）。 

## <a name="local-storage"></a>本地存储

在 LS 配置中，每个数据库由管理服务 (MS) 在控件环中联机。 跨越同一数据中心内三个独立物理子系统的租户环中包含一个主要副本以及至少两个次要副本（仲裁集）。 所有读取和写入内容由网关 (GW) 发送到主要副本，写入内容以异步方式复制到次要副本。 SQL 数据库使用基于仲裁的提交方案，在此方案中，会先将数据写入主要副本以及至少一个次要副本，然后提交事务。

当节点发生故障时，[Service Fabric](/azure/service-fabric/service-fabric-overview.md) 故障转移系统会自动重新生成副本；当节点脱离和加入系统时，它会保留仲裁集成员资格。 计划内维护经过谨慎的协调，可防止仲裁集减少到最小副本计数（通常为 2）以下。 此模型非常适合高级数据库，但需要在计算和存储组件上配置冗余，因此会产生较高的成本。

## <a name="remote-storage"></a>远程存储

对于远程存储配置（基本和标准层），只会在远程 Blob 存储中数据库维护一个副本，利用存储系统的功能来实现持久性、冗余和位衰减检测。 

下图演示了这种高可用性体系结构：
 
![高可用性体系结构](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection--recovery"></a>故障检测和恢复 
大规模分布式系统需要一个能够快速可靠地检测故障且尽量靠近客户的故障检测系统。 对于 SQL 数据库而言，此系统基于 Azure Service Fabric。 

对于主要副本，它能立即查明主要副本是否以及何时发生故障且工作无法继续进行，因为所有读取和写入操作首先都在主要副本上发生。 将次要副本提升为主要副本状态的过程会导致 30 秒恢复时间目标 (RTO) 和 0 恢复点目标 (RPO)。 为了缓解 30 秒 RTO 造成的影响，最佳做法是在连接失败时，以较小的等待时间重试连接多次。

当次要副本发生故障时，数据库将缩减到最少量的仲裁集，且没有备用的副本。 Service Fabric 启动的重新配置过程类似于主要副本发生故障后启动的过程，因此，只需在经过片刻的等待后，就能确定故障是否为永久性的并创建另一个次要副本。 如果出现暂时性的服务中断状态（例如操作系统故障或升级），不会立即生成新副本，使故障节点能够重启。 

对于远程存储配置，SQL 数据库在升级期间使用 AlwaysON 功能来故障转移数据库。 为此，在执行计划内升级事件过程中，将会提前运转一个新的 SQL 实例，用于从远程存储附加和恢复数据库文件。 如果发生进程崩溃或其他计划外事件，Windows Fabric 会管理实例可用性，并附加远程数据库文件（最后一个恢复步骤）。

## <a name="conclusion"></a>结束语
Azure SQL 数据库与 Azure 平台深度集成，严重依赖于使用 Service Fabric 来执行故障检测和恢复，并严重依赖于使用 Azure 存储 Blob 来执行数据保护。 同时，Azure SQL 数据库使用 SQL Server 现成产品的 AlwaysOn 技术来执行复制和故障转移。 将这些技术相结合，应用程序可完全实现混合存储模型的优势并支持最严格的 SLA。 

## <a name="next-steps"></a>后续步骤

- 了解 [Service Fabric](/azure/service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理器](/traffic-manager/traffic-manager-overview.md) 
