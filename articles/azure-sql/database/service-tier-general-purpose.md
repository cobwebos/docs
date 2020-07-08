---
title: “常规用途”服务层级
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 了解 Azure SQL 数据库和 Azure SQL 托管实例的常规用途服务层。
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
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986635"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>常规用途服务层-Azure SQL 数据库和 Azure SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 基于 vCore 的购买模型中的常规用途服务层在基于 DTU 的购买模型中称为标准服务层。 有关基于 vCore 的购买模型与基于 DTU 的购买模型的比较，请参阅[购买模型和资源](purchasing-models.md)。

Azure SQL 数据库和 Azure SQL 托管实例基于 SQL Server 数据库引擎体系结构，适用于云环境，以确保即使在发生基础结构故障的情况下，也能确保99.99% 的可用性。 

Azure SQL 数据库和 SQL 托管实例使用两个服务层： 

- 常规用途
- 业务关键

Azure SQL Database 还具有第三个服务层，此服务层当前不适用于 Azure SQL 托管实例：

- 超大规模

常规用途服务层的体系结构模型基于计算和存储的分离。 此体系结构模型依赖于 Azure Blob 存储的高可用性和可靠性，旨在以透明的方式复制数据库文件，并确保在发生基础结构故障时不会丢失数据。

下图显示了计算层和存储层已隔离的标准体系结构模型中的四个节点。

![计算和存储隔离](./media/service-tier-general-purpose/general-purpose-service-tier.png)

在常规用途服务层的体系结构模型中，有两个层：

- 无状态计算层：运行 `sqlservr.exe` 进程，仅包含暂时性的缓存数据（例如 - 计划缓存、缓冲池和列存储池）。 此无状态节点由 Azure Service Fabric 操作，该节点初始化进程，控制节点的运行状况，并在必要时执行故障转移到另一个位置。
- 有状态数据层：包含存储在 Azure Blob 存储中的数据库文件 (.mdf/.ldf)。 Azure Blob 存储保证任何数据库文件中的任何记录不会发生数据丢失。 Azure 存储具有内置的数据可用性/冗余，可确保即使进程崩溃，也会保留日志文件或数据文件中的页上的所有记录。

只要数据库引擎或操作系统升级，底层基础结构的某些部分就会失败，或者如果在此过程中检测到某个关键问题 `sqlservr.exe` ，Azure Service Fabric 会将无状态进程移到另一个无状态计算节点。 发生主节点故障转移时，将有一组备用节点等待运行新的计算服务，以尽量减少故障转移时间。 Azure 存储层中的数据不会受到影响，并且数据/日志文件会附加到新初始化的进程。 此过程保证99.99% 的可用性，但对于由于转换时间而运行的繁重工作负荷，此过程可能会有一些性能影响，而新节点则以冷缓存开头。

## <a name="when-to-choose-this-service-tier"></a>何时选择此服务层级

常规用途服务层是 Azure SQL 数据库和 Azure SQL 托管实例中的一个默认服务层，适用于大多数常规工作负载。 如果需要一个具有 99.99% SLA 的完全托管数据库引擎，在大多数情况下，与 Azure 虚拟机上的 SQL Server 匹配的存储延迟介于5到 10 ms 之间，则可以选择常规用途层。

## <a name="next-steps"></a>后续步骤

- 在[SQL 托管实例](../managed-instance/resource-limits.md#service-tier-characteristics)中查找 "常规用途/标准" 层的资源特征（核心数、i/o、内存），在 "vCore"[模型](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4)或[dtu 模型](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)中查找单个数据库，或在[vCore 模型](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4)和[dtu 模型](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits)中查找弹性池。
- 了解[业务关键](service-tier-business-critical.md)和[超大规模](service-tier-hyperscale.md)层。
- 了解 [Service Fabric](../../service-fabric/service-fabric-overview.md)。
- 有关高可用性和灾难恢复的更多选项，请参阅[业务连续性](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
