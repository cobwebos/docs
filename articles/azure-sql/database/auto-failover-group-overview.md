---
title: 自动故障转移组
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 自动故障转移组可用于管理服务器中一组数据库或托管实例中所有数据库的复制和自动/协调式故障转移。
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 08/28/2020
ms.openlocfilehash: 2035fa811ed6bb5760f2527f66e0f2ca48ccb2c9
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91627219"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>使用自动故障转移组可以实现多个数据库的透明、协调式故障转移
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

通过自动故障转移组功能，可以管理服务器中一组数据库或托管实例中所有数据库到另一区域的复制和故障转移。 它是建立在现有[活动异地复制](active-geo-replication-overview.md)功能基础之上的声明性抽象，旨在简化异地复制的数据库的大规模部署和管理。 可以手动启动故障转移，也可以基于用户定义的策略委托 Azure 服务进行故障转移。 使用后一种做法可在发生下述情况后自动恢复次要区域中的多个相关数据库：灾难性故障或其他导致主要区域中 SQL 数据库或 SQL 托管实例完全或部分丧失可用性的计划外事件。 一个故障转移组可以包含一个或多个数据库，通常由同一个应用程序使用。 此外，你还可以使用可读辅助数据库卸载只读查询工作负荷。 由于自动故障转移组涉及多个数据库，因此这些数据库必须在主服务器上进行配置。 自动故障转移组支持将组中所有的数据库复制到另一个区域中唯一的辅助服务器或实例。

> [!NOTE]
> 如果希望多个 Azure SQL 数据库辅助数据库在相同或不同的区域中，请使用[活动异地复制](active-geo-replication-overview.md)。

将自动故障转移组与自动故障转移策略配合使用时，任何影响组中一个或多个数据库的服务中断都会导致自动故障转移。 通常，通过内置的自动高可用性操作无法自行缓解这些事件。 故障转移触发器的示例包括：SQL 数据库租户环或控制环由于多个计算节点上的 OS 内核内存泄漏而导致的事件，或者由于在日常硬件解除期间断开错误的网线而关闭一个或多个租户环，因此导致的事件。  有关详细信息，请参阅 [SQL 数据库高可用性](high-availability-sla.md)。

此外，自动故障转移组提供在故障转移期间保持不变的读写和只读侦听器终结点。 无论使用手动故障转移激活还是自动故障转移激活，故障转移都会将组中所有的辅助数据库切换到主数据库。 数据库故障转移完成后，会自动更新 DNS 记录，以便将终结点重定向到新的区域。 有关具体的 RPO 和 RTO 数据，请参阅[业务连续性概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)。

将自动故障转移组与自动故障转移策略配合使用时，任何影响服务器或托管实例中数据库的服务中断都会导致自动故障转移。 可使用以下方式管理自动故障转移组：

- [Azure 门户](geo-distributed-application-configure-tutorial.md)
- [Azure CLI：故障转移组](scripts/add-database-to-failover-group-cli.md)
- [PowerShell：故障转移组](scripts/add-database-to-failover-group-powershell.md)
- [REST API：故障转移组](/rest/api/sql/failovergroups)。

故障转移后，请确保在新的主数据库上配置数据库和服务器或实例的身份验证要求。 有关详细信息，请参阅 [SQL Database security after disaster recovery](active-geo-replication-security-configure.md)（灾难恢复后的 Azure SQL 数据库安全性）。

