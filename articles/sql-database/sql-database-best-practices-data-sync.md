---
title: Azure SQL 数据同步最佳做法 | Microsoft Docs
description: 了解有关配置和运行 Azure SQL 数据同步的最佳做法。
services: sql-database
ms.date: 04/01/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
manager: craigg
ms.openlocfilehash: b53c72f1df4f2fc2509d91220d08aff4682b6620
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025113"
---
# <a name="best-practices-for-sql-data-sync"></a>SQL 数据同步最佳做法 

本文介绍了针对 Azure SQL 数据同步的最佳做法。

有关 SQL 数据同步的概述，请参阅[使用 Azure SQL 数据同步跨多个云和本地数据库同步数据](sql-database-sync-data.md)。

## <a name="security-and-reliability"></a>安全性和可靠性

### <a name="client-agent"></a>客户端代理

-   使用具有网络服务访问权限的最小特权用户帐户安装客户端代理。  
-   在不是本地 SQL Server 计算机的计算机上安装客户端代理。  
-   请勿向多个代理注册本地数据库。    
    -   即使为不同的同步组同步不同表时也应避免这样做。  
    -   如果向多个客户端代理注册本地数据库，则会在删除其中一个同步组时产生问题。

### <a name="database-accounts-with-least-required-privileges"></a>具有所需最小特权的数据库帐户

-   对于同步设置。 创建/更改表、更改数据库、创建过程、选择/更改架构、创建用户定义的类型。

-   对于正在进行的同步。在选择用于同步的表上以及同步元数据和跟踪表上为“选择/插入/更新/删除”权限，在服务创建的存储过程上为“执行”权限，在用户定义的表类型上为“执行”权限。

-   对于取消预配。 在同步的表部分为“更改”权限，在同步元数据表上为“选择/删除”权限，在同步跟踪表、存储的过程和用户定义类型上为“控制”权限。

Azure SQL 数据库仅支持单组凭据。 若要在此约束内完成这些任务，请考虑使用以下选项：

-   针对不同阶段更改凭据（例如 credential1 用于安装，credential2 用于正在运行）。  
-   更改凭据的权限（即，在设置同步后更改权限）。

## <a name="setup"></a>设置

### <a name="database-considerations-and-constraints"></a> 数据库考虑因素和约束

#### <a name="sql-database-instance-size"></a>SQL 数据库实例大小

创建新的 SQL 数据库实例时，请设置最大大小，以使其始终大于你部署的数据库。 如果未将最大大小设置为大于部署的数据库，则同步会失败。 尽管 SQL 数据同步不提供自动增长，但可以在创建数据库后运行 `ALTER DATABASE` 命令来增加数据库的大小。 请确保始终保持在 SQL 数据库实例大小限制之内。

> [!IMPORTANT]
> SQL 数据同步会为每个数据库存储额外的元数据。 请确保在计算所需的空间时考虑此元数据。 增加的开销量与表宽度（例如，窄表会需要更多的开销）和流量大小有关。

### <a name="table-considerations-and-constraints"></a> 表考虑因素和约束

#### <a name="selecting-tables"></a>选择表

无需在同步组中包括数据库中的所有表。 同步组中所包括的表会影响效率和成本。 仅当业务需要时才在同步组中包括表及其依赖的表。

#### <a name="primary-keys"></a>主键

同步组中的每个表均必须具有主键。 SQL 数据同步服务无法同步没有主键的表。

在生产中使用 SQL 数据同步之前，请测试初始和正在进行的同步性能。

### <a name="provisioning-destination-databases"></a> 预配目标数据库

SQL 数据同步提供了基本的数据库自动预配。

本部分讨论 SQL 数据同步预配的限制。

#### <a name="autoprovisioning-limitations"></a>自动预配限制

SQL 数据同步自动预配的限制如下：

-   在目标表中仅选择已创建的列。  
    在目标表中，不会对不属于同步组一部分的任何列进行预配。
-   仅为所选列创建索引。  
    如果源表索引包含不是同步组一部分的列，则不会在目标表中预配这些索引。  
-   不会预配 XML 类型列上的索引。  
-   不会预配 CHECK 约束。  
-   不会预配源表上的现有触发器。  
-   不会在目标数据库上创建视图和存储的过程。

#### <a name="recommendations"></a>建议

-   仅在尝试使用该服务时使用 SQL 数据同步自动预配功能。  
-   对于生产环境，应预配数据库架构。

### <a name="locate-hub"></a> 在哪里定位中心数据库

#### <a name="enterprise-to-cloud-scenario"></a>企业到云方案

为了最大程度的减小延迟，请使中心数据库位于同步组数据库流量最密集的位置。

#### <a name="cloud-to-cloud-scenario"></a>云到云方案

-   如果同步组中的所有数据库位于一个数据中心，则中心数据库应位于同一个数据中心。 此配置会减少延迟并降低在数据中心之间的数据传输成本。
-   如果同步组中的数据库位于多个数据中心，则中心数据库应位于与大部分数据库和数据库流量相同的数据中心。

#### <a name="mixed-scenarios"></a>混合方案

将前面的准则应用于复杂的同步组配置，例如，应用于企业到云和云到云方案的组合。

## <a name="sync"></a>同步

### <a name="avoid-a-slow-and-costly-initial-synchronization"></a> 避免缓慢且昂贵的初始同步

在本部分，我们将讨论同步组的初始同步。 了解如何帮助防止初始同步运行时间过长而产生不必要的昂贵开销。

#### <a name="how-initial-sync-works"></a>初始同步工作原理

