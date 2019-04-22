---
title: 业务关键层 - Azure SQL 数据库服务 | Microsoft Docs
description: 了解 Azure SQL 数据库业务关键层
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: e9f40e749642f2025c5298df74f9d8ff87aec14b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784050"
---
# <a name="business-critical-tier---azure-sql-database"></a>业务关键层 - Azure SQL 数据库

> [!NOTE]
> 业务关键层在 DTU 购买模型中称为“高级”。 有关基于 vCore 购买模型与基于 DTU 购买模型的比较，请参阅 [Azure SQL 数据库购买模型和资源](sql-database-purchase-models.md)。

Azure SQL 数据库基于 SQL Server 数据库引擎体系结构，该体系结构已根据云环境做出调整，以确保即使在发生基础结构故障时，也仍能提供 99.99% 的可用性。 Azure SQL 数据库中使用了三种体系结构模型：
- 常规用途/标准 
- 业务关键/高级
- 超大规模

高级/业务关键服务层模型基于数据库引擎进程群集。 此体系结构模型依赖于以下事实：即使在执行维护活动期间，也始终存在可用数据库引擎节点的仲裁，并且能尽量减少对工作负荷性能的影响。

Azure 以透明方式升级和修补底层操作系统、驱动程序和 SQL Server 数据库引擎，同时尽量减少最终用户的停机时间。 

Azure SQL 数据库的“高级”或“业务关键”服务层中已启用高级可用性，此功能面向密集型工作负荷，此类工作负荷无法承受由于持续维护操作而造成的任何性能影响。

在高级模型中，Azure SQL 数据库在单个节点上集成了计算和存储层。 此体系结构模型中的高可用性是通过使用类似于 SQL Server [AlwaysOn 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技术复制部署在四节点集群中的计算（SQL Server 数据库引擎进程）和存储（本地连接的 SSD）来实现的。

![数据库引擎节点群集](media/sql-database-managed-instance/business-critical-service-tier.png)

SQL 数据库引擎进程和基础 mdf/ldf 文件都放置在同一个节点上，该节点在本地附加了 SSD 存储，使工作负荷保持较低的延迟。 高可用性是通过类似于 SQL Server [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)的技术实现的。 每个数据库是由数据库节点组成的群集，该群集中的一个主数据库可由客户工作负荷访问，还有三个辅助进程包含数据副本。 主节点不断地将更改推送到辅助节点，以确保在主节点出于任何原因崩溃时，可在次要副本上提供数据。 故障转移由 SQL Server 数据库引擎处理 – 一个次要副本成为主节点，并创建新的次要副本来确保群集中有足够的节点。 工作负荷自动重定向到新的主节点。

此外，业务关键群集具有内置的[读取扩展](sql-database-read-scale-out.md)功能，该功能提供免费的内置只读节点，可用于运行不会影响主要工作负荷性能的只读查询（例如报告）。

## <a name="when-to-choose-this-service-tier"></a>何时选择此服务层？

业务关键服务层为具有以下特点的应用程序而设计：需要来自基础 SSD 存储的低延迟响应（平均 1-2 毫秒）、在底层基础结构发生故障时需要快速恢复或是需要将报表、分析和只读查询分流到主数据库的免费可读次要副本。

## <a name="next-steps"></a>后续步骤

- 了解[常规用途](sql-database-service-tier-general-purpose.md)和[超大规模](sql-database-service-tier-hyperscale.md)层。
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)。
- 有关高可用性和灾难恢复的更多选项，请参阅[业务连续性](sql-database-business-continuity.md)。
