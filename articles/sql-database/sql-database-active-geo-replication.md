---
title: 活动异地复制
description: 使用活动异地复制在相同的或不同的数据中心（区域）创建各个数据库的可读辅助数据库。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/17/2020
ms.openlocfilehash: fe006cebe9aab30a6aaa0bdf2bf3362a494f64d7
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77426266"
---
# <a name="creating-and-using-active-geo-replication"></a>创建和使用活动异地复制

活动异地复制是一项 Azure SQL 数据库功能，允许你在相同或不同的数据中心（区域）中的 SQL 数据库服务器上创建单个数据库的可读辅助数据库。

> [!NOTE]
> 托管实例不支持活动异地复制。 对于托管实例的地理故障转移，请使用[自动故障转移组](sql-database-auto-failover-group.md)。

活动异地复制旨在充当业务连续性解决方案，允许应用程序在发生区域性灾难或大规模中断时执行各个数据库的快速灾难恢复。 如果启用了异地复制，则应用程序可以向其他 Azure 区域中的辅助数据库启动故障转移。 在相同或不同的区域中最多支持四个辅助数据库，并且辅助数据库也可以用于只读访问查询。 故障转移必须由应用程序或用户手动启动。 故障转移后，新的主数据库具有不同的连接终结点。 下图演示了使用活动异地复制的异地冗余云应用程序的典型配置。