若要真正实现业务连续性，只需添加数据中心之间的数据库冗余即可，这只是该解决方案的一部分功能。 在发生灾难性故障后，端对端地恢复应用程序（服务）需要恢复构成该服务的所有组件以及所有依赖服务。 这些组件的示例包括客户端软件（例如，使用自定义 JavaScript 的浏览器）、Web 前端、存储和 DNS。 所有组件必须能够弹性应对相同的故障，并在应用程序的恢复时间目标 (RTO) 值内变为可用，这一点非常关键。 因此，需要识别所有依赖服务，并了解它们提供的保证和功能。 然后，必须执行适当的步骤来确保对用户的服务所依赖的服务执行故障转移期间，用户的服务能够正常运行。 有关设计灾难恢复解决方案的详细信息，请参阅[设计使用活动异地复制的灾难恢复云解决方案](designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="terminology-and-capabilities"></a>术语和功能

- **故障转移组 (FOG)**

  故障转移组是由一个服务器管理或位于一个托管实例中的一组指定数据库，当主要区域的服务中断导致所有或部分主要数据库不可用时，这组数据库可作为单元故障转移到另一区域。 为 SQL 托管实例创建故障转移组后，该组将包含该实例中的所有用户数据库，因此，只能在一个实例上配置一个故障转移组。
  
  > [!IMPORTANT]
  > 故障转移组的名称在 `.database.windows.net` 域中必须全局唯一。

- **服务器**

     使用服务器可将服务器上的部分或所有用户数据库放入故障转移组。 此外，服务器支持单个服务器上的多个故障转移组。

- **主要节点**

  托管故障转移组中的主数据库的服务器或托管实例。

- **辅助节点**

  托管故障转移组中的辅助数据库的服务器或托管实例。 辅助节点不能与主要节点位于相同的区域。

- **将单一数据库添加到故障转移组**

  可以将同一服务器上的多个单一数据库放入同一故障转移组。 如果将单一数据库添加到故障转移组，则它会在辅助服务器上自动使用相同的版本和计算大小创建辅助数据库。  创建故障转移组时指定该服务器。 如果在辅助服务器中添加已具有辅助数据库的数据库，则该异地复制链接由组继承。 在不属于故障转移组的服务器中添加已有辅助数据库的数据库时，会在辅助服务器中创建新的辅助节点。

  > [!IMPORTANT]
  > 确保辅助服务器没有使用同一名称的数据库，除非它是现有的辅助数据库。 在 SQL 托管实例的故障转移组中，将复制所有用户数据库。 无法选择复制故障转移组中的一部分用户数据库。

- **将弹性池中的数据库添加到故障转移组**

  可将一个弹性池内的所有或多个数据库放入同一故障转移组。 如果主数据库在弹性池中，将在具有相同名称的弹性池（辅助池）中自动创建辅助数据库。 必须确保辅助服务器包含名称完全相同的弹性池，并有足够的可用容量来托管将由故障转移组创建的辅助数据库。 如果在辅助池中已有辅助数据库的池中添加数据库，则该异地复制链接由组继承。 在不属于故障转移组的服务器中添加已有辅助数据库的数据库时，会在辅助池中创建新的辅助数据库。
  
- **初始种子设定**

  将数据库、弹性池或托管实例添加到故障转移组时，在数据复制开始之前，会有一个初始种子设定阶段。 初始种子设定阶段的操作耗时最长且开销最大。 初始种子设定完成后，数据将会同步，此后只会复制后续的数据更改。 完成初始种子设定所需的时间取决于数据大小、复制数据库的数量，以及故障转移组中的实体之间的链接速度。 在正常情况下，SQL 数据库的可能的种子设定速度最多为 500 GB （一小时），对于 SQL 托管实例最多为 360 GB。 种子设定将对所有数据库并行执行。

  对于 SQL 托管实例，请考虑在估计初始播种阶段时，两个实例之间的快速路由链接的速度。 如果两个实例之间的链接速度比所需速度要慢，则种子设定所需的时间可能会受到较大影响。 可以根据所述种子设定速度、数据库数量、数据总大小和链接速度，来估算在数据复制开始之前初始种子设定阶段花费的时间。 例如，对于单个 100 GB 数据库，如果链接能够按小时推送 84 GB，并且没有任何其他数据库被设定为种子，则初始种子阶段将需要大约1.2 小时。 如果链路每小时只能传输 10 GB，则为 100 GB 数据库设定种子需要大约 10 小时。 如果有多个数据库要复制，则种子设定将会并行执行，在链接速度较慢的情况下，初始种子设定阶段可能需要相当长的时间，尤其是为所有数据库中的数据并行设定种子超过可用的链接带宽时。 如果两个实例之间的网络带宽受限制，而你要将多个托管实例添加到故障转移组，请考虑按顺序逐个地将多个托管实例添加到故障转移组。 在两个托管实例之间提供了适当大小的网关 SKU，如果公司网络带宽允许，则可以实现速度高达 360 GB （1小时）。  

- **DNS 区域**

  创建新 SQL 托管实例时自动生成的唯一 ID。 将为此实例预配一个多域 (SAN) 证书，以便对与同一 DNS 区域中的任何实例建立的客户端连接进行身份验证。 同一故障转移组中的两个托管实例必须共享 DNS 区域。
  
  > [!NOTE]
  > 为 SQL 数据库创建的故障转移组不需要 DNS 区域 ID。

- **故障转移组读写侦听器**

  一个 DNS CNAME 记录，指向当前主要节点的 URL。 此记录是创建故障转移组时自动创建的，可让读写工作负载在故障转移发生后主节点发生更改时，以透明方式重新连接到主数据库。 在服务器上创建故障转移组时，侦听器 URL 的 DNS CNAME 记录格式为 `<fog-name>.database.windows.net`。 在 SQL 托管实例上创建故障转移组时，侦听器 URL 的 DNS CNAME 记录格式为 `<fog-name>.<zone_id>.database.windows.net`。

- **故障转移组只读侦听器**

  构成的 DNS CNAME 记录，指向只读侦听器，后者指向辅助节点的 URL。 此记录是创建故障转移组时自动创建的，可让只读 SQL 工作负荷使用指定的负载均衡规则以透明方式连接到辅助数据库。 在服务器上创建故障转移组时，侦听器 URL 的 DNS CNAME 记录格式为 `<fog-name>.secondary.database.windows.net`。 在 SQL 托管实例上创建故障转移组时，侦听器 URL 的 DNS CNAME 记录格式为 `<fog-name>.secondary.<zone_id>.database.windows.net`。

- **自动故障转移策略**

  默认使用自动故障转移策略配置故障转移组。 检测到故障并且宽限期到期后，Azure 会触发故障转移。 系统必须确保，因影响范围太大，内置[高可用性基础结构](high-availability-sla.md)无法缓解服务中断。 如果要从应用程序控制故障转移工作流，可以关闭自动故障转移。
  
  > [!NOTE]
  > 由于验证中断规模及其缓解速度涉及到运营团队的人工措施，因此不能将宽限期设置为一小时以下。 此限制适用于故障转移组中的所有数据库，不管其数据同步状态如何。

- **只读故障转移策略**

  默认禁用只读侦听器的故障转移功能。 这可确保在辅助数据库脱机时，主数据库的性能不会受到影响。 但是，这也意味辅助数据库恢复前，只读会话将无法连接。 如果不能容忍只读会话停机，但能容忍以主数据库的潜在性能降级为代价将主数据库临时用于只读和读写流量，则可以通过配置 `AllowReadOnlyFailoverToPrimary` 属性为只读侦听器启用故障转移。 在这种情况下，如果辅助节点不可用，则会将只读流量自动重定向到主要节点。

- **计划的故障转移**

   将辅助角色切换为主要角色之前，计划内故障转移在主要数据库与辅助数据库之间执行完全同步。 这可以保证数据不会丢失。 计划内故障转移用于以下场景：

  - 不可接受数据丢失时在生产环境中执行灾难恢复 (DR) 演练
  - 将数据库重新定位到不同的区域
  - 缓解服务中断（故障回复）后将数据库恢复到主要区域。

- **未计划的故障转移**

   计划外故障转移或强制故障转移立即将辅助角色切换为主要角色，而不与主要节点进行任何同步。 此操作会导致数据丢失。 在服务中断期间当主要节点不可访问时，计划外故障转移将用作恢复方法。 原始主要节点重新联机后，将在不进行同步的情况下自动重新连接，并成为新的辅助节点。

- **手动故障转移**

  可随时手动启动故障转移，而不考虑自动故障转移配置。 如果未配置自动故障转移策略，则需要执行手动故障转移才能将故障转移组中的数据库恢复到辅助节点。 可通过完整数据同步启动强制或友好的故障转移。 后者可用于将主要节点重新定位到次要区域。 故障转移完成后，会自动更新 DNS 记录，以确保与新的主要节点建立连接

- **数据丢失宽限期**

  由于主数据库和辅助数据库是使用异步复制进行同步的，因此故障转移可能会导致数据丢失。 可以自定义自动故障转移策略，以便反映应用程序对数据丢失的容错。 通过配置 `GracePeriodWithDataLossHours`，可以控制系统启动可能导致数据丢失的故障转移之前的等待时间。

- **多个故障转移组**

  可为同一对服务器配置多个故障转移组以控制故障转移规模。 每个组均独立进行故障转移。 如果多租户应用程序使用弹性池，则可使用此功能来混合每个池的主数据库和辅助数据库。 采用这种方式可将服务中断的影响范围缩小到一半的租户中。

  > [!NOTE]
  > SQL 托管实例不支持多个故障转移组。
  
## <a name="permissions"></a>权限

通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 管理故障转移组的权限。 [SQL Server 参与者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)角色拥有管理故障转移组所需的全部权限。

### <a name="create-failover-group"></a>创建故障转移组

若要创建某个故障转移组，需要对主服务器和辅助服务器，以及该故障转移组中的所有数据库拥有 RBAC 写入访问权限。 对于 SQL 托管实例，需要对主要和辅助 SQL 托管实例拥有 RBAC 写入访问权限，但对单个数据库的权限无关紧要，因为无法在故障转移组中添加或删除单个 SQL 托管实例数据库。

### <a name="update-a-failover-group"></a>更新故障转移组

若要更新某个故障转移组，需要对该故障转移组，以及当前主服务器或托管实例上的所有数据库拥有 RBAC 写入访问权限。  

### <a name="fail-over-a-failover-group"></a>对故障转移组进行故障转移

若要对某个故障转移组进行故障转移，需要对新的主服务器或托管实例上的故障转移组拥有 RBAC 写入访问权限。

## <a name="best-practices-for-sql-database"></a>SQL 数据库的最佳做法

自动故障转移组必须在主服务器上进行配置，需将其连接到不同 Azure 区域中的辅助服务器。 组可以包含这些服务器中的所有或部分数据库。 下图演示了使用多个数据库和自动故障转移组的异地冗余云应用程序的典型配置。

![关系图显示了使用多个数据库和自动故障转移组的异地冗余云应用程序的典型配置。](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> 有关将 SQL 数据库中的数据库添加到故障转移组的详细分步教程，请参阅[将 SQL 数据库添加到故障转移组](failover-group-add-single-database-tutorial.md)。

在设计具有业务连续性的服务时，请遵循以下一般准则：

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>使用一个或多个故障转移组来管理多个数据库的故障转移

可在不同区域的两个服务器（主服务器和辅助服务器）之间创建一个或多个故障转移组。 每组可包含一个或多个数据库，这些数据库是在所有或某些主数据库因主要区域中的服务中断而变得不可用时，作为单元恢复的。 故障转移组使用服务目标作为主数据库创建异地辅助数据库。 如果将现有的异地复制关系添加到故障转移组，请确保使用与主数据库相同的服务层级和计算大小来配置异地辅助数据库。
  
> [!IMPORTANT]
> 对于 Azure SQL 数据库，目前不支持创建在不同订阅中的两个服务器之间进行故障转移的组。 如果在故障转移组创建以后将主服务器或辅助服务器移到另一订阅，则可能导致故障转移请求和其他操作失败。

### <a name="using-read-write-listener-for-oltp-workload"></a>使用读写侦听器处理 OLTP 工作负荷

执行 OLTP 操作时，请使用 `<fog-name>.database.windows.net` 作为服务器 URL，连接将自动定向到主要节点。 此 URL 在故障转移后不会更改。 请注意，故障转移涉及更新 DNS 记录，以便仅在刷新客户端 DNS 缓存后，客户端连接才会重定向到新的主数据库。

### <a name="using-read-only-listener-for-read-only-workload"></a>使用只读侦听器处理只读工作负荷

如果你有一个在逻辑上隔离的只读工作负荷，且它允许存在一些过时数据，则可在应用程序中使用辅助数据库。 对于只读的会话，请使用 `<fog-name>.secondary.database.windows.net` 作为服务器 URL，连接将自动定向到辅助节点。 此外，还建议使用 `ApplicationIntent=ReadOnly` 在连接字符串中指示读取意向。 如果要确保只读工作负荷在故障转移后或辅助服务器脱机时可以重新连接，请确保配置故障转移策略的 `AllowReadOnlyFailoverToPrimary` 属性。

### <a name="preparing-for-performance-degradation"></a>为性能降低做好准备

典型的 Azure 应用程序使用多个 Azure 服务，并由多个组件构成。 故障转移组的自动故障转移是基于 Azure SQL 组件本身的状态触发的。 主要区域中的其他 Azure 服务可能不受中断的影响，其组件可能仍在该区域中可用。 将主数据库切换到灾难恢复区域后，依赖组件之间的延迟可能会增大。 若要避免较高延迟对应用程序性能造成影响，请确保对灾难恢复区域中的所有应用程序组件采用冗余配置，并遵循以下[网络安全指导原则](#failover-groups-and-network-security)。

### <a name="preparing-for-data-loss"></a>为数据丢失做好准备

如果检测到服务中断，Azure 会等待 `GracePeriodWithDataLossHours` 指定的期限。 默认值为 1 小时。 如果不能承受丢失数据，请确保将 `GracePeriodWithDataLossHours` 设置为一个足够大的数字，如 24 小时。 使用手动组故障转移从辅助节点故障回复到主要节点。

> [!IMPORTANT]
> DTU 少于或等于 800、使用异地复制的数据库超过 250 个的弹性数据库池可能会遇到更长的计划故障转移和性能下降等问题。  这些问题更可能在写密集型工作负荷下发生，例如，异地复制终结点广泛分隔于各个地理位置，或者每个数据库使用多个辅助终结点。  当异地复制滞后随着时间推移增加时，这些问题的症状便会显现。  这种滞后可以使用 [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) 进行监视。  如果发生这些问题，缓解方法包括增加池 DTU 的数量或者减少同一池中异地复制数据库的数量。

### <a name="changing-secondary-region-of-the-failover-group"></a>更改故障转移组的次要区域

为了演示更改顺序，我们假设服务器 A 是主服务器，服务器 B 是现有的辅助服务器，服务器 C 是第三个区域中的新辅助服务器。  若要进行转换，请执行以下步骤：

1. 使用[活动异地复制](active-geo-replication-overview.md)，在服务器 C 中为服务器 A 上的每个数据库创建额外的辅助数据库。 服务器 A 上的每个数据库具有两个辅助数据库，其中一个位于服务器 B 上，另一个位于服务器 C 上。这可以保证主数据库在转换过程中仍受保护。
2. 删除故障转移组。 此时，登录将会失败。 这是因为，故障转移组侦听器的 SQL 别名已删除，因此网关无法识别故障转移组名称。
3. 在服务器 A 与 C 之间重新创建同名的故障转移组。此时，登录将不再失败。
4. 将服务器 A 上的所有主数据库添加到新的故障转移组。
5. 删除服务器 B。服务器 B 上的所有数据库将自动删除。

### <a name="changing-primary-region-of-the-failover-group"></a>更改故障转移组的主要区域

为了演示更改顺序，我们假设服务器 A 是主服务器，服务器 B 是现有的辅助服务器，服务器 C 是第三个区域中的新主服务器。  若要进行转换，请执行以下步骤：

1. 执行计划性故障转移，将主服务器切换到 B。服务器 A 将成为新的辅助服务器。 故障转移可能会导致几分钟的停机。 实际时间取决于故障转移组的大小。
2. 使用[活动异地复制](active-geo-replication-overview.md)，在服务器 C 中为服务器 B 上的每个数据库创建额外的辅助数据库。 服务器 B 上的每个数据库具有两个辅助数据库，其中一个位于服务器 A 上，另一个位于服务器 C 上。这可以保证主数据库在转换过程中仍受保护。
3. 删除故障转移组。 此时，登录将会失败。 这是因为，故障转移组侦听器的 SQL 别名已删除，因此网关无法识别故障转移组名称。
4. 在服务器 B 与 C 之间重新创建同名的故障转移组。此时，登录将不再失败。
5. 将服务器 B 上的所有主数据库添加到新的故障转移组。
6. 执行故障转移组的计划性故障转移来切换 B 和 C。现在，服务器 C 将成为主服务器，B 将成为辅助服务器。 服务器 A 上的所有辅助数据库将自动链接到 C 上的主数据库。如步骤 1 中所述，故障转移可能会导致几分钟的停机。
7. 删除服务器 A。服务器 A 上的所有数据库将自动删除。

> [!IMPORTANT]
> 删除故障转移组时，也会删除侦听器终结点的 DNS 记录。 此时，其他某人能够创建同名故障转移组或服务器别名的概率不为零，这会阻止你再次使用此故障转移组或服务器别名。 若要将风险降到最低，请不要使用常用的故障转移组名称。

## <a name="best-practices-for-sql-managed-instance"></a>SQL 托管实例的最佳做法

自动故障转移组必须在主要实例上进行配置，需将其连接到不同 Azure 区域中的辅助实例。  实例中的所有数据库将复制到辅助实例。

下图演示了使用托管实例和自动故障转移组的异地冗余云应用程序的典型配置。

![自动故障转移示意图](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> 有关添加 SQL 托管实例以使用故障转移组的详细分步教程，请参阅[将托管实例添加到故障转移组](../managed-instance/failover-group-add-instance-tutorial.md)。

如果应用程序使用 SQL 托管实例作为数据层，进行业务连续性设计时，请遵循以下一般准则：

### <a name="creating-the-secondary-instance"></a>创建辅助实例

若要确保故障转移后与主要 SQL 托管实例的连接不中断，主要实例和辅助实例必须位于同一 DNS 区域。 将会保证同一个多域 (SAN) 证书可用于对与故障转移组中的两个实例之一建立的客户端连接进行身份验证。 准备好将应用程序部署到生产环境后，在不同的区域中创建一个辅助 SQL 托管实例，并确保它与主要 SQL 托管实例共享 DNS 区域。 为此，可以使用 Azure 门户、PowerShell 或 REST API 指定可选 `DNS Zone Partner` 参数。

> [!IMPORTANT]
> 在子网中创建的第一个托管实例确定同一子网中所有后续实例的 DNS 区域。 这意味着，同一子网中的两个实例不能属于不同的 DNS 区域。

有关在主要实例所在的 DNS 区域中创建辅助 SQL 托管实例的详细信息，请参阅[创建辅助托管实例](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance)。

### <a name="using-geo-paired-regions"></a>使用地理配对区域

出于性能原因，将两个托管实例部署到 [配对区域](../../best-practices-availability-paired-regions.md) 。 与非配对区域相比，位于地域配对区域中的托管实例具有更好的性能。 

### <a name="enabling-replication-traffic-between-two-instances"></a>在两个实例之间启用复制流量

由于每个实例隔离在其自身的 VNet 中，因此，必须允许这些 VNet 之间的双向流量。 请参阅 [Azure VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>在不同订阅中的托管实例之间创建故障转移组

可以在两个不同订阅中的 SQL 托管实例之间创建故障转移组，前提是订阅与相同的 [Azure Active Directory 租户](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology)关联。 使用 PowerShell API 时，可以通过为辅助 SQL 托管实例指定 `PartnerSubscriptionId` 参数来执行此操作。 使用 REST API 时，`properties.managedInstancePairs` 参数中包含的每个实例 ID 都可以有自己的订阅 ID。
  
> [!IMPORTANT]
> Azure 门户不支持创建跨不同订阅的故障转移组。 此外，对于跨不同订阅和/或资源组的现有故障转移组，无法通过门户从主要 SQL 托管实例手动启动故障转移。 改为从异地辅助实例启动它。

### <a name="managing-failover-to-secondary-instance"></a>管理到辅助实例的故障转移

故障转移组将管理 SQL 托管实例中所有数据库的故障转移。 创建某个组后，实例中的每个数据库将自动异地复制到辅助 SQL 托管实例。 无法使用故障转移组针对一部分数据库启动部分故障转移。

> [!IMPORTANT]
> 如果从主要 SQL 托管实例中删除了某个数据库，该数据库也会在异地辅助 SQL 托管实例上自动删除。

### <a name="using-read-write-listener-for-oltp-workload"></a>使用读写侦听器处理 OLTP 工作负荷

执行 OLTP 操作时，请使用 `<fog-name>.zone_id.database.windows.net` 作为服务器 URL，连接将自动定向到主要节点。 此 URL 在故障转移后不会更改。 故障转移涉及更新 DNS 记录，以便仅在刷新客户端 DNS 缓存后，客户端连接才会重定向到新的主要节点。 由于辅助实例与主要实例共享 DNS 区域，客户端应用程序可以使用相同的 SAN 证书重新连接到辅助实例。

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>使用只读侦听器连接到辅助实例

如果你有一个在逻辑上隔离的只读工作负荷，且它允许存在一些过时数据，则可在应用程序中使用辅助数据库。 若要直接连接到异地复制的辅助节点，请使用 `<fog-name>.secondary.<zone_id>.database.windows.net` 作为服务器 URL，这样可以直接连接到异地复制的辅助节点。

> [!NOTE]
> 在某些服务层级中，SQL 数据库支持通过[只读副本](read-scale-out.md)，使用一个只读副本的容量和连接字符串中的 `ApplicationIntent=ReadOnly` 参数对只读查询工作负载进行负载均衡。 如果配置了异地复制的辅助节点，则可以使用此功能连接到主要位置或异地复制位置中的只读副本。
>
> - 若要连接到主要位置中的只读副本，请使用 `<fog-name>.<zone_id>.database.windows.net`。
> - 若要连接到辅助位置中的只读副本，请使用 `<fog-name>.secondary.<zone_id>.database.windows.net`。

### <a name="preparing-for-performance-degradation"></a>为性能降低做好准备

典型的 Azure 应用程序使用多个 Azure 服务，并由多个组件构成。 故障转移组的自动故障转移是基于 Azure SQL 组件本身的状态触发的。 主要区域中的其他 Azure 服务可能不受中断的影响，其组件可能仍在该区域中可用。 将主数据库切换到灾难恢复区域后，依赖组件之间的延迟可能会增大。 若要避免较高延迟对应用程序性能造成影响，请确保对灾难恢复区域中的所有应用程序组件采用冗余配置，并遵循以下[网络安全指导原则](#failover-groups-and-network-security)。

### <a name="preparing-for-data-loss"></a>为数据丢失做好准备

如果检测到服务中断，则根据我们所知，如果没有数据丢失，将触发读写故障转移。 否则，等待指定的期限。 否则，它会等待 `GracePeriodWithDataLossHours` 指定的期限。 如果指定了 `GracePeriodWithDataLossHours`，则可能会丢失数据。 一般情况下，在中断期间 Azure 倾向于可用性。 如果不能承受丢失数据，请务必将 GracePeriodWithDataLossHours 设置为一个足够大的数字，例如 24 小时。

启动故障转移后，读写侦听器的 DNS 更新会立即发生。 此操作不会导致数据丢失。 但是，在正常情况下，切换数据库角色的过程可能需要 5 分钟时间。 在完成之前，新主要实例中的某些数据库仍是只读的。 如果使用 PowerShell 启动故障转移，则整个操作是同步的。 如果使用 Azure 门户启动故障转移，UI 将指示完成状态。 如果使用 REST API 启动故障转移，可以使用标准 Azure 资源管理器的轮询机制来监视完成状态。

> [!IMPORTANT]
> 使用手动组故障转移可将主要数据库移回到原始位置。 缓解导致故障转移的服务中断问题后，可将主要数据库移到原始位置。 为此，应该启动组的手动故障转移。
  
### <a name="changing-secondary-region-of-the-failover-group"></a>更改故障转移组的次要区域

假设实例 A 是主实例，实例 B 是现有的辅助实例，实例 C 是第三个区域中的新辅助实例。  若要进行转换，请执行以下步骤：

1. 在同一个 DNS 区域中创建大小与 A 相同的实例 C。
2. 删除实例 A 与 B 之间的故障转移组。此时，登录将会失败，因为故障转移组侦听器的 SQL 别名已删除，因此网关无法识别故障转移组名称。 辅助数据库将从主实例断开连接，并成为读写数据库。
3. 在实例 A 与 C 之间创建同名的故障转移组。按照[包含 SQL 托管实例的故障转移组教程](../managed-instance/failover-group-add-instance-tutorial.md)中的说明操作。 这是一个与数据大小相关的操作，实例 A 中的所有数据库都已设定种子并同步后，此操作将会完成。
4. 如果不需要实例 B，请将其删除，以免产生不必要的费用。

> [!NOTE]
> 完成步骤 2 到 3 后，如果实例 A 发生灾难性故障，其中的数据库将仍不受保护。

### <a name="changing-primary-region-of-the-failover-group"></a>更改故障转移组的主要区域

假设实例 A 是主实例，实例 B 是现有的辅助实例，实例 C 是第三个区域中的新主实例。  若要进行转换，请执行以下步骤：

1. 在同一个 DNS 区域中创建大小与 B 相同的实例 C。
2. 连接到实例 B，并手动故障转移以将主实例切换到 B。实例 A 将自动成为新的辅助实例。
3. 删除实例 A 与 B 之间的故障转移组。此时，登录将会失败，因为故障转移组侦听器的 SQL 别名已删除，因此网关无法识别故障转移组名称。 辅助数据库将从主实例断开连接，并成为读写数据库。
4. 在实例 A 与 C 之间创建同名的故障转移组。按照[包含托管实例的故障转移组教程](../managed-instance/failover-group-add-instance-tutorial.md)中的说明操作。 这是一个与数据大小相关的操作，实例 A 中的所有数据库都已设定种子并同步后，此操作将会完成。
5. 如果不需要实例 A，请将其删除，以免产生不必要的费用。

> [!CAUTION]
> 完成步骤 3 到 4 后，如果实例 A 发生灾难性故障，其中的数据库将仍不受保护。

> [!IMPORTANT]
> 删除故障转移组时，也会删除侦听器终结点的 DNS 记录。 此时，其他某人能够创建同名故障转移组或服务器别名的概率不为零，这会阻止你再次使用此故障转移组或服务器别名。 若要将风险降到最低，请不要使用常用的故障转移组名称。

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>启用依赖于系统数据库中的对象的方案
系统数据库不会复制到故障转移组中的辅助实例。 若要启用依赖于系统数据库中的对象的方案，请确保在辅助实例上创建相同的对象。 例如，如果你计划在辅助实例上使用相同的登录名，请确保使用相同的 SID 创建它们。 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>故障转移组和网络安全

对于某些应用程序，安全规则要求只允许特定组件（如 VM、Web 服务等）通过网络访问数据层。此要求对业务连续性设计和故障转移组的使用提出了一些挑战。 在实施此类受限访问时，请考虑以下选项。

### <a name="using-failover-groups-and-virtual-network-rules"></a>使用故障转移组和虚拟网络规则

如果使用[虚拟网络服务终结点和规则](vnet-service-endpoint-rule-overview.md)来限制对 SQL 数据库或 SQL 托管实例中的数据库的访问，请注意每个虚拟网络服务终结点仅适用于一个 Azure 区域。 终结点不允许其他区域接受来自该子网的通信。 因此，只有部署在同一区域中的客户端应用程序才能连接到主数据库。 因为故障转移会导致 SQL 数据库客户端会话重新路由到不同（次要）区域中的服务器，所以源自该区域之外的客户端的这些会话将失败。 因此，如果参与的服务器或实例包含在虚拟网络规则中，则无法启用自动故障转移策略。 若要支持手动故障转移，请执行以下步骤：

1. 在次要区域中预配应用程序前端组件（Web 服务、虚拟机等）的冗余副本
2. 为主服务器和辅助服务器分别配置[虚拟网络规则](vnet-service-endpoint-rule-overview.md)
3. 使用流量管理器配置启用[前端故障转移](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. 检测到服务中断时启动手动故障转移。 此选项针对需要在前端和数据层之间保持一致延迟的应用程序进行了优化，并支持在前端和/或数据层受到服务中断的影响时进行恢复。

> [!NOTE]
> 如果使用**只读侦听器**对只读工作负荷进行负载均衡，请确保在次要区域中的 VM 或其他资源上执行此工作负荷，以便它可以连接到辅助数据库。

### <a name="use-failover-groups-and-firewall-rules"></a>使用故障转移组和防火墙规则

如果业务连续性计划要求使用自动故障转移组进行故障转移，则可以使用传统防火墙规则限制对 SQL 数据库中的数据库的访问。 若要支持自动故障转移，请执行以下步骤：

1. [创建公共 IP](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [创建公共负载均衡器](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)并为其分配公共 IP。
3. 为前端组件[创建虚拟网络和虚拟机](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)
4. [创建网络安全组](../../virtual-network/security-overview.md)并配置入站连接。
5. 使用“Sql”[服务标记](../../virtual-network/security-overview.md#service-tags)确保出站连接向 Azure SQL 数据库开放。
6. 创建 [SQL 数据库防火墙规则](firewall-configure.md)，以允许来自步骤 1 中创建的公共 IP 地址的入站流量。

有关如何配置出站访问以及在防火墙规则中使用哪个 IP 的详细信息，请参阅[负载均衡器出站连接](../../load-balancer/load-balancer-outbound-connections.md)。

上述配置将确保自动故障转移不会阻止来自前端组件的连接，并假定应用程序可以容忍前端与数据层之间的较长延迟。

> [!IMPORTANT]
> 若要保证区域服务中断的业务连续性，则必须确保前端组件和数据库的地理冗余。

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>在托管实例及其 VNet 之间启用异地复制

在两个不同区域中的主要和辅助 SQL 托管实例之间设置故障转移组时，将使用独立的虚拟网络来隔离每个实例。 若要允许这些 VNet 之间的复制流量，请确保满足以下先决条件：

- SQL 托管实例的两个实例需位于不同的 Azure 区域中。
- SQL 托管实例的这两个实例需位于相同的服务层级，并且具有相同的存储大小。
- SQL 托管实例的辅助实例必须是空的（不包含任何用户数据库）。
- 需要通过 [VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)或 [Express Route](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) 来连接 SQL 托管实例的实例使用的虚拟网络。 当两个虚拟网络通过本地网络连接时，请确保没有任何防火墙规则阻止端口 5022 和 11000-11999。 下面的说明中所述的限制支持全局 VNet 对等互连。

   > [!IMPORTANT]
   > [在9/22/2020 中，我们为新创建的虚拟群集宣布了全局虚拟网络对等互连](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/)。 这意味着，对于在公告日期之后在空子网中创建的 SQL 托管实例，以及在这些子网中创建的所有后续托管实例，都支持全局虚拟网络对等互连。 对于所有其他 SQL 托管实例的对等互连，都将限制为同一区域的网络，因为 [全局虚拟网络对等互连的限制](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)。 有关更多详细信息，另请参阅 [Azure 虚拟网络常见问题解答](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)一文的相关部分。 

- 两个 SQL 托管实例 VNet 的 IP 地址不能重叠。
- 需要设置网络安全组 (NSG)，使端口 5022 和端口范围 11000~12000 保持打开，以便能够从其他托管实例的子网建立入站和出站连接。 目的是允许实例之间的复制流量。

   > [!IMPORTANT]
   > NSG 安全规则配置不当会导致数据库复制操作停滞。

- 辅助 SQL 托管实例上已配置正确的 DNS 区域 ID。 DNS 区域是 SQL 托管实例和基础虚拟群集的属性，其 ID 包含在主机名地址中。 在每个 VNet 中创建第一个 SQL 托管实例时，将生成随机字符串形式的区域 ID。同一个 ID 将分配到同一子网中的所有其他实例。 分配后，无法修改 DNS 区域。 同一故障转移组中包含的 SQL 托管实例必须共享 DNS 区域。 为此，在创建辅助实例时，可以传递主要实例的区域 ID 作为 DnsZonePartner 参数的值。

   > [!NOTE]
   > 有关使用 SQL 托管实例配置故障转移组的详细教程，请参阅[将 SQL 托管实例添加到故障转移组](../managed-instance/failover-group-add-instance-tutorial.md)。

## <a name="upgrading-or-downgrading-a-primary-database"></a>升级或降级主数据库

无需断开连接任何辅助数据库，即可将主数据库升级或降级到不同的计算大小（在相同的服务层级中，但不在“常规用途”与“业务关键”类型之间）。 升级时，建议先升级所有辅助数据库，再升级主数据库。 降级时，请反转顺序：先降级主数据库，再降级所有辅助数据库。 将数据库升级或降级到不同服务层级时，将强制执行此建议操作。

具体而言，建议采用此顺序的目的是避免较低 SKU 上的辅助数据库在过载时出现问题，并且必须在升级或降级过程中重新设定种子。 此外，可以通过将主数据库设为只读来避免问题，代价是针对主数据库的所有读写工作负荷会受到影响。

> [!NOTE]
> 如果你创建了一个辅助数据库作为故障转移组配置的一部分，则我们不建议对辅助数据库进行降级。 这是为了确保激活故障转移后，数据层有足够的容量来处理常规工作负荷。

## <a name="preventing-the-loss-of-critical-data"></a>防止丢失关键数据

由于广域网的延迟时间较长，连续复制使用了异步复制机制。 在发生故障时，异步复制会不可避免地丢失某些数据。 但是，某些应用程序可能要求不能有数据丢失。 为了保护这些关键更新，应用程序开发人员可以在提交事务后立即调用 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 系统过程。 调用 `sp_wait_for_database_copy_sync` 会阻止调用线程，直到将上次提交的事务传输到辅助数据库。 但是，它不会等待传输的事务提交到辅助数据库进行重播。 `sp_wait_for_database_copy_sync` 的范围限定为特定的连续复制链接。 对主数据库具有连接权限的任何用户都可以调用此过程。

> [!NOTE]
> `sp_wait_for_database_copy_sync` 可防止故障转移后的数据丢失，但不能保证读取访问完全同步。 `sp_wait_for_database_copy_sync` 过程调用导致的延迟可能会很明显，具体取决于调用时的事务日志大小。

## <a name="failover-groups-and-point-in-time-restore"></a>故障转移组和时间点还原

有关将时间点还原与故障转移组配合使用的信息，请参阅[时间点恢复 (PITR)](recovery-using-backups.md#point-in-time-restore)。

## <a name="limitations-of-failover-groups"></a>故障转移组的限制

注意以下限制：

- 无法在同一 Azure 区域中的两个服务器或实例之间创建故障转移组。
- 无法重命名故障转移组。 需要删除该组，并使用不同的名称重新创建它。
- 故障转移组中的实例不支持数据库重命名。 需要临时删除故障转移组，才能重命名数据库。
- 系统数据库不会复制到故障转移组中的辅助实例。 因此，除非在辅助实例上手动创建对象，否则依赖于系统数据库中的对象的方案将不可能在辅助实例上出现。

## <a name="programmatically-managing-failover-groups"></a>以编程方式管理故障转移组

如上所述，也可以使用 Azure PowerShell 和 REST API 以编程方式管理自动故障转移组和活动异地复制。 下表描述了可用的命令集。 活动异地复制包括一组用于管理的 Azure 资源管理器 API，其中包括 [Azure SQL 数据库 REST API](https://docs.microsoft.com/rest/api/sql/) 和 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/)。 这些 API 需要使用资源组，并支持基于角色的安全性 (RBAC)。 有关如何实现访问角色的详细信息，请参阅 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。

### <a name="manage-sql-database-failover"></a>管理 SQL 数据库故障转移

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | 说明 |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |此命令会创建故障转移组，并将其同时注册到主服务器和辅助服务器|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | 从服务器中删除故障转移组 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 检索故障转移组的配置 |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |修改故障转移组的配置 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | 触发故障转移组到辅助服务器的故障转移 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|将一个或更多个数据库添加到故障转移组|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| 命令 | 说明 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |此命令会创建故障转移组，并将其同时注册到主服务器和辅助服务器|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | 从服务器中删除故障转移组 |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | 检索故障转移组配置 |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |修改故障转移组的配置，和/或将一个或多个数据库添加到故障转移组|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 触发故障转移组到辅助服务器的故障转移 |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | 说明 |
| --- | --- |
| [创建或更新故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | 创建或更新故障转移组 |
| [删除故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | 从服务器中删除故障转移组 |
| [故障转移（计划内）](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | 在完全同步数据的情况下，触发从当前主服务器到辅助服务器的故障转移。|
| [强制故障转移允许数据丢失](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | 在不同步数据的情况下，触发从当前主服务器到辅助服务器的故障转移。 此操作可能导致数据丢失。 |
| [获取故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | 检索故障转移组的配置。 |
| [按服务器列出故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | 列出服务器上的故障转移组。 |
| [更新故障转移组](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | 更新故障转移组的配置。 |

---

### <a name="manage-sql-managed-instance-failover"></a>管理 SQL 托管实例故障转移


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | 说明 |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |此命令会创建故障转移组，并将其同时注册到主实例和辅助实例|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |修改故障转移组的配置|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |检索故障转移组的配置|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |触发故障转移组到辅助实例的故障转移|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | 删除故障转移组|


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

| 命令 | 说明 |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |此命令会创建故障转移组，并将其同时注册到主服务器和辅助服务器|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | 从服务器中删除故障转移组 |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | 检索故障转移组配置 |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |修改故障转移组的配置，和/或将一个或多个数据库添加到故障转移组|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 触发故障转移组到辅助服务器的故障转移 |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | 说明 |
| --- | --- |
| [创建或更新故障转移组](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | 创建或更新故障转移组的配置 |
| [删除故障转移组](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | 从实例中删除故障转移组 |
| [故障转移（计划内）](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | 在完全同步数据的情况下，触发从当前主实例到此实例的故障转移。 |
| [强制故障转移允许数据丢失](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | 在不同步数据的情况下，触发从当前主实例到辅助实例的故障转移。 此操作可能导致数据丢失。 |
| [获取故障转移组](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | 检索故障转移组的配置。 |
| [列出故障转移组 - 按位置列出](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | 列出某个位置中的故障转移组。 |

---

## <a name="next-steps"></a>后续步骤

- 有关详细教程，请参阅
  - [将 SQL 数据库添加到故障转移组](failover-group-add-single-database-tutorial.md)
  - [将弹性池添加到故障转移组](failover-group-add-elastic-pool-tutorial.md)
  - [将 SQL 托管实例添加到故障转移组](../managed-instance/failover-group-add-instance-tutorial.md)
- 有关示例脚本，请参阅：
  - [使用 PowerShell 为 Azure SQL 数据库配置活动异地复制](scripts/setup-geodr-and-failover-database-powershell.md)
  - [使用 PowerShell 为 Azure SQL 数据库中的共用数据库配置活动异地复制](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [使用 PowerShell 将 Azure SQL 数据库添加到故障转移组](scripts/add-database-to-failover-group-powershell.md)
- 有关业务连续性概述和应用场景，请参阅[业务连续性概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](automated-backups-overview.md)。
- 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](recovery-using-backups.md)。
- 若要了解新主服务器和数据库的身份验证要求，请参阅[灾难恢复后的 SQL 数据库安全性](active-geo-replication-security-configure.md)。
