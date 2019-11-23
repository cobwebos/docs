---
title: 故障转移组
description: 自动故障转移组是一项 SQL 数据库功能，可便于管理 SQL 数据库服务器中一组数据库或托管实例中所有数据库的复制和自动/协调式故障转移。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 11/07/2019
ms.openlocfilehash: 470e9a9c36b6b4ec2e40db5dfc47ae03fb6b5aa8
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421372"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>使用自动故障转移组可以实现多个数据库的透明、协调式故障转移

Auto-failover groups is a SQL Database feature that allows you to manage replication and failover of a group of databases on a SQL Database server or all databases in a managed instance to another region. It is a declarative abstraction on top of the existing [active geo-replication](sql-database-active-geo-replication.md) feature, designed to simplify deployment and management of geo-replicated databases at scale. 可以手动启动故障转移，也可以基于用户定义的策略委托 SQL 数据库服务进行故障转移。 使用后一种做法可在发生下述情况后自动恢复次要区域中的多个相关数据库：灾难性故障或其他导致主要区域中 SQL 数据库服务完全或部分丧失可用性的计划外事件。 A failover group can include one or multiple databases, typically used by the same application. 此外，你还可以使用可读辅助数据库卸载只读查询工作负荷。 由于自动故障转移组涉及多个数据库，因此这些数据库必须在主服务器上进行配置。 自动故障转移组支持将组中所有的数据库复制到另一个区域中唯一的辅助服务器。

> [!NOTE]
> 如果在 SQL 数据库服务器上使用单一数据库或共用数据库，并要在相同或不同的区域中使用多个辅助数据库，请使用[活动异地复制](sql-database-active-geo-replication.md)。 

将自动故障转移组与自动故障转移策略配合使用时，任何影响组中一个或多个数据库的服务中断都会导致自动故障转移。 Typically these are incidents that cannot be self-mitigated by the built-in automatic high availability operations. The examples of failover triggers include an incident caused by a SQL tenant ring or control ring being down due to an OS kernel memory leak on several compute nodes, or an incident caused by one or more tenant rings being down because a wrong network cable was cut during routine hardware decommissioning.  For more information, see [SQL Database High Availability](sql-database-high-availability.md).

此外，自动故障转移组提供在故障转移期间保持不变的读写和只读侦听器终结点。 无论使用手动故障转移激活还是自动故障转移激活，故障转移都会将组中所有的辅助数据库切换到主数据库。 数据库故障转移完成后，会自动更新 DNS 记录，以便将终结点重定向到新的区域。 有关具体的 RPO 和 RTO 数据，请参阅[业务连续性概述](sql-database-business-continuity.md)。

将自动故障转移组与自动故障转移策略配合使用时，任何影响 SQL 数据库服务器或托管实例中数据库的服务中断都会导致自动故障转移。 可使用以下方式管理自动故障转移组：

