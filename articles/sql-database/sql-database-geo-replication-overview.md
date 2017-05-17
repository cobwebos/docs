---
title: "故障转移组和活动异地复制 - Azure SQL 数据库 | Microsoft Docs"
description: "利用自动故障转移组与活动异地复制，可在任何 Azure 数据中心设置数据库的 4 个副本，并可在发生故障时自动进行故障转移。"
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 2a29f657-82fb-4283-9a83-e14a144bfd93
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/26/2016
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 647dbeb96181fb9a6118b0cbca8dce9876d9a983
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="overview-failover-groups-and-active-geo-replication"></a>概述：故障转移组和活动异地复制
使用活动异地复制可在相同或不同数据中心位置（区域）中最多配置四个可读的辅助数据库。 在数据中心发生服务中断或无法连接到主数据库时，可以使用辅助数据库进行查询和故障转移。 故障转移必须由用户通过应用程序手动启动。 故障转移后，新的主数据库具有不同的连接终结点。 

> [!NOTE]
> 活动异地复制（可读辅助数据库）可供所有服务层中的所有数据库使用
>  

Azure SQL 数据库自动故障转移组是一项 SQL 数据库功能，主要用于自动管理大规模复制、连接和故障转移。 通过使用故障转移组，客户能够在发生后述情况后自动恢复次要区域中多个相关数据库：灾难性区域故障或其他导致主要区域中 SQL 数据库服务完全或部分丧失可用性的非计划事件。 此外，客户还可以使用可读辅助数据库卸载只读工作负荷。  由于自动故障转移组涉及多个数据库，因此必须在主服务器上进行配置。 主服务器和辅助服务器必须位于同一订阅。 自动故障转移组支持将组中所有的数据库复制到另一个区域中唯一的辅助服务器。 而活动异地复制允许最多复制到任何区域中的 4 个辅助数据库。

如果使用活动异地复制，且主数据库因某种原因而出现故障，或者如果只是需要脱机，则可以启动到任何辅助数据库的故障转移。 当将故障转移激活到辅助数据库之一时，所有其他辅助数据库会自动链接到新的主数据库。 如果使用自动故障转移组管理数据库恢复，并且任何影响组中一个或多个数据库的故障会导致自动故障转移。 可以配置最能满足应用程序需求的自动故障转移策略，或者可以选择退出并使用手动激活。 此外，自动故障转移组提供在故障转移期间保持不变的读写和只读侦听器终结点。 无论使用手动故障转移激活还是自动故障转移激活，故障转移都会将组中所有的辅助数据库切换到主数据库。 数据库故障转移完成后，将自动更新 DNS 记录，以便将终结点重定向到新的区域。

