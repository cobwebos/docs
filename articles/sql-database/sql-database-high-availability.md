---
title: 高可用性 - Azure SQL 数据库服务 | Microsoft Docs
description: 了解 Azure SQL 数据库服务的高可用性功能和特性
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: 2283b7559bb0dc7e8333949a8e6382d562162123
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092481"
---
# <a name="high-availability-and-azure-sql-database"></a>高可用性和 Azure SQL 数据库

Azure SQL 数据库是高可用性数据库平台即服务，可保证数据库在 99.99% 的时间内保持正常运行，让客户无需考虑维护和停机问题。 它是在 Azure 云中承载的完全托管型 SQL Server 数据库引擎进程，确保 SQL Server 数据库始终能够得到升级/修补，且不会影响工作负荷。 即使出现最严重的问题，Azure SQL 数据库也能快速恢复，确保数据始终可用。

Azure 平台完全管理每个 Azure SQL 数据库，保证不会丢失数据并保证高百分比的数据可用性。 Azure 会自动处理修补、备份、复制、故障检测；基础的潜在硬件、软件或网络故障；部署 bug 修复、故障转移、数据库升级和其他维护任务。 SQL Server 工程师已实施成熟的做法，确保在数据库生命周期的不到 0.01% 的时间内就能完成所有维护操作。 此体系结构旨在确保提交的数据永远不会丢失，且执行的维护操作不会影响工作负荷。 在升级或维护数据库期间，维护或停机时段都不需要停止工作负荷。 Azure SQL 数据库中的内置高可用性保证该数据库永远不会成为软件体系结构中的单一故障点。

Azure SQL 中应用两种高可用性模型：

- 标准/常规用途模型：提供 99.99% 的可用性，但在执行维护活动期间性能可能会出现某种程度的下降。
- 高级/业务关键型模型：也提供 99.99% 的可用性，但即使是在执行维护活动期间，也能尽量降低工作负荷的性能影响。

Azure 以透明方式升级和修补底层操作系统、驱动程序和 SQL Server 数据库引擎，同时尽量减少最终用户的停机时间。 Azure SQL 数据库在最新稳定版本的 SQL Server 数据库引擎和 Windows OS 上运行，大多数用户不会察觉到正在持续执行升级。

## <a name="standard-availability"></a>标准可用性

标准可用性是指在标准/基本/常规用途层中应用的 99.99% SLA。 可用性是通过将计算层与存储层相隔离来实现的。 标准可用性模型包含两个层：

- 无状态计算层：运行 sqlserver.exe 进程，仅包含暂时性的缓存数据（例如 – 计划缓存、缓冲池和列存储池）。 此无状态 SQL Server 节点由 Azure Service Fabric 操作。Service Fabric 初始化进程、控制节点运行状况，并根据需要执行到另一位置的故障转移。
- 有状态数据层：包含存储在 Azure 高级存储磁盘中的数据库文件 (.mdf/.ldf)。 Azure 存储保证任何数据库文件中的任何记录不会发生数据丢失。 Azure 存储具有内置的数据可用性/冗余，确保即使 SQL Server 进程崩溃，也能保留日志文件中的每条记录或者数据文件中的页面。

每当升级数据库引擎或操作系统，或者在 SQL Server 进程中检测到某个严重问题时，Azure Service Fabric 会将无状态 SQL Server 进程移到另一个无状态计算节点。 Azure 存储层中的数据不受影响，数据/日志文件将附加到新初始化的 SQL Server 进程。 可以秒为单位计量预期的故障转移时间。 此进程保证 99.99% 的可用性，但可能会对正在运行的重型工作负荷造成一定的性能影响，原因是转换时间较长，并且新 SQL Server 节点从冷缓存启动。

## <a name="premium-availability"></a>高级可用性

Azure SQL 数据库的高级层中已启用高级可用性，此功能面向密集型工作负荷，此类工作负荷无法容忍由于持续维护操作而造成的任何性能影响。

在高级模型中，Azure SQL 数据库在单个节点上集成了计算和存储层。 SQL Server 数据库引擎进程和底层 mdf/ldf 文件都放置在同一个节点上，该节点在本地附加了 SSD 存储，使工作负荷保持较低的延迟。

使用标准 [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)实现高可用性。 每个数据库是由数据库节点组成的群集，该群集中的一个主数据库可由客户工作负荷访问，还有一些辅助进程包含数据副本。 主节点不断地将更改推送到辅助节点，以确保在主节点出于任何原因崩溃时，可在次要副本上提供数据。 故障转移由 SQL Server 数据库引擎处理 – 一个次要副本成为主节点，并创建新的次要副本来确保群集中有足够的节点。 工作负荷自动重定向到新的主节点。 故障转移时间以毫秒为单位计量，新的主实例可立即开始为请求提供持续服务。