- [Azure 门户](sql-database-implement-geo-distributed-database.md)
- [PowerShell：故障转移组](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [REST API：故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups)。

故障转移后，请确保在新的主机上配置服务器和数据库的身份验证要求。 有关详细信息，请参阅[灾难恢复后的 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。

若要真正实现业务连续性，只需添加数据中心之间的数据库冗余即可，这只是该解决方案的一部分功能。 在发生灾难性故障后，端对端地恢复应用程序（服务）需要恢复构成该服务的所有组件以及所有依赖服务。 这些组件的示例包括客户端软件（例如，使用自定义 JavaScript 的浏览器）、Web 前端、存储和 DNS。 所有组件必须能够弹性应对相同的故障，并在应用程序的恢复时间目标 (RTO) 值内变为可用，这一点非常关键。 因此，需要识别所有依赖服务，并了解它们提供的保证和功能。 然后，必须执行适当的步骤来确保对服务所依赖的服务执行故障转移期间，服务能够正常运行。 有关设计灾难恢复解决方案的详细信息，请参阅[设计使用活动异地复制的灾难恢复云解决方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="auto-failover-group-terminology-and-capabilities"></a>自动故障转移组的术语和功能

- **Failover group (FOG)**

  A failover group is a named group of databases managed by a single SQL Database server or within a single managed instance that can fail over as a unit to another region in case all or some primary databases become unavailable due to an outage in the primary region. When created for managed instances, a failover group contains all user databases in the instance and therefore only one failover group can be configured on an instance.
  
  > [!IMPORTANT]
  > The name of the failover group must be globally unique within the `.database.windows.net` domain.

- **SQL 数据库服务器**

     使用 SQL 数据库服务器，可以将一个 SQL 数据库服务器上的部分或所有用户数据库放入故障转移组。 此外，SQL 数据库服务器支持一个 SQL 数据库服务器上有多个故障转移组。

- **主要节点**

  The SQL Database server or managed instance that hosts the primary databases in the failover group.

- **辅助节点**

  The SQL Database server or managed instance that hosts the secondary databases in the failover group. 辅助节点不能与主要节点位于相同的区域。

- **将单一数据库添加到故障转移组**

  可以将同一 SQL 数据库服务器上的多个单一数据库放入同一故障转移组。 如果将单一数据库添加到故障转移组，则它会在辅助服务器上自动使用相同的版本和计算大小创建辅助数据库。  创建故障转移组时指定该服务器。 如果在辅助服务器中添加已具有辅助数据库的数据库，则该异地复制链接由组继承。 在不属于故障转移组的服务器中添加已有辅助数据库的数据库时，会在辅助服务器中创建新的辅助节点。

  > [!IMPORTANT]
  > Make sure that the secondary server doesn't have a database with the same name unless it is an existing secondary database. In failover groups for managed instance all user databases are replicated. 无法选择复制故障转移组中的一部分用户数据库。

- **将弹性池中的数据库添加到故障转移组**

  可将一个弹性池内的所有或多个数据库放入同一故障转移组。 如果主数据库在弹性池中，将在具有相同名称的弹性池（辅助池）中自动创建辅助数据库。 必须确保辅助服务器包含名称完全相同的弹性池，并有足够的可用容量来托管将由故障转移组创建的辅助数据库。 如果在辅助池中已有辅助数据库的池中添加数据库，则该异地复制链接由组继承。 在不属于故障转移组的服务器中添加已有辅助数据库的数据库时，会在辅助池中创建新的辅助数据库。
  
- **DNS 区域**

  A unique ID that is automatically generated when a new instance is created. A multi-domain (SAN) certificate for this instance is provisioned to authenticate the client connections to any instance in the same DNS zone. The two managed instances in the same failover group must share the DNS zone.
  
  > [!NOTE]
  > A DNS zone ID is not required for failover groups created for SQL Database servers.

- **故障转移组读写侦听器**

  A DNS CNAME record that points to the current primary's URL. It is created automatically when the failover group is created and allows the read-write SQL workload to transparently reconnect to the primary database when the primary changes after failover. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.database.windows.net`.

- **故障转移组只读侦听器**

  构成的 DNS CNAME 记录，指向只读侦听器，后者指向辅助节点的 URL。 It is created automatically when the failover group is created and allows the read-only SQL workload to transparently connect to the secondary using the specified load-balancing rules. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.secondary.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.secondary.database.windows.net`.

- **自动故障转移策略**

  默认使用自动故障转移策略配置故障转移组。 检测到故障且宽限期过后，SQL 数据库服务会触发故障转移。 系统必须确保，因影响范围太大，[SQL 数据库服务的内置高可用性基础结构](sql-database-high-availability.md)无法缓解服务中断。 如果要从应用程序控制故障转移工作流，可以关闭自动故障转移。
  
  > [!NOTE]
  > Because verification of the scale of the outage and how quickly it can be mitigated involves human actions by the operations team, the grace period cannot be set below one hour.  This  limitation applies to all databases in the failover group regardless of their data synchronization state. 

- **只读故障转移策略**

  默认禁用只读侦听器的故障转移功能。 这可确保在辅助数据库脱机时，主数据库的性能不会受到影响。 但是，这也意味辅助数据库恢复前，只读会话将无法连接。 If you cannot tolerate downtime for the read-only sessions and are OK to temporarily use the primary for both read-only and read-write traffic at the expense of the potential performance degradation of the primary, you can enable failover for the read-only listener by configuring the `AllowReadOnlyFailoverToPrimary` property. In that case, the read-only traffic will be automatically redirected to the primary if the secondary is not available.

- **计划内故障转移**

   将辅助角色切换为主要角色之前，计划内故障转移在主要数据库与辅助数据库之间执行完全同步。 这可以保证数据不会丢失。 计划内故障转移用于以下场景：

  - 不可接受数据丢失时在生产环境中执行灾难恢复 (DR) 演练
  - 将数据库重新定位到不同的区域
  - 缓解服务中断（故障回复）后将数据库恢复到主要区域。

- **计划外故障转移**

   计划外故障转移或强制故障转移立即将辅助角色切换为主要角色，而不与主要节点进行任何同步。 此操作会导致数据丢失。 在服务中断期间当主要节点不可访问时，计划外故障转移将用作恢复方法。 When the original primary is back online, it will automatically reconnect without synchronization and become a new secondary.

- **手动故障转移**

  可随时手动启动故障转移，而不考虑自动故障转移配置。 如果未配置自动故障转移策略，则需要执行手动故障转移才能将故障转移组中的数据库恢复到辅助节点。 可通过完整数据同步启动强制或友好的故障转移。 后者可用于将主要节点重新定位到次要区域。 故障转移完成后，会自动更新 DNS 记录，以确保与新的主要节点建立连接

- **数据丢失宽限期**

  由于主数据库和辅助数据库是使用异步复制进行同步的，因此故障转移可能会导致数据丢失。 可以自定义自动故障转移策略，以便反映应用程序对数据丢失的容错。 By configuring `GracePeriodWithDataLossHours`, you can control how long the system waits before initiating the failover that is likely to result data loss.

- **多个故障转移组**

  可为同一对服务器配置多个故障转移组以控制故障转移规模。 每个组均独立进行故障转移。 如果多租户应用程序使用弹性池，则可使用此功能来混合每个池的主数据库和辅助数据库。 采用这种方式可将服务中断的影响范围缩小到一半的租户中。

  > [!NOTE]
  > 托管实例不支持多个故障转移组。
  
## <a name="permissions"></a>权限

Permissions for a failover group are managed via [role-based access control (RBAC)](../role-based-access-control/overview.md). The [SQL Server Contributor](../role-based-access-control/built-in-roles.md#sql-server-contributor) role has all the necessary permissions to manage failover groups.

### <a name="create-failover-group"></a>Create failover group

To create a failover group, you need RBAC write access to both the primary and secondary servers, and to all databases in the failover group. For a managed instance, you need RBAC write access to both the primary and secondary managed instance, but permissions on individual databases are not relevant since individual managed instance databases cannot be added to or removed from a failover group. 

### <a name="update-a-failover-group"></a>Update a failover group

To update a failover group, you need RBAC write access to the failover group, and all databases on the current primary server or managed instance.  

### <a name="failover-a-failover-group"></a>Failover a failover group

To fail over a failover group, you need RBAC write access to the failover group on the new primary server or managed instance.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>有关将故障转移组与单一数据库和弹性池配合使用的最佳做法

自动故障转移组必须在主要 SQL 数据库服务器上进行配置，并会将它连接到不同 Azure 区域中的辅助 SQL 数据库服务器。 组可以包含这些服务器中的所有或部分数据库。 下图演示了使用多个数据库和自动故障转移组的异地冗余云应用程序的典型配置。

![自动故障转移](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> See [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a single database to a failover group.

在设计具有业务连续性的服务时，请遵循以下一般准则：

- **使用一个或多个故障转移组来管理多个数据库的故障转移**

  可在不同区域的两个服务器（主服务器和辅助服务器）之间创建一个或多个故障转移组。 每组可包含一个或多个数据库，这些数据库是在所有或某些主数据库因主要区域中的服务中断而变得不可用时，作为单元恢复的。 故障转移组使用服务目标作为主数据库创建异地辅助数据库。 如果将现有的异地复制关系添加到故障转移组，请确保使用与主数据库相同的服务层级和计算大小来配置异地辅助数据库。
  
  > [!IMPORTANT]
  > Creating failover groups between two servers in different subscriptions is not currently supported for single databases and elastic pools. If you move the primary or secondary server to a different subscription after the failover group has been created, it could result in failures of the failover requests and other operations.

- **使用读写侦听器处理 OLTP 工作负荷**

  执行 OLTP 操作时，请使用 `<fog-name>.database.windows.net` 作为服务器 URL，连接将自动定向到主要节点。 此 URL 在故障转移后不会更改。 请注意，故障转移涉及更新 DNS 记录，以便仅在刷新客户端 DNS 缓存后，客户端连接才会重定向到新的主数据库。

- **使用只读侦听器处理只读工作负荷**

  如果你有一个在逻辑上隔离的只读工作负荷，且它允许存在一些过时数据，则可在应用程序中使用辅助数据库。 对于只读的会话，请使用 `<fog-name>.secondary.database.windows.net` 作为服务器 URL，连接将自动定向到辅助节点。 It is also recommended that you indicate in connection string read intent by using `ApplicationIntent=ReadOnly`. If you want to ensure that the read-only workload can reconnect after failover or in case the secondary server goes offline, make sure to configure the `AllowReadOnlyFailoverToPrimary` property of the failover policy.

- **可应对性能下降的问题**

  SQL 故障转移决策与应用程序的其余部分或所用的其他服务无关。 应用程序可能是“mixed”与在一个区域和一些在另一些组件。 为避免性能降低，请确保在 DR 区域中采用冗余的应用程序部署，并遵循这些[网络安全性准则](#failover-groups-and-network-security)。

  > [!NOTE]
  > DR 区域中的应用程序不必使用不同的连接字符串。  

- **可应对数据丢失的问题**

  If an outage is detected, SQL waits for the period you specified by `GracePeriodWithDataLossHours`. 默认值为 1 小时。 If you cannot afford data loss, make sure to set `GracePeriodWithDataLossHours` to a sufficiently large number, such as 24 hours. 使用手动组故障转移从辅助节点故障回复到主要节点。

  > [!IMPORTANT]
  > DTU 少于或等于 800、使用异地复制的数据库超过 250 个的弹性数据库池可能会遇到更长的计划故障转移和性能下降等问题。  这些问题更可能在写密集型工作负荷下发生，例如，异地复制终结点广泛分隔于各个地理位置，或者每个数据库使用多个辅助终结点。  当异地复制滞后随着时间推移增加时，这些问题的症状便会显现。  这种滞后可以使用 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) 进行监视。  如果发生这些问题，缓解方法包括增加池 DTU 的数量或者减少同一池中异地复制数据库的数量。

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Best practices of using failover groups with managed instances

自动故障转移组必须在主要实例上进行配置，需将其连接到不同 Azure 区域中的辅助实例。  实例中的所有数据库将复制到辅助实例。

下图演示了使用托管实例和自动故障转移组的异地冗余云应用程序的典型配置。

![自动故障转移](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> See [Add managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a managed instance to use failover group.

If your application uses managed instance as the data tier, follow these general guidelines when designing for business continuity:

- **在主要实例所在的同一 DNS 区域中创建辅助实例**

  若要确保故障转移后与主要实例的连接不中断，主要实例和辅助实例必须位于同一 DNS 区域。 It will guarantee that the same multi-domain (SAN) certificate can be used to authenticate the client connections to either of the two instances in the failover group. 准备好将应用程序部署到生产环境后，在不同的区域中创建一个辅助实例，并确保它与主要实例共享 DNS 区域。 You can do it by specifying a `DNS Zone Partner` optional parameter using the Azure portal, PowerShell, or the REST API.

> [!IMPORTANT]
> First instance created in the subnet determines DNS zone for all subsequent instances in the same subnet. This means that two instances from the same subnet cannot belong to different DNS zones.

  For more information about creating the secondary instance in the same DNS zone as the primary instance, see [Create a secondary managed instance](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

- **在两个实例之间启用复制流量**

  由于每个实例隔离在其自身的 VNet 中，因此，必须允许这些 VNet 之间的双向流量。 请参阅 [Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Create a failover group between managed instances in different subscriptions**

  You can create a failover group between managed instances in two different subscriptions. When using PowerShell API you can do it by  specifying the `PartnerSubscriptionId` parameter for the secondary instance. When using REST API, each instance ID included in the `properties.managedInstancePairs` parameter can have its own subscriptionID.
  
  > [!IMPORTANT]
  > Azure Portal does not support failover groups across different subscriptions.

- **将故障转移组配置为管理整个实例的故障转移**

  故障转移组将管理实例中所有数据库的故障转移。 创建某个组后，实例中的每个数据库将自动异地复制到辅助实例。 无法使用故障转移组针对一部分数据库启动部分故障转移。

  > [!IMPORTANT]
  > 如果从主要实例中删除了某个数据库，该数据库也会在异地辅助实例上自动删除。

- **使用读写侦听器处理 OLTP 工作负荷**

  执行 OLTP 操作时，请使用 `<fog-name>.zone_id.database.windows.net` 作为服务器 URL，连接将自动定向到主要节点。 此 URL 在故障转移后不会更改。 故障转移涉及更新 DNS 记录，以便仅在刷新客户端 DNS 缓存后，客户端连接才会重定向到新的主要节点。 Because the secondary instance shares the DNS zone with the primary, the client application will be able to reconnect to it using the same SAN certificate.

- **直接连接到异地复制的辅助节点以进行只读查询**

  如果你有一个在逻辑上隔离的只读工作负荷，且它允许存在一些过时数据，则可在应用程序中使用辅助数据库。 若要直接连接到异地复制的辅助节点，请使用 `server.secondary.zone_id.database.windows.net` 作为服务器 URL，这样可以直接连接到异地复制的辅助节点。

  > [!NOTE]
  > 在某些服务层级中，Azure SQL 数据库支持通过[只读副本](sql-database-read-scale-out.md)使用只读副本的容量和连接字符串中的 `ApplicationIntent=ReadOnly` 参数对只读查询工作负荷进行负载均衡。 如果配置了异地复制的辅助节点，则可以使用此功能连接到主要位置或异地复制位置中的只读副本。
  > - 若要连接到主要位置中的只读副本，请使用 `<fog-name>.zone_id.database.windows.net`。
  > - To connect to a read-only replica in the secondary location, use `<fog-name>.secondary.zone_id.database.windows.net`.

- **可应对性能下降的问题**

  SQL 故障转移决策与应用程序的其余部分或所用的其他服务无关。 应用程序可能是“mixed”与在一个区域和一些在另一些组件。 为避免性能降低，请确保在 DR 区域中采用冗余的应用程序部署，并遵循这些[网络安全性准则](#failover-groups-and-network-security)。

- **可应对数据丢失的问题**

  如果检测到服务中断，则 SQL 会自动触发读写故障转移是否我们的知识的最佳零数据丢失。 否则，它会等待 `GracePeriodWithDataLossHours` 指定的期限。 如果指定了 `GracePeriodWithDataLossHours`，则可能会丢失数据。 一般情况下，在中断期间 Azure 倾向于可用性。 如果不能承受丢失数据，请务必将 GracePeriodWithDataLossHours 设置为一个足够大的数字，例如 24 小时。

  启动故障转移后，读写侦听器的 DNS 更新会立即发生。 此操作不会导致数据丢失。 但是，在正常情况下，切换数据库角色的过程可能需要 5 分钟时间。 在完成之前，新主要实例中的某些数据库仍是只读的。 If failover is initiated using PowerShell, the entire operation is synchronous. If it is initiated using the Azure portal, the UI will indicate completion status. 如果使用 REST API 启动故障转移，可以使用标准 Azure 资源管理器的轮询机制来监视完成状态。

  > [!IMPORTANT]
  > 使用手动组故障转移可将主要数据库移回到原始位置。 缓解导致故障转移的服务中断问题后，可将主要数据库移到原始位置。 为此，应该启动组的手动故障转移。

- **Acknowledge known limitations of failover groups**

  Database rename is not supported for instances in failover group. You will need to temporarily delete failover group to be able to rename a database.

## <a name="failover-groups-and-network-security"></a>故障转移组和网络安全

For some applications the security rules require that the network access to the data tier is restricted to a specific component or components such as a VM, web service etc. This requirement presents some challenges for business continuity design and the use of the failover groups. Consider the following options when implementing such restricted access.

### <a name="using-failover-groups-and-virtual-network-rules"></a>使用故障转移组和虚拟网络规则

如果使用[虚拟网络服务终结点和规则](sql-database-vnet-service-endpoint-rule-overview.md)来限制对 SQL 数据库的访问，请注意每个虚拟网络服务终结点仅适用于一个 Azure 区域。 终结点不允许其他区域接受来自该子网的通信。 因此，只有部署在同一区域中的客户端应用程序才能连接到主数据库。 因为故障转移会导致 SQL 客户端会话重新路由到不同（次要）区域中的服务器，所以源自该区域之外的客户端的这些会话将失败。 因此，如果参与的服务器包含在虚拟网络规则中，则无法启用自动故障转移策略。 若要支持手动故障转移，请执行以下步骤：

1. 在次要区域中预配应用程序前端组件（Web 服务、虚拟机等）的冗余副本
2. 为主服务器和辅助服务器分别配置[虚拟网络规则](sql-database-vnet-service-endpoint-rule-overview.md)
3. 使用流量管理器配置启用[前端故障转移](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. 检测到服务中断时启动手动故障转移。 此选项针对需要在前端和数据层之间保持一致延迟的应用程序进行了优化，并支持在前端和/或数据层受到服务中断的影响时进行恢复。

> [!NOTE]
> 如果使用**只读侦听器**对只读工作负荷进行负载均衡，请确保在次要区域中的 VM 或其他资源上执行此工作负荷，以便它可以连接到辅助数据库。

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>使用故障转移组和 SQL 数据库防火墙规则

如果业务连续性计划要求使用自动故障转移组进行故障转移，则可以使用传统防火墙规则限制对 SQL 数据库的访问。 若要支持自动故障转移，请执行以下步骤：

1. [创建公共 IP](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [创建公共负载均衡器](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer)并为其分配公共 IP。
3. 为前端组件[创建虚拟网络和虚拟机](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers)
4. [创建网络安全组](../virtual-network/security-overview.md)并配置入站连接。
5. 使用“Sql”[服务标记](../virtual-network/security-overview.md#service-tags)确保出站连接向 Azure SQL 数据库开放。
6. 创建 [SQL 数据库防火墙规则](sql-database-firewall-configure.md)，以允许来自步骤 1 中创建的公共 IP 地址的入站流量。

有关如何配置出站访问以及在防火墙规则中使用哪个 IP 的详细信息，请参阅[负载均衡器出站连接](../load-balancer/load-balancer-outbound-connections.md)。

上述配置将确保自动故障转移不会阻止来自前端组件的连接，并假定应用程序可以容忍前端与数据层之间的较长延迟。

> [!IMPORTANT]
> 若要保证区域服务中断的业务连续性，则必须确保前端组件和数据库的地理冗余。

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Enabling geo-replication between managed instances and their VNets

When you set up a failover group between primary and secondary managed instances in two different regions, each instance is isolated using an independent virtual network. To allow replication traffic between these VNets ensure these prerequisites are met:

1. The two managed instances need to be in different Azure regions.
1. The two managed instances need to be the same service tier, and have the same storage size.
1. Your secondary managed instance must be empty (no user databases).
1. The virtual networks used by the managed instances need to be connected through a [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) or Express Route. When two virtual networks connect through an on-premises network, ensure there is no firewall rule blocking ports 5022, and 11000-11999. 不支持全局 VNet 对等互连。
1. The two managed instance VNets cannot have overlapping IP addresses.
1. You need to set up your Network Security Groups (NSG) such that ports 5022 and the range 11000~12000 are open inbound and outbound for connections from the other managed instanced subnet. 目的是允许实例之间的复制流量

   > [!IMPORTANT]
   > NSG 安全规则配置不当会导致数据库复制操作停滞。

1. The secondary instance is configured with the correct DNS zone ID. DNS zone is a property of a managed instance and virtual cluster, and its ID is included in the host name address. The zone ID is generated as a random string when the first managed instance is created in each VNet and the same ID is assigned to all other instances in the same subnet. Once assigned, the DNS zone cannot be modified. Managed instances included in the same failover group must share the DNS zone. You accomplish this by passing the primary instance's zone ID as the value of DnsZonePartner parameter when creating the secondary instance. 

   > [!NOTE]
   > For a detailed tutorial on configuring failover groups with managed instance, see [add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>升级或降级主数据库

无需断开连接任何辅助数据库，即可将主数据库升级或降级到不同的计算大小（在相同的服务层级中，但不在“常规用途”与“业务关键”类型之间）。 When upgrading, we recommend that you upgrade all of the secondary databases first, and then upgrade the primary. When downgrading, reverse the order: downgrade the primary first, and then downgrade all of the secondary databases. 将数据库升级或降级到不同服务层级时，将强制执行此建议操作。

This sequence is recommended specifically to avoid the problem where the secondary at a lower SKU gets overloaded and must be re-seeded during an upgrade or downgrade process. You could also avoid the problem by making the primary read-only, at the expense of impacting all read-write workloads against the primary.

> [!NOTE]
> 如果辅助数据库是作为故障转移组配置的一个部分创建的，则不建议对辅助数据库进行降级。 这是为了确保激活故障转移后，数据层有足够的容量来处理常规工作负荷。

## <a name="preventing-the-loss-of-critical-data"></a>防止丢失关键数据

由于广域网的延迟时间较长，连续复制使用了异步复制机制。 在发生故障时，异步复制会不可避免地丢失某些数据。 但是，某些应用程序可能要求不能有数据丢失。 为了保护这些关键更新，应用程序开发人员可以在提交事务后立即调用 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 系统过程。 Calling `sp_wait_for_database_copy_sync` blocks the calling thread until the last committed transaction has been transmitted to the secondary database. 但是，它不会等待传输的事务提交到辅助数据库进行重播。 `sp_wait_for_database_copy_sync` is scoped to a specific continuous copy link. 对主数据库具有连接权限的任何用户都可以调用此过程。

> [!NOTE]
> `sp_wait_for_database_copy_sync` prevents data loss after failover, but does not guarantee full synchronization for read access. The delay caused by a `sp_wait_for_database_copy_sync` procedure call can be significant and depends on the size of the transaction log at the time of the call.

## <a name="failover-groups-and-point-in-time-restore"></a>故障转移组和时间点还原

有关将时间点还原与故障转移组配合使用的信息，请参阅[时间点恢复 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="programmatically-managing-failover-groups"></a>以编程方式管理故障转移组

如上所述，也可以使用 Azure PowerShell 和 REST API 以编程方式管理自动故障转移组和活动异地复制。 下表描述了可用的命令集。 活动异地复制包括一组用于管理的 Azure 资源管理器 API，其中包括 [Azure SQL 数据库 REST API](https://docs.microsoft.com/rest/api/sql/) 和 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)。 这些 API 需要使用资源组，并支持基于角色的安全性 (RBAC)。 有关如何实现访问角色的详细信息，请参阅 [Azure 基于角色的访问控制](../role-based-access-control/overview.md)。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>使用单一数据库和弹性池管理 SQL 数据库故障转移

| Cmdlet | 描述 |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |此命令会创建故障转移组，并将其同时注册到主服务器和辅助服务器|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | 从服务器中移除故障转移组，并删除组中包含的所有辅助数据库 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 检索故障转移组配置 |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |修改故障转移组的配置 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | 触发故障转移组到辅助服务器的故障转移 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|将一个或多个数据库添加到 Azure SQL 数据库故障转移组|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| Cmdlet | 描述 |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |此命令会创建故障转移组，并将其同时注册到主服务器和辅助服务器|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |修改故障转移组的配置|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |检索故障转移组配置|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |触发故障转移组到辅助服务器的故障转移|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | 删除故障转移组|

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>使用单一数据库和弹性池管理 SQL 数据库故障转移

| 命令 | 描述 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |此命令会创建故障转移组，并将其同时注册到主服务器和辅助服务器|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | 从服务器中移除故障转移组，并删除组中包含的所有辅助数据库 |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | 检索故障转移组配置 |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modifies the configuration of the failover group and/or adds one or more databases to an Azure SQL Database failover group|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 触发故障转移组到辅助服务器的故障转移 |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| 命令 | 描述 |
| --- | --- |
| [az sql instance-failover-group create](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | 此命令会创建故障转移组，并将其同时注册到主服务器和辅助服务器|
| [az sql instance-failover-group update](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | 修改故障转移组的配置|
| [az sql instance-failover-group show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | 检索故障转移组配置|
| [az sql instance-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | 触发故障转移组到辅助服务器的故障转移|
| [az sql instance-failover-group delete](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | 删除故障转移组 |

* * *

> [!IMPORTANT]
> 有关示例脚本，请参阅[为单一数据库配置并故障转移一个故障转移组](scripts/sql-database-add-single-db-to-failover-group-powershell.md)

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Manage SQL database failover groups with single and pooled databases

| API | 描述 |
| --- | --- |
| [创建或更新故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | 创建或更新故障转移组 |
| [删除故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | 从服务器中删除故障转移组 |
| [故障转移（计划内）](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | 把故障从当前的主服务器转移到该服务器。 |
| [强制故障转移可能导致数据丢失](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |把故障从当前的主服务器转移到该服务器。 此操作可能导致数据丢失。 |
| [获取故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | 获取故障转移组。 |
| [服务器的故障转移组列表](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | 列出服务器中的故障转移组。 |
| [更新故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | 更新故障转移组。 |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: Manage failover groups with Managed Instances

| API | 描述 |
| --- | --- |
| [创建或更新故障转移组](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | 创建或更新故障转移组 |
| [删除故障转移组](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | 从服务器中删除故障转移组 |
| [故障转移（计划内）](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | 把故障从当前的主服务器转移到该服务器。 |
| [强制故障转移可能导致数据丢失](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |把故障从当前的主服务器转移到该服务器。 此操作可能导致数据丢失。 |
| [获取故障转移组](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | 获取故障转移组。 |
| [列出故障转移组 - 按位置列出](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | 列出某个位置中的故障转移组。 |

## <a name="next-steps"></a>后续步骤

- For detailed tutorials, see
    - [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md)
    - [Add elastic pool to a failover group](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md)
- 示例脚本请参阅：
  - [Use PowerShell to configure active geo-replication for a single database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Use PowerShell to configure active geo-replication for a pooled database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Use PowerShell to add an Azure SQL Database single database to a failover group](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
- 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)。
- 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)。
- 若要了解新主服务器和数据库的身份验证要求，请参阅[灾难恢复后的 SQL 数据库安全性](sql-database-geo-replication-security-config.md)。