可以使用活动异地复制管理服务器或弹性池中单个数据库或一组数据库的复制和故障转移。 可以使用 [Azure 门户](sql-database-geo-replication-portal.md)、[PowerShell](sql-database-geo-replication-powershell.md)、[Transact-SQL](sql-database-geo-replication-transact-sql.md) 或 [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) 实现该操作。 故障转移后，请确保在新的主机上配置服务器和数据库的身份验证要求。 有关详细信息，请参阅[灾难恢复后的 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。 这同时适用于活动异地复制和自动故障转移组。

活动异地复制利用 SQL Server 的 [AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) 技术，使用读提交快照隔离 (RCSI) 以异步方式将主数据库上已提交的事务复制到辅助数据库。 自动故障转移组提供基于活动异地复制的组语义，但使用相同的异步复制机制。 尽管在任意给定时间，辅助数据库可能略微滞后于主数据库，但系统可以保证辅助数据永不包含部分事务。 跨区域冗余使应用程序能够在自然灾害、灾难性人为失误或恶意行为导致整个或部分数据中心永久性数据丢失后得以快速恢复。 [业务连续性概述](sql-database-business-continuity.md)中提供了具体的 RPO 数据。

下图显示了配置的活动异地复制示例，其中主数据库位于“美国中北部”区域，辅助数据库位于“美国中南部”区域。

![异地复制关系](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

因为辅助数据库是可读的，并且可用于卸载只读工作负荷，如报表作业。 如果使用活动异地复制，则可在主数据库所在的区域中创建辅助数据库，但不会增加应用程序针对灾难性故障的复原能力。 如果使用自动故障转移组，则会始终在不同的区域中创建辅助数据库。

除了灾难恢复外，活动异地复制还可用于以下情况：

* **数据库迁移**：可以使用活动异地复制将数据库在联机情况下从一台服务器迁移到另一台服务器，只需要极少的停机时间。
* **应用程序升级**：可以在应用程序升级期间创建额外的辅助数据库作为故障回复副本。

若要真正实现业务连续性，只需添加数据中心之间的数据库冗余即可，这只是该解决方案的一部分功能。 在发生灾难性故障后，端对端地恢复应用程序（服务）需要恢复构成该服务的所有组件以及所有依赖服务。 这些组件的示例包括客户端软件（例如，使用自定义 JavaScript 的浏览器）、Web 前端、存储和 DNS。 所有组件必须能够弹性应对相同的故障，并在应用程序的恢复时间目标 (RTO) 值内变为可用，这一点非常关键。 因此，你需要识别所有依赖服务，并了解它们提供的保证和功能。 然后，必须执行适当的步骤来确保对你的服务所依赖的服务执行故障转移期间，你的服务能够正常运行。 有关设计灾难恢复解决方案的详细信息，请参阅[设计使用活动异地复制的灾难恢复解决方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="active-geo-replication-capabilities"></a>活动异地复制功能
活动异地复制功能提供以下基本功能：

* **自动异步复制**：只能通过添加到现有数据库来创建辅助数据库。 辅助数据库可在任何 Azure SQL 数据库服务器中创建。 创建完成之后，使用从主数据库复制的数据填充辅助数据库。 这个过程称为种子设定。 创建辅助数据库并设定其种子后，会自动以异步方式将主数据库的更新复制到辅助数据库。 异步复制是指先在主数据库上提交事务，然后将事务复制到辅助数据库。 
* **可读的辅助数据库**：应用程序可以使用与访问主数据库时所用的相同或不同的安全主体来访问辅助数据库以执行只读操作。 辅助数据库在快照隔离模式下运行，以确保对主数据库的更新的复制（日志重播）不会因辅助数据库上执行的查询操作而延迟。

   > [!NOTE]
   > 如果日志重播正在从主数据库接收的内容存在架构更新，则它将在辅助数据库上延迟，因为此过程需要辅助数据库上的架构锁。 
   > 

* 多个可读辅助数据库：两个或多个辅助数据库可以提高主数据库和应用程序的冗余和保护级别。 如果存在多个辅助数据库，即使其中一个辅助数据库发生故障，应用程序仍会受到保护。 如果只有一个辅助数据库，一旦它发生故障，应用程序就会遭受更高的风险，直到创建了新的辅助数据库。

   > [!NOTE]
   > 如果使用活动异地复制生成全球分布的应用程序，并需要在 4 个以上的区域中提供数据只读访问，则可以创建辅助数据库的辅助数据库（称为链接的过程）。 采用这种方式几乎可以实现数据库复制的无限制缩放。 此外，链接减少了从主数据库进行复制的开销。 随之而来的弊端是增加了大多数辅助数据库上的复制延迟。 。 
   >

* 支持弹性池数据库：可以为任何弹性池中的任何数据库配置活动异地复制。 辅助数据库可以位于另一个弹性池中。 对于常规的数据库，辅助数据库可以是弹性池，反过来也一样，只要服务层相同。 
* **辅助数据库的可配置性能级别**：可以使用低于主数据库的性能级别创建辅助数据库。 主数据库和辅助数据库都需要有相同的服务层。 不建议将此选项用于具有高数据库写入活动的应用程序，因为复制延迟时间的增长会加大故障转移后大量数据丢失的风险。 此外，在故障转移后，应用程序的性能将受到影响，直到新的主数据库升级到更高的性能级别。 Azure 门户上的日志 IO 百分比图表提供了一种评估维持复制负荷所需的辅助数据库的最小性能级别的好办法。 例如，如果你的主数据库是 P6 (1000 DTU)，其日志 IO 百分比为 50%，则辅助数据库需要至少为 P4 (500 DTU)。 你还可以使用 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 或 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 数据库视图检索日志 IO 数据。  有关 SQL 数据库性能级别的详细信息，请参阅 [SQL 数据库选项和性能](sql-database-service-tiers.md)。 
* **用户控制的故障转移和故障回复**：应用程序或用户可随时将辅助数据库显式切换到主角色。 在实际服务中断期间，应使用“计划外”选项，这会立即将辅助数据库升级为主数据库。 当出现故障的主数据库恢复并再次可用时，系统会自动将恢复的主数据库标记为辅助数据库并使其与新的主数据库保持最新状态。 由于复制的异步特性，在未计划的故障转移期间，如果主数据库在将最新的更改复制到辅助数据库之前出现故障，则可能会丢失少量数据。 当具有多个辅助数据库的主数据库进行故障转移时，系统将自动重新配置复制关系，并将剩余辅助数据库链接到新升级的主数据库，无需任何用户的干预。 导致了故障转移的服务中断得到缓解后，可能需要将应用程序返回到主要区域。 为此，应使用“计划内”选项调用故障转移命令。 
* **保持凭据和防火墙规则同步**：建议将[数据库防火墙规则](sql-database-firewall-configure.md)用于异地复制数据库，以便这些规则可以和数据库一起复制，从而确保所有辅助数据库具有与主数据库相同的防火墙规则。 此方法不再需要客户手动配置和维护承载主数据库和辅助数据库的服务器上的防火墙规则。 同样，将[包含的数据库用户](sql-database-manage-logins.md)用于数据访问可确保主数据库和辅助数据库始终具有相同的用户凭据，以便在故障转移期间，不会因登录名和密码不匹配而产生中断。 通过添加 [Azure Active Directory](../active-directory/active-directory-whatis.md)，客户可以管理主数据库和辅助数据库的用户访问权限，且不再需要同时管理数据库中的凭据。

## <a name="auto-failover-group-capabilities"></a>自动故障转移组功能

通过支持组级别复制和自动故障转移，自动故障转移组功能提供功能强大的活动异地复制的抽象。 此外，通过提供更多侦听器终结点，该功能消除了在故障转移后更改 SQL 连接字符串的必要性。 

* 故障转移组：可在不同区域的两个服务器（主服务器和辅助服务器）之间创建一个或多个故障转移组。 每组可包含一个或多个数据库，这些数据库是在所有或某些主数据库因主要区域中的服务中断而变得不可用时，作为单元恢复的。  
* 将数据库添加到故障转移组：可以将一台服务器内或一个弹性池内的多个数据库放入同一故障转移组。 如果将独立数据库添加到组中，会自动使用相同的版本和性能级别创建辅助数据库。 如果主数据库在弹性池中，将使用相同的名称在弹性池中自动创建辅助数据库。 如果在辅助服务器中添加已具有辅助数据库的数据库，则异地复制将由组继承。

   > [!NOTE]
   > 在不属于故障转移组的服务器中添加已具有辅助数据库的数据库时，会在辅助服务器中创建新的辅助数据库。 
   >

* 故障转移组读写侦听器：指向当前主服务器 URL 的 DNS CNAME 记录。 它允许读写 SQL 应用程序在故障转移发生后主服务器发生更改时，以透明方式重新连接到主数据库。 
* 故障转移组只读侦听器：指向当前辅助服务器 URL 的 DNS CNAME 记录。 它允许只读 SQL 应用程序以透明方式使用指定的负载均衡规则连接到辅助数据库。 或者，可以指定是否要在辅助服务器不可用时将只读流量自动重定向到主服务器中。
* 自动故障转移策略：默认使用自动故障转移策略配置故障转移组。 检测到故障时，系统将立即触发故障转移。 如果要从应用程序控制故障转移工作流，可以关闭自动故障转移。 
* 手动故障转移：可在任何时候手动启动故障转移，而不考虑自动故障转移配置。 如果未配置自动故障转移策略，则需要进行手动故障转移来恢复故障转移组中的数据库。 可通过完整数据同步启动强制或友好的故障转移。 后者可用于将活动服务器重新定位到主要区域。 故障转移完成后，将自动更新 DNS 记录，以确保连接到正确的服务器。
* 数据丢失宽限期：因为主数据库和辅助数据库是使用异步复制进行同步的，所以故障转移可能会导致数据丢失。 可以自定义自动故障转移策略，以便反映应用程序对数据丢失的容错。 通过配置数据丢失宽限期，可以控制系统启动可能导致数据丢失的故障转移之前的等待时间。 

   > [!NOTE]
   > 系统检测到组中的数据库仍处于联机状态（例如服务中断仅影响服务控制平面）时，会立即使用完整数据同步激活故障转移（友好故障转移），而不考虑为数据丢失宽限期设置的值。 这是为了确保不会在恢复期间丢失数据。 宽限期仅在不可能进行友好故障转移时发挥作用。 如果在宽限期到期之前服务中断得到了缓解，则不会激活故障转移。
   >

* 多个故障转移组：可为同一对服务器配置多个故障转移组以控制故障转移规模。 每个组均独立进行故障转移。 如果多租户应用程序使用弹性池，则可使用此功能来混合每个池的主数据库和辅助数据库。 采用这种方式可将服务中断的影响范围缩小到一半的租户中。

## <a name="upgrading-or-downgrading-a-primary-database"></a>升级或降级主数据库
无需断开连接任何辅助数据库，即可将主数据库升级或降级到不同的性能级别（在相同的服务层中）。 升级时，建议先升级辅助数据库，然后再升级主数据库。 降级时，应反转顺序：先降级主数据库，然后再降级辅助数据库。 将数据库升级或降级到不同服务层时，应强制执行此建议操作。 

> [!NOTE]
> 如果辅助数据库是作为故障转移组配置的一个部分创建的，则不建议对辅助数据库进行降级。 这是为了确保激活故障转移后，数据层有足够的容量来处理常规工作负荷。 
>

## <a name="preventing-the-loss-of-critical-data"></a>防止丢失关键数据
由于广域网的延迟时间较长，连续复制使用了异步复制机制。 在发生故障时，异步复制会不可避免地丢失某些数据。 但是，某些应用程序可能要求不能有数据丢失。 为了保护这些关键更新，应用程序开发人员可以在提交事务后立即调用 [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) 系统过程。 调用 **sp_wait_for_database_copy_sync** 会阻止调用线程，直到将上次提交的事务复制到辅助数据库。 该过程会一直等到辅助数据库确认所有排队的事务为止。 **sp_wait_for_database_copy_sync** 的作用域是一个具体的连续复制链路。 对主数据库具有连接权限的任何用户都可以调用此过程。

> [!NOTE]
> **sp_wait_for_database_copy_sync** 过程调用导致的延迟可能会很明显。 延迟取决于当前事务日志长度的大小，此调用在复制整个日志之前不会返回。 除非绝对必要，否则请避免调用此过程。
> 

## <a name="programmatically-managing-active-geo-replication"></a>以编程方式管理活动异地复制
如上所述，也可以使用 Azure PowerShell 和 REST API 以编程方式管理自动故障转移组和活动异地复制。 下表描述了可用的命令集。

* **Azure Resource Manager API 和基于角色的安全性**：活动异地复制包括一组用于管理的 [Azure Resource Manager API](https://msdn.microsoft.com/library/azure/mt163571.aspx)，其中包括[基于 Azure Resource Manager 的 PowerShell cmdlet](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)。 这些 API 需要使用资源组，并支持基于角色的安全性 (RBAC)。 有关如何实现访问角色的详细信息，请参阅 [Azure 基于角色的访问控制](../active-directory/role-based-access-control-configure.md)。

> [!NOTE]
> 只有使用基于 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 的 [Azure SQL REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx) 和 [Azure SQL 数据库 PowerShell cmdlet](https://msdn.microsoft.com/library/azure/mt574084.aspx) 才支持活动异地复制的许多新功能。 后向兼容性支持[（经典）REST API](https://msdn.microsoft.com/library/azure/dn505719.aspx) 和 [Azure SQL 数据库（经典）cmdlet](https://msdn.microsoft.com/library/azure/dn546723.aspx)，因此建议使用基于 Azure Resource Manager 的 API。 
> 
> 

### <a name="transact-sql"></a>Transact-SQL
| 命令 | 说明 |
| --- | --- |
| [ALTER DATABASE（Azure SQL 数据库）](https://msdn.microsoft.com/library/mt574871.aspx) |使用 ADD SECONDARY ON SERVER 参数为现有数据库创建辅助数据库，并开始数据复制 |
| [ALTER DATABASE（Azure SQL 数据库）](https://msdn.microsoft.com/library/mt574871.aspx) |使用 FAILOVER 或 FORCE_FAILOVER_ALLOW_DATA_LOSS 将辅助数据库切换为主数据库，启动故障转移 |
| [ALTER DATABASE（Azure SQL 数据库）](https://msdn.microsoft.com/library/mt574871.aspx) |使用 REMOVE SECONDARY ON SERVER 终止 SQL 数据库和指定的辅助数据库之间的数据复制。 |
| [sys.geo_replication_links（Azure SQL 数据库）](https://msdn.microsoft.com/library/mt575501.aspx) |返回有关 Azure SQL 数据库逻辑服务器上每个数据库的所有现有复制链路的信息。 |
| [sys.dm_geo_replication_link_status（Azure SQL 数据库）](https://msdn.microsoft.com/library/mt575504.aspx) |获取有关给定 SQL 数据库的复制链路的上次复制时间、上次复制滞后时间和其他信息。 |
| [sys.dm_operation_status（Azure SQL 数据库）](https://msdn.microsoft.com/library/dn270022.aspx) |显示所有数据库操作的状态，包括复制链路的状态。 |
| [sp_wait_for_database_copy_sync（Azure SQL 数据库）](https://msdn.microsoft.com/library/dn467644.aspx) |使应用程序等待，直到所有提交的事务已复制，并由活动辅助数据库确认。 |
|  | |

### <a name="powershell"></a>PowerShell
| Cmdlet | 说明 |
| --- | --- |
| [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabase?view=azurermps-3.7.0) |获取一个或多个数据库。 |
| [New-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabasesecondary?view=azurermps-3.7.0) |为现有数据库创建辅助数据库，并开始数据复制。 |
| [Set-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasesecondary?view=azurermps-3.7.0) |将辅助数据库切换为主数据库，启动故障转移。 |
| [Remove-AzureRmSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqldatabasesecondary?view=azurermps-3.7.0) |终止 SQL 数据库和指定的辅助数据库之间的数据复制。 |
| [Get-AzureRmSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabasereplicationlink?view=azurermps-3.7.0) |获取 Azure SQL 数据库和资源组或 SQL Server 之间的异地复制链路。 |
| [New-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup?view=azurermps-3.7.0) |    此命令会创建新的故障转移组，并将其同时注册到主服务器和辅助服务器|
| [Remove-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqldatabasefailovergroup?view=azurermps-3.7.0) | 从服务器中移除故障转移组，并删除组中包含的所有辅助数据库 |
| [Get-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabasefailovergroup?view=azurermps-3.7.0) | 检索故障转移组配置 |
| [Set-AzureRmSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabasefailovergroup?view=azurermps-3.7.0) |    修改故障转移组的配置 |
| [Switch-AzureRMSqlDatabaseFailoverGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/switch-azurermsqldatabasefailovergroup?view=azurermps-3.7.0) | 触发故障转移组到辅助服务器的故障转移 |
|  | |

### <a name="rest-api"></a>REST API
| API | 说明 |
| --- | --- |
| [创建或更新数据库 (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |创建、更新或还原主数据库或辅助数据库。 |
| [获取创建或更新数据库状态](https://msdn.microsoft.com/library/azure/mt643934.aspx) |返回创建操作过程中的状态。 |
| [将辅助数据库设为主数据库（计划的故障转移）](https://msdn.microsoft.com/library/azure/mt575007.aspx) |提升异地复制合作关系中的辅助数据库，使其成为新的主数据库。 |
| [将辅助数据库设为主数据库（未计划的故障转移）](https://msdn.microsoft.com/library/azure/mt582027.aspx) |强制故障转移到辅助数据库，并将辅助数据库设为主数据库。 |
| [获取复制链路](https://msdn.microsoft.com/library/azure/mt600929.aspx) |获取异地复制合作关系中给定 SQL 数据库的所有复制链路。 它检索 sys.geo_replication_links 目录视图中可见的信息。 |
| [获取复制链路](https://msdn.microsoft.com/library/azure/mt600778.aspx) |获取异地复制合作关系中给定 SQL 数据库的特定复制链路。 它检索 sys.geo_replication_links 目录视图中可见的信息。 |
|  | |

## <a name="next-steps"></a>后续步骤
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)。
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)。
* 若要了解如何使用自动备份进行存档，请参阅[数据库复制](sql-database-copy.md)。
* 若要了解新主服务器和数据库的身份验证要求，请参阅[灾难恢复后的 SQL 数据库安全性](sql-database-geo-replication-security-config.md)。