## <a name="zone-redundant-configuration"></a>区域冗余配置

默认情况下，本地存储配置的仲裁集是在相同的数据中心中创建的。 通过引入 [Azure 可用性区域](../availability-zones/az-overview.md)，可以将程序集中的不同副本放到同一区域中的不同可用性区域中。 若要消除单一故障点，还要将控件环跨区域地复制为三个网关环 (GW)。 到特定网关环的路由受 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) (ATM) 控制。 区域冗余配置不会创建其他数据库冗余，因此在“高级”或“业务关键”服务层中使用可用性区域不需要承担任何额外费用。 通过选择区域冗余数据库，可以让“高级”或“业务关键”数据库能应对范围更广的故障，包括灾难性的数据中心服务中断，且不会对应用程序逻辑产生任何更改。 还可以将所有现有“高级”或“业务关键”数据库或池转换到区域冗余配置。

由于区域冗余仲裁集的副本位于不同数据中心，且互相之间都有一定距离，因此增加的网络延迟可能会延长提交时间，从而影响某些 OLTP 工作负载的性能。 始终可以通过禁用区域冗余设置返回到单个区域配置。 此进程是一种数据操作，并且与常规的服务等级目标 (SLO) 更新类似。 在此进程结束时，该数据库或池将从区域冗余环迁移到单个区域环，反之亦然。

下图演示了高可用性体系结构的区域冗余版本：
 
![高可用性体系结构区域冗余](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>读取横向扩展
如上所述，在单区域和区域冗余配置中，“高级”和“业务关键”服务层均利用仲裁集和 Always On 技术实现高可用性。 Always On 的优势之一是副本始终处于事务一致状态。 因为副本具有与主数据库相同的性能级别，因此，应用程序可以利用该额外容量为只读工作负荷提供服务（读取横向扩展），不需要额外付费。 这样，只读查询将与主要的读写工作负荷相隔离，不会影响其性能。 读取横向扩展功能适用于其中包括逻辑上独立的只读工作负荷（例如分析）的应用程序，因此可以利用此额外的容量而不需要连接到主数据库。 

若要将读取扩展功能用于特定的数据库，必须在创建数据库时或者在之后通过更改其配置来显式激活此功能，可以采用以下方式执行此操作：使用 PowerShell 调用 [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) 或 [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) cmdlet，或者通过 Azure 资源管理器 REST API 使用[数据库 - 创建或更新](/rest/api/sql/databases/createorupdate)方法。

为某个数据库启用读取横向扩展后，会根据在应用程序的连接字符串中配置的 `ApplicationIntent` 属性将连接到该数据库的应用程序定向到该数据库的读写副本或只读副本。 有关 `ApplicationIntent` 属性的信息，请参阅[指定应用程序意向](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。 

如果禁用了“读取扩展”，或在不支持的服务层中设置了 ReadScale 属性，则所有连接都将定向到读写副本，而与 `ApplicationIntent` 属性无关。  

> [!NOTE]
> 可以在标准或常规用途数据库上激活读取扩展，即使不会导致只读的预期会话路由到单独副本也是如此。 这样做是为了支持在“标准/常规用途”和“高级/业务关键”层之间横向扩展和纵向扩展的现有应用程序。  

读取横向扩展功能支持会话级一致性。 如果只读会话在由于副本不可用而出现连接错误后重新连接，则可以将其重定向到另一个副本。 它可能会导致处理陈旧的数据集，虽然可能性不大。 同样，如果应用程序使用读写会话写入数据，并立即使用只读会话读取该数据，则新数据可能不会立即可见。

## <a name="conclusion"></a>结束语
Azure SQL 数据库与 Azure 平台深度集成，严重依赖于使用 Service Fabric 来执行故障检测和恢复，并严重依赖于使用 Azure 存储 Blob 来执行数据保护，以及使用可用性区域提高容错性。 同时，Azure SQL 数据库充分利用 SQL Server 现成产品的 Always On 可用性组技术来执行复制和故障转移。 将这些技术相结合，应用程序可完全实现混合存储模型的优势并支持最严格的 SLA。 

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 可用性区域](../availability-zones/az-overview.md)
- 了解 [Service Fabric](../service-fabric/service-fabric-overview.md)
- 了解 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md) 