![活动异地复制](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> SQL 数据库还支持自动故障转移组。 有关详细信息，请参阅[自动故障转移组](sql-database-auto-failover-group.md)。 另外，活动异地复制不受在托管实例中创建的数据库的支持。 考虑将[故障转移组](sql-database-auto-failover-group.md)与托管实例配合使用。

如果主数据库因某种原因而出现故障，或者只是需要脱机，则可以启动故障转移到任何辅助数据库的操作。 当将故障转移激活到辅助数据库之一时，所有其他辅助数据库会自动链接到新的主数据库。

可以使用活动异地复制管理服务器或弹性池中单个数据库或一组数据库的复制和故障转移。 可通过以下方式执行此操作：

- [Azure 门户](sql-database-geo-replication-portal.md)
- [PowerShell：单个数据库](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell：弹性池](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [TRANSACT-SQL：单个数据库或弹性池](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API：单个数据库](https://docs.microsoft.com/rest/api/sql/replicationlinks)


活动异地复制利用 SQL Server 的 [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) 技术，使用快照隔离以异步方式将主数据库上已提交的事务复制到辅助数据库。 自动故障转移组提供基于活动异地复制的组语义，但使用相同的异步复制机制。 尽管在任意给定时间，辅助数据库可能略微滞后于主数据库，但系统可以保证辅助数据永不包含部分事务。 跨区域冗余使应用程序能够在自然灾害、灾难性人为失误或恶意行为导致整个或部分数据中心永久性数据丢失后得以快速恢复。 [业务连续性概述](sql-database-business-continuity.md)中提供了具体的 RPO 数据。

> [!NOTE]
> 如果两个区域之间存在网络故障，我们会每 10 秒钟重试一次以重新建立连接。
> [!IMPORTANT]
> 若要确保对主数据库所做的关键更改已在故障转移前复制到辅助数据库，可以强制同步，以确保复制关键更改（例如密码更新）。 强制同步会阻止调用线程，直到所有提交的事务得到复制，因此会影响性能。 有关详细信息，请参阅 [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync)。 若要监视主数据库和地域辅助数据库之间的复制延迟，请参阅 [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)。

下图显示了配置的活动异地复制示例，其中主数据库位于“美国中北部”区域，辅助数据库位于“美国中南部”区域。

![异地复制关系](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

因为辅助数据库是可读的，并且可用于卸载只读工作负荷，如报表作业。 如果使用活动异地复制，则可在主数据库所在的区域中创建辅助数据库，但不会增加应用程序针对灾难性故障的复原能力。 如果使用自动故障转移组，则会始终在不同的区域中创建辅助数据库。

除了灾难恢复外，活动异地复制还可用于以下情况：

- **数据库迁移**：可以使用活动异地复制将数据库在联机情况下从一台服务器迁移到另一台服务器，只需要极少的停机时间。
- **应用程序升级**：可以在应用程序升级期间创建额外的辅助数据库作为故障回复副本。

若要真正实现业务连续性，只需添加数据中心之间的数据库冗余即可，这只是该解决方案的一部分功能。 在发生灾难性故障后，端对端地恢复应用程序（服务）需要恢复构成该服务的所有组件以及所有依赖服务。 这些组件的示例包括客户端软件（例如，使用自定义 JavaScript 的浏览器）、Web 前端、存储和 DNS。 所有组件必须能够弹性应对相同的故障，并在应用程序的恢复时间目标 (RTO) 值内变为可用，这一点非常关键。 因此，你需要识别所有依赖服务，并了解它们提供的保证和功能。 然后，必须执行适当的步骤来确保对你的服务所依赖的服务执行故障转移期间，你的服务能够正常运行。 有关设计灾难恢复解决方案的详细信息，请参阅[设计使用活动异地复制的灾难恢复云解决方案](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

## <a name="active-geo-replication-terminology-and-capabilities"></a>活动异地复制术语和功能

- **自动异步复制**

  只能通过添加到现有数据库来创建辅助数据库。 辅助数据库可在任何 Azure SQL 数据库服务器中创建。 创建完成之后，使用从主数据库复制的数据填充辅助数据库。 这个过程称为种子设定。 创建辅助数据库并设定其种子后，会自动以异步方式将主数据库的更新复制到辅助数据库。 异步复制是指先在主数据库上提交事务，然后将事务复制到辅助数据库。

- **可读取的辅助数据库**

  应用程序可使用与访问主数据库时所用的相同或不同的安全主体来访问辅助数据库以执行只读操作。 辅助数据库在快照隔离模式下运行，以确保对主数据库的更新的复制（日志重播）不会因辅助数据库上执行的查询操作而延迟。

> [!NOTE]
> 如果主数据库上有架构更新，则日志重播会在辅助数据库上延迟。 因为架构更新需要在辅助数据库上有架构锁。
> [!IMPORTANT]
> 您可以使用异地复制在与主数据库相同的区域中创建辅助数据库。 您可以使用此辅助数据库对同一区域中的只读工作负荷进行负载均衡。 但是，同一区域中的辅助数据库不提供额外的容错能力，因此不适合用于灾难恢复的故障转移目标。 它也不保证可用性区域的隔离。 使用具有[区域冗余配置](sql-database-high-availability.md#zone-redundant-configuration)的业务关键或高级服务层来实现可用性区域隔离。   
>

- **计划内故障转移**

  完全同步完成后，计划的故障转移将切换主数据库和辅助数据库的角色。 这是一项联机操作，它不会导致数据丢失。 操作的时间取决于主副本上需要同步的事务日志的大小。 计划的故障转移设计用于以下方案：（a）在无法接受数据丢失时在生产中执行 DR 演练;（b）：将数据库重新定位到不同的区域;在缓解中断后（c）将数据库返回到主要区域（故障回复）。

- **计划外故障转移**

  计划外故障转移或强制故障转移立即将辅助角色切换为主要角色，而不与主要节点进行任何同步。 提交到主副本但不复制到辅助副本的任何事务都将丢失。 当无法访问主副本时，此操作被设计为故障恢复方法，但必须快速还原数据库的可用性。 原始主副本重新联机时，它会自动重新连接，并成为新的辅助副本。 故障转移之前的所有未同步事务将保留在备份文件中，但不会与新的主副本同步，以避免冲突。 这些事务必须与主数据库的最新版本进行手动合并。
 
- **多个可读的辅助数据库**

  可以为每个主要数据库创建最多 4 个辅助数据库。 如果只有一个辅助数据库，一旦它发生故障，应用程序就会遭受更高的风险，直到创建了新的辅助数据库。 如果存在多个辅助数据库，即使其中一个辅助数据库发生故障，应用程序仍会受到保护。 也可使用其他辅助数据库来横向扩展只读工作负荷。

  > [!NOTE]
  > 如果使用活动异地复制生成全球分布的应用程序，并需要在四个以上的区域中提供数据只读访问，则可以创建辅助数据库的辅助数据库（此过程称为链接）。 采用这种方式几乎可以实现数据库复制的无限制缩放。 此外，链接减少了从主数据库进行复制的开销。 随之而来的弊端是增加了大多数辅助数据库上的复制延迟。

- **在弹性池中异地复制数据库**

  每个辅助数据库可单独参与弹性池或根本不在弹性池中。 每个辅助数据库的池选择是单独的，并且不会依赖任何其他辅助数据库的配置（无论是主数据库还是辅助数据库）。 每个弹性池都包含在一个区域内，因此，同一拓扑中的多个辅助数据库永远无法共享弹性池。


- **用户控制的故障转移和故障回复**

  应用程序或用户可随时会辅助数据库显式切换到主角色。 在实际服务中断期间，应使用“计划外”选项，这会立即将辅助数据库升级为主数据库。 当出现故障的主数据库恢复并再次可用时，系统会自动将恢复的主数据库标记为辅助数据库并使其与新的主数据库保持最新状态。 由于复制的异步特性，在未计划的故障转移期间，如果主数据库在将最新的更改复制到辅助数据库之前出现故障，则可能会丢失少量数据。 当具有多个辅助数据库的主数据库进行故障转移时，系统会自动重新配置复制关系，并将剩余辅助数据库链接到新升级的主数据库，无需任何用户的干预。 导致了故障转移的服务中断得到缓解后，可能需要将应用程序返回到主要区域。 为此，应使用“计划内”选项调用故障转移命令。

## <a name="preparing-secondary-database-for-failover"></a>准备辅助数据库以进行故障转移

若要确保应用程序在故障转移后可以立即访问新的主服务器，请确保正确配置辅助服务器和数据库的身份验证要求。 有关详细信息，请参阅[灾难恢复后的 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。 若要确保在故障转移后符合性，请确保辅助数据库上的备份保留策略与主数据库的保留策略匹配。 这些设置不是数据库的一部分，也不会被复制。 默认情况下，辅助数据库将配置为默认的 PITR 保持期为七天。 有关详细信息，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)。

> [!IMPORTANT]
> 如果数据库是故障转移组的成员，则不能使用异地复制 faiover 命令启动其故障转移。 请考虑使用组的故障转移命令。 如果需要故障转移单个数据库，则必须先将其从故障转移组中删除。 有关详细信息，请参阅[故障转移组](sql-database-auto-failover-group.md)。 


## <a name="configuring-secondary-database"></a>配置辅助数据库

主数据库和辅助数据库都需要有相同的服务层级。 另外，强烈建议创建与主数据库具有相同计算大小（DTU 或 vCore）的辅助数据库。 如果主数据库遇到大量写入工作负荷，则计算大小较小的辅助数据库可能无法跟上它。 这将导致辅助副本上的重做延迟，并且可能不可用。 如果需要强制故障转移，则在主数据库后滞后的辅助数据库也会面临大数据丢失的风险。 若要缓解这些风险，有效的活动异地复制会限制主要的日志速率，以允许其辅助数据库保持同步。 不均衡辅助配置的另一个结果是，在故障转移后，应用程序的性能会因为新的主数据库的计算能力不足而受到影响。 需要将其升级到更高的计算级别，这在中断中断之前将无法进行。 


> [!IMPORTANT]
> 除非将辅助数据库配置为具有与主数据库相同的计算大小，否则无法保证发布的 RPO = 5 秒。 


如果决定创建具有较低计算大小的辅助数据库，Azure 门户上的日志 IO 百分比图表提供了一种评估维持复制负荷所需的辅助数据库的最小计算大小的好办法。 例如，如果主数据库是 P6 (1000 DTU)，其日志 IO 百分比为 50%，则辅助数据库需要至少为 P4 (500 DTU)。 还可以使用 [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 或 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 数据库视图检索日志 IO 数据。  该限制将报告为[dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)和[sys.databases dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)数据库视图中的 HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO 等待状态。 

有关 SQL 数据库计算大小的详细信息，请参阅[什么是 SQL 数据库服务层级](sql-database-purchase-models.md)。

## <a name="cross-subscription-geo-replication"></a>跨订阅异地复制

若要在属于不同订阅的两个数据库之间设置活动异地复制（无论是在同一个租户下），必须遵循本部分中所述的特殊步骤。  此过程基于 SQL 命令，要求： 

- 在两个服务器上创建特权登录
- 将 IP 地址添加到在两个服务器上执行更改的客户端（例如运行 SQL Server Management Studio 的主机的 IP 地址）的允许列表。 

执行更改的客户端需要对主服务器的网络访问。 尽管客户端的相同 IP 地址必须添加到辅助服务器上的允许列表中，但并不严格要求与辅助服务器建立网络连接。 

### <a name="on-the-master-of-the-primary-server"></a>在主服务器的主服务器上

1. 将 IP 地址添加到执行更改的客户端允许列表（有关详细信息，请参阅[配置防火墙](sql-database-firewall-configure.md)）。 
1. 创建专用于设置活动异地复制的登录名（并根据需要调整凭据）：

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. 创建相应的用户并将其分配给 dbmanager 角色： 

   ```sql
   create user geodrsetup for login geodrsetup
   alter role geodrsetup dbmanager add member geodrsetup
   ```

1. 使用此查询记下新登录名的 SID： 

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>在主服务器上的源数据库上

1. 为同一个登录名创建一个用户：

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. 将用户添加到 db_owner 角色：

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>辅助服务器的主服务器上 

1. 将 IP 地址添加到执行更改的客户端允许列表中。 它必须与主服务器的 IP 地址完全相同。 
1. 使用相同的用户名密码和 SID，在主服务器上创建相同的登录名： 

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. 创建相应的用户并将其分配给 dbmanager 角色：

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>在主服务器的主服务器上

1. 使用新登录名登录到主服务器的主服务器。 
1. 在辅助服务器上创建源数据库的辅助副本（根据需要调整数据库名称和服务器名称）：

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

初始设置之后，可以删除创建的用户、登录名和防火墙规则。 


## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>保持凭据和防火墙规则同步

建议为异地复制的数据库使用[数据库级 IP 防火墙规则](sql-database-firewall-configure.md)，以便可以将这些规则与数据库一起复制，以确保所有辅助数据库具有与主数据库相同的 IP 防火墙规则。 此方法不再需要客户手动配置和维护承载主数据库和辅助数据库的服务器上的防火墙规则。 同样，将[包含的数据库用户](sql-database-manage-logins.md)用于数据访问可确保主数据库和辅助数据库始终具有相同的用户凭据，以便在故障转移期间，不会因登录名和密码不匹配而产生中断。 通过添加 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)，客户可以管理主数据库和辅助数据库的用户访问权限，且不再需要同时管理数据库中的凭据。

## <a name="upgrading-or-downgrading-primary-database"></a>升级或降级主数据库

无需断开连接任何辅助数据库，即可将主数据库升级或降级到不同的计算大小（在相同的服务层级中，但不在“常规用途”与“业务关键”类型之间）。 升级时，建议先升级辅助数据库，再升级主数据库。 降级时，应反转顺序：先降级主数据库，再降级辅助数据库。 将数据库升级或降级到不同服务层级时，将强制执行此建议操作。

> [!NOTE]
> 如果辅助数据库是作为故障转移组配置的一个部分创建的，则不建议对辅助数据库进行降级。 这是为了确保激活故障转移后，数据层有足够的容量来处理常规工作负荷。

> [!IMPORTANT]
> 故障转移组中的主数据库不能扩展到更高的层，除非第一次将辅助数据库缩放到更高的层。 如果在缩放辅助数据库之前尝试缩放主数据库，则可能会收到以下错误：
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>防止丢失关键数据

由于广域网的延迟时间较长，连续复制使用了异步复制机制。 在发生故障时，异步复制会不可避免地丢失某些数据。 但是，某些应用程序可能要求不能有数据丢失。 为了保护这些关键更新，应用程序开发人员可以在提交事务后立即调用 [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) 系统过程。 调用 **sp_wait_for_database_copy_sync** 会阻止调用线程，直到将上次提交的事务传输到辅助数据库。 但是，它不会等待传输的事务提交到辅助数据库进行重播。 **sp_wait_for_database_copy_sync** 的作用域是一个具体的连续复制链路。 对主数据库具有连接权限的任何用户都可以调用此过程。

> [!NOTE]
> sp_wait_for_database_copy_sync 将在故障转移后防止数据丢失，但它不会保证读取访问的完全同步。 **sp_wait_for_database_copy_sync** 过程调用导致的延迟可能很明显，具体取决于调用时的事务日志大小。

## <a name="monitoring-geo-replication-lag"></a>监视异地复制滞后时间

若要监视与 RPO 有关的 lag，请在主数据库上使用[sys. dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) *replication_lag_sec*列。 它显示在主副本上提交的事务与在辅助数据库上保留的事务之间的滞后时间（秒）。 例如 如果 lag 的值为1秒，则表示主副本受中断的影响，并且启动故障转移时，将不会保存1秒的最近转换。 

若要对主数据库上已应用于辅助数据库的更改（即可从辅助数据库进行读取）进行度量，请将辅助数据库上*last_commit*时间与主数据库上的相同值进行比较。

> [!NOTE]
> 有时，主数据库上*replication_lag_sec*的值为 NULL，这意味着主数据库当前不知道辅助数据库的距离。   这通常发生在进程重新启动之后，并且应为暂时性情况。 如果*replication_lag_sec*长时间返回空值，请考虑对应用程序发出警报。 这会指示由于永久性连接失败，辅助数据库无法与主数据库通信。 还可能会导致辅助数据库上的*last_commit*时间与主数据库上的时间差异变得很大。 例如 如果在较长一段时间后在主副本上进行提交，则在快速返回到0之前，该差异将跳到较大的值。 如果这两个值之间的差异在长时间内保持较大，请考虑使用错误情况。


## <a name="programmatically-managing-active-geo-replication"></a>以编程方式管理活动异地复制

如上所述，也可以使用 Azure PowerShell 和 REST API 以编程方式管理活动异地复制。 下表描述了可用的命令集。 活动异地复制包括一组用于管理的 Azure 资源管理器 API，其中包括 [Azure SQL 数据库 REST API](https://docs.microsoft.com/rest/api/sql/) 和 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)。 这些 API 需要使用资源组，并支持基于角色的安全性 (RBAC)。 有关如何实现访问角色的详细信息，请参阅 [Azure 基于角色的访问控制](../role-based-access-control/overview.md)。

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-sql：管理单个和共用数据库的故障转移

> [!IMPORTANT]
> 这些 Transact-SQL 命令仅适用于活动异地复制，不适用于故障转移组。 因此，它们也不适用于托管实例，因为它们仅支持故障转移组。

| Command | 说明 |
| --- | --- |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |使用 ADD SECONDARY ON SERVER 参数为现有数据库创建辅助数据库，并开始数据复制 |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |使用 FAILOVER 或 FORCE_FAILOVER_ALLOW_DATA_LOSS 将辅助数据库切换为主数据库，启动故障转移 |
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |使用 REMOVE SECONDARY ON SERVER 终止 SQL 数据库和指定的辅助数据库之间的数据复制。 |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |返回有关 Azure SQL 数据库服务器上每个数据库的所有现有复制链接的信息。 |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |获取有关给定 SQL 数据库的复制链路的上次复制时间、上次复制滞后时间和其他信息。 |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |显示所有数据库操作的状态，包括复制链路的状态。 |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |使应用程序等待，直到所有提交的事务已复制，并由活动辅助数据库确认。 |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell：管理单个和共用数据库的故障转移

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 数据库仍支持 PowerShell Azure 资源管理器模块，但所有将来的开发都适用于 Az .Sql 模块。 有关这些 cmdlet，请参阅[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令的参数完全相同。

| Cmdlet | 说明 |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |获取一个或多个数据库。 |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |为现有数据库创建辅助数据库，并开始数据复制。 |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |将辅助数据库切换为主数据库，启动故障转移。 |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |终止 SQL 数据库和指定的辅助数据库之间的数据复制。 |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |获取 Azure SQL 数据库和资源组或 SQL Server 之间的异地复制链路。 |
|  | |

> [!IMPORTANT]
> 有关示例脚本，请参阅[配置单一数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)和[配置共用数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)。

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API：管理单个和共用数据库的故障转移

| API | 说明 |
| --- | --- |
| [创建或更新数据库 (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |创建、更新或还原主数据库或辅助数据库。 |
| [获取创建或更新数据库状态](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |返回创建操作过程中的状态。 |
| [将辅助数据库设为主数据库（计划的故障转移）](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |通过来自当前主要数据库的故障转移设置辅助数据库。 **托管实例不支持此选项。**|
| [将辅助数据库设为主数据库（未计划的故障转移）](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |通过来自当前主要数据库的故障转移设置辅助数据库。 此操作可能导致数据丢失。 **托管实例不支持此选项。**|
| [获取复制链路](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |获取异地复制合作关系中给定 SQL 数据库的特定复制链路。 它检索 sys.geo_replication_links 目录视图中可见的信息。 **托管实例不支持此选项。**|
| [复制链接 - 按数据库列出](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | 获取异地复制合作关系中给定 SQL 数据库的所有复制链路。 它检索 sys.geo_replication_links 目录视图中可见的信息。 |
| [删除复制链接](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | 删除数据库复制链接。 无法在故障转移过程中完成。 |
|  | |

## <a name="next-steps"></a>后续步骤

- 示例脚本请参阅：
  - [配置单一数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [配置共用数据库并使用活动异地复制对其进行故障转移](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- SQL 数据库还支持自动故障转移组。 有关详细信息，请参阅[自动故障转移组](sql-database-auto-failover-group.md)。
- 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
- 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)。
- 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)。
- 若要了解新主服务器和数据库的身份验证要求，请参阅[灾难恢复后的 SQL 数据库安全性](sql-database-geo-replication-security-config.md)。
