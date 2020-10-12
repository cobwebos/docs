---
title: “常规用途”服务层级
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 了解 Azure SQL 数据库和 Azure SQL 托管实例的“常规用途”服务层级。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: ee218253309995e721c97f4a7f7b4547b32f7c36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85986635"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>“常规用途”服务层级 - Azure SQL 数据库和 Azure SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 基于 vCore 的购买模型中的“常规用途”服务层级在基于 DTU 的购买模型中称为“标准”服务层级。 有关基于 vCore 的购买模型与基于 DTU 的购买模型的比较，请参阅[购买模型和资源](purchasing-models.md)。

Azure SQL 数据库和 Azure SQL 托管实例基于 SQL Server 数据库引擎体系结构，该体系结构已根据云环境做出调整，以确保即使在发生基础结构故障时，也仍能提供 99.99% 的可用性。 

Azure SQL 数据库和 SQL 托管实例使用两个服务层级： 

- 常规用途
- 业务关键

Azure SQL 数据库还具有第三个服务层级，此服务层级当前不适用于 Azure SQL 托管实例：

- 超大规模

“常规用途”服务层级的体系结构模型基于计算和存储的分离。 此体系结构模型依赖于 Azure Blob 存储的高可用性和可靠性，旨在以透明的方式复制数据库文件，并确保在发生基础结构故障时不会丢失数据。

下图显示了计算层和存储层已隔离的标准体系结构模型中的四个节点。

![计算和存储隔离](./media/service-tier-general-purpose/general-purpose-service-tier.png)

“常规用途”服务层级的体系结构模型中有两个层：

- 无状态计算层：运行 `sqlservr.exe` 进程，仅包含暂时性的缓存数据（例如 - 计划缓存、缓冲池和列存储池）。 此无状态节点由 Azure Service Fabric 操作。Service Fabric 初始化进程、控制节点运行状况，并根据需要执行到另一位置的故障转移。
- 有状态数据层：包含存储在 Azure Blob 存储中的数据库文件 (.mdf/.ldf)。 Azure Blob 存储保证任何数据库文件中的任何记录不会发生数据丢失。 Azure 存储具有内置的数据可用性/冗余，确保即使进程崩溃，也能保留日志文件中的每条记录或者数据文件中的页面。

每当升级数据库引擎或操作系统、底层基础结构的某个部件发生故障，或者在 `sqlservr.exe` 进程中检测到某个严重问题时，Azure Service Fabric 会将无状态进程移到另一个无状态计算节点。 发生主节点故障转移时，将有一组备用节点等待运行新的计算服务，以尽量减少故障转移时间。 Azure 存储层中的数据不受影响，数据/日志文件将附加到新初始化的进程。 此进程保证 99.99% 的可用性，但可能会对正在运行的重型工作负荷造成一定的性能影响，原因是转换时间较长，并且新节点从冷缓存启动。

## <a name="when-to-choose-this-service-tier"></a>何时选择此服务层级

“常规用途”服务层级是 Azure SQL 数据库和 Azure SQL 托管实例中的默认服务层级，专为大多数常规工作负载而设计。 若需要 SLA 达 99.99% 且存储延迟在 5 到 10 毫秒之间的完全托管数据库引擎，且在大多数情况下与 Azure 虚拟机上的 SQL Server 匹配，可以选择“常规用途”层级。

## <a name="next-steps"></a>后续步骤

- 在 [SQL 托管实例](../managed-instance/resource-limits.md#service-tier-characteristics)中查找“常规用途/标准”层级的资源特征（核心数、I/O 数、内存数），在 [vCore 模型](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4)或 [DTU 模型](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)中查找单一数据库，在 [vCore 模型](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4)和 [DTU 模型](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits)中查找弹性池。
- 了解[业务关键](service-tier-business-critical.md)和[超大规模](service-tier-hyperscale.md)层。
- 了解 [Service Fabric](../../service-fabric/service-fabric-overview.md)。
- 有关高可用性和灾难恢复的更多选项，请参阅[业务连续性](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