创建同步组时，请先仅处理一个数据库中的数据。 如果数据在多个数据库中，SQL 数据同步会将每一行都视为需要解决的冲突。 此冲突解决会导致初始同步运行缓慢。 如果数据在多个数据库中，则初始同步可能需要几天到几个月的时间，具体要取决于数据库大小。

如果数据库在不同的数据中心，每行都必须在不同的数据中心之间穿行。 这也增加了初始同步的成本。

#### <a name="recommendation"></a>建议

如果可能，请先仅处理同步组中一个数据库的数据。

### <a name="design-to-avoid-synchronization-loops"></a> 进行设计以避免同步循环

在同步组中存在循环引用时将出现同步循环。 在此情况下，一个数据库中的每个更改都将通过同步组中的数据库进行循环无休止地复制。   

请确保避免同步循环，因为它们会导致性能降低并可能显著增加成本。

### <a name="handling-changes-that-fail-to-propagate"></a> 无法传播的更改

#### <a name="reasons-that-changes-fail-to-propagate"></a>更改无法传播的原因

更改可能由于以下原因之一而无法传播：

-   架构/数据类型不兼容。
-   在不可为 NULL 的列中插入 NULL。
-   违反外键约束。

#### <a name="what-happens-when-changes-fail-to-propagate"></a>如果更改无法传播，会发生什么情况？

-   同步组显示它处于警告状态。
-   详细信息显示在门户 UI 日志查看器中。
-   如果问题在 45 天未解决，数据库将过时。

> [!NOTE]
> 这些更改即不再传播。 在此情况下恢复的唯一方法是重新创建同步组。

#### <a name="recommendation"></a>建议

通过门户和日志界面定期监控同步组和数据库运行状况。


## <a name="maintenance"></a>维护

### <a name="avoid-out-of-date-databases-and-sync-groups"></a> 避免过时的数据库和同步组

同步组或同步组中的数据库可能会过时。 如果同步组的状态为“过时”，则会停止运行。 如果数据库的状态为“过时”，数据可能会丢失。 最好避免这种情况，而不是尝试基于其还原。

#### <a name="avoid-out-of-date-databases"></a>避免过时的数据库

如果数据库离线长达 45 天或更多，其状态将设置为“过时”。 请确保没有任何数据库离线长达 45 天或更多，以避免数据库出现“过时”状态。

#### <a name="avoid-out-of-date-sync-groups"></a>避免过时的同步组

如果同步组中的任何更改在 45 天或更长时间内无法传播到同步组中的其他数据库，则同步组的状态将设置为“过时”。 请定期检查同步组的历史记录日志，以避免同步组出现“过时”状态。 确保所有冲突已解决，且更改在同步组数据库之间成功传播。

同步组可能会由于下列原因之一无法应用更改：

-   表之间的架构不兼容。
-   表之间的数据不兼容。
-   将具有 NULL 值的行插入不允许 NULL 值的列中。
-   使用违反外键约束的值更新行。

若要避免同步组过时：

-   更新架构以允许失败的行中包含的值。
-   更新外键值以包括失败的行中包含的值。
-   更新失败的行中的数据值，以使其与目标数据库中的架构或外键兼容。

### <a name="avoid-deprovisioning-issues"></a> 避免取消预配问题

在某些情况下，向客户端代理取消注册数据库可能会导致同步失败。

#### <a name="scenario"></a>场景

1. 使用 SQL 数据库实例和本地 SQL Server 数据库创建与本地代理 1 关联的同步组 A。
2. 向本地代理 2（此代理不与任何同步组关联）注册同一本地数据库。
3. 从本地代理 2 取消注册本地数据库将会删除本地数据库的同步组 A 的跟踪和元表。
4. 同步组 A 操作失败，并显示以下错误：“当前操作无法完成，因为尚未预配进行同步的数据库，或者你没有访问同步配置表的权限。”

#### <a name="solution"></a>解决方案

若要避免此情况，请不要向多个代理注册数据库。

若要从此情况恢复：

1. 从数据库所属的每个同步组删除此数据库。  
2. 将数据库重新添加回从中删除的每个同步组。  
3. 部署每个受影响的同步组（此操作预配了数据库）。  

### <a name="modifying-your-sync-group"></a> 修改同步组

请勿尝试先从同步组中删除数据库，然后在不先部署其中一个更改的情况下编辑同步组。

而是首先从同步组中删除数据库。 然后，部署更改并等待取消预配完成。 取消预配完成后，可以编辑同步组并部署更改。

如果你尝试先删除数据库，然后编辑同步组而不先部署其中一个更改，则一个或另一个操作会失败。 门户界面可能会出现不一致状态。 如果出现此情况，请刷新页面以还原正确的状态。

## <a name="next-steps"></a>后续步骤
有关 SQL 数据同步的详细信息，请参阅：

-   [使用 Azure SQL 数据同步跨多个云和本地数据库同步数据](sql-database-sync-data.md)
-   [设置 Azure SQL 数据同步](sql-database-get-started-sql-data-sync.md)
-   [使用 Log Analytics 监视 Azure SQL 数据同步](sql-database-sync-monitor-oms.md)
-   [Azure SQL 数据同步问题疑难解答](sql-database-troubleshoot-data-sync.md)  
-   演示如何配置 SQL 数据同步的完整 PowerShell 示例：  
    -   [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](scripts/sql-database-sync-data-between-sql-databases.md)  
    -   [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](scripts/sql-database-sync-data-between-azure-onprem.md)  
-   [下载 SQL 数据同步 REST API 文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)  

有关 SQL 数据库的详细信息，请参阅：

-   [SQL 数据库概述](sql-database-technical-overview.md)
-   [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)
