---
title: "Azure SQL 数据同步最佳实践 | Microsoft 文档"
description: "了解有关配置和运行 Azure SQL 数据同步的最佳实践"
services: sql-database
ms.date: 11/2/2017
ms.topic: article
ms.service: sql-database
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 7492fffd1c18a149ef12174c79d64b47afbaa3e4
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2017
---
# <a name="best-practices-for-azure-sql-data-sync-preview"></a>Azure SQL 数据同步（预览版）最佳实践 

本文介绍了 SQL 数据同步（预览版）最佳实践。

有关 SQL 数据同步的概述，请参阅[使用 Azure SQL 数据同步（预览版）跨多个云和本地数据库同步数据](sql-database-sync-data.md)。

## <a name="security-and-reliability"></a>安全性和可靠性

### <a name="client-agent"></a>客户端代理

-   使用具有网络服务访问权限的最小特权帐户安装客户端代理。

-   最好在与本地 SQL Server 计算机不同的计算机上安装客户端代理。

-   请勿向多个代理注册本地数据库。

-   即使为不同的同步组同步不同表时也是如此。

-   如果向多个客户端代理注册本地数据库，在删除其中一个同步组时将会产生问题。

### <a name="database-accounts-with-least-required-privilege"></a>具有所需最小特权的数据库帐户

-   对于同步设置。 创建/更改表、更改数据库、创建过程、选择/更改架构、创建用户定义的类型。

-   对于正在进行的同步。在选择用于同步的表上以及同步元数据和跟踪表上选择/插入/更新/删除，在服务创建的存储过程上执行权限，在用户定义的表类型上执行权限。

-   对于取消预配。 在同步的表部分更改，在同步元数据表上选择/删除，在同步跟踪表、存储的过程和用户定义类型上控制。

**如果同步组中的数据库只有一个凭据，如何使用此信息？**

-   针对不同阶段更改凭据（例如 credential1 用于设置，credential2 用于正在进行）。

-   更改凭据的权限（即，在设置同步后更改权限）。

## <a name="locate-hub"></a> 在哪里定位中心数据库

### <a name="enterprise-to-cloud-scenario"></a>企业到云方案

为了最大程度的减小延迟，请使中心数据库位于同步组数据库流量最密集的位置。

### <a name="cloud-to-cloud-scenario"></a>云到云方案

-   如果同步组中的所有数据库位于一个数据中心，则中心数据库应位于同一个数据中心。 此配置会减少延迟并降低在数据中心之间的数据传输成本。

-   如果同步组中的数据库位于多个数据中心，则中心数据库应位于与大部分数据库和数据库流量相同的数据中心。

### <a name="mixed-scenarios"></a>混合方案

对更复杂的同步组配置应用上述指南。

## <a name="database-considerations-and-constraints"></a> 数据库考虑因素和约束

### <a name="sql-database-instance-size"></a>SQL 数据库实例大小

创建新 SQL 数据库实例时，请设置最大大小，以使其始终大于你部署的数据库。 如果未将最大大小设置为大于部署的数据库，则同步会失败。 虽然没有自动增长设置，但你可以在创建数据库后执行 ALTER DATABASE 来增加数据库大小。 请确保始终保持在 SQL 数据库实例大小限制之内。

> [!IMPORTANT]
> SQL 数据同步会为每个数据库存储额外的元数据。 请确保在计算所需的空间时考虑此元数据。 增加的开销量由表宽度（例如，窄表会需要更多的开销）和流量大小控制。

## <a name="table-considerations-and-constraints"></a> 表考虑因素和约束

### <a name="selecting-tables"></a>选择表

并不是数据库中的所有表都需要在一个[同步组](#sync-group)中。 选择在同步组中包括和排除哪些表（或哪些表包括在其他同步组中）都可能会影响效率和成本。 请在同步组中仅包括业务需求所需的表以及所依赖的表。

### <a name="primary-keys"></a>主键

同步组中的每个表均必须具有主键。 SQL 数据同步（预览版）服务无法同步没有主键的任何表。

在投入生产之前，请测试初始和正在进行的同步性能。

## <a name="provisioning-destination-databases"></a> 预配目标数据库

SQL 数据同步（预览版）提供了基本的数据库自动预配。

本部分讨论 SQL 数据同步（预览版）预配的限制。

### <a name="auto-provisioning-limitations"></a>自动预配限制

SQL 数据同步（预览版）自动预配的限制如下。

-   在目标表中仅创建所选的列。
因此，如果某些列不是同步组的一部分，则不会在目标表中预配这些列。

-   仅为所选列创建索引。
如果源表索引包含不是同步组一部分的列，则不会在目标表中预配这些索引。

-   不会预配 XML 类型列上的索引。

-   不会预配 CHECK 约束。

-   不会预配源表上的现有触发器。

-   不会在目标数据库上创建视图和存储的过程。

### <a name="recommendations"></a>建议

-   请仅在尝试服务时使用自动预配功能。

-   对于生产环境，应预配数据库架构。

## <a name="avoid-a-slow-and-costly-initial-synchronization"></a> 避免缓慢且昂贵的初始同步

本部分讨论了同步组的初始同步，以及你可以采取哪些措施来避免花费时间过长且成本高于正常值的初始同步。

### <a name="how-initial-synchronization-works"></a>初始同步的工作原理

创建同步组时，请先仅处理一个数据库中的数据。 如果数据在多个数据库中，SQL 数据同步（预览版）会将每一行都视为需要解决的冲突。 此冲突解决会导致初始同步变得缓慢，花费数天到数月时间才能完成，具体要取决于数据库大小。

此外，如果数据库位于不同的数据中心，初始同步的成本将高于必要值，因为每一行都必须在不同数据中心之间传输。

### <a name="recommendation"></a>建议

如果可能，请先仅处理同步组中的一个数据库的数据。

## <a name="design-to-avoid-synchronization-loops"></a> 设计以避免同步循环

如果同步组中有循环引用，则会引起同步循环，导致一个数据库中的每个更改都会在同步组中的数据库之间无限循环复制。 请避免同步循环，因为这会降低性能，并大大增加成本。

## <a name="avoid-out-of-date-databases-and-sync-groups"></a> 避免过时的数据库和同步组

同步组或同步组中的数据库可能会过时。 如果同步组的状态为“过时”，则会停止运行。 如果数据库的状态为“过时”，数据可能会丢失。 请尽力避免出现这些情况，以免要从这些情况中恢复。

### <a name="avoid-out-of-date-databases"></a>避免过时的数据库

如果数据库离线长达 45 天或更多，其状态将设置为“过时”。 通过确保没有任何数据库离线长达 45 天或更多，避免数据库出现过时状态。

### <a name="avoid-out-of-date-sync-groups"></a>避免过时的同步组

如果同步组中的任何更改在 45 天或更长时间内无法传播到同步组中的其他数据库，则同步组的状态将设置为“过时”。 通过定期检查同步组的历史记录日志，避免同步组出现过时状态。 确保所有冲突已解决，更改在同步组数据库之间成功传播。

同步组无法应用更改的原因包括：

-   表之间的架构不兼容。

-   表之间的数据不兼容。

-   将具有 NULL 值的行插入不允许 NULL 值的列中。

-   使用违反外键约束的值更新行。

你可以执行以下操作来避免同步组过时：

-   更新架构以允许失败的行中包含的值。

-   更新外键值以包括失败的行中包含的值。

-   更新失败的行中的数据值以与目标数据库的架构或外键兼容。

## <a name="avoid-deprovisioning-issues"></a> 避免取消预配问题

在某些情况下，向客户端代理取消注册数据库可能会导致同步失败。

### <a name="scenario"></a>方案

1. 使用 SQL 数据库实例和本地 SQL Server 数据库创建与本地代理 1 关联的同步组 A。

2. 向本地代理 2（此代理不与任何同步组关联）注册同一本地数据库。

3. 从本地代理 2 取消注册本地数据库将会删除本地数据库的同步组 A 的跟踪/元表。

4. 现在，同步组 A 操作失败，并显示以下错误：“当前操作无法完成，因为尚未为同步预配数据库，或者你没有访问同步配置表的权限。”

### <a name="solution"></a>解决方案

通过从不向多个代理注册数据库，可完全避免这种情况。

若要从此情况恢复，请执行以下操作：

1. 从数据库所属的每个同步组删除此数据库。

2. 将数据库重新添加回刚从中删除的每个同步组。

3. 部署每个受影响的同步组（其中预配了数据库）。

## <a name="handling-changes-that-fail-to-propagate"></a> 处理无法传播的更改

### <a name="reasons-that-changes-fail-to-propagate"></a>更改无法传播的原因

更改可能会因多种原因无法传播。 部分原因包括：

-   架构/数据类型不兼容。

-   尝试在不可为 NULL 的列中插入 NULL。

-   违反外键约束。

### <a name="what-happens-when-changes-fail-to-propagate"></a>如果更改无法传播，会发生什么情况？

-   同步组显示它处于警告状态。

-   详细信息显示在门户 UI 日志查看器中。

-   如果问题在 45 天未解决，数据库将过时。

> [!NOTE]
> 这些更改即不再传播。 恢复的唯一方法是重新创建同步组。

### <a name="recommendation"></a>建议

通过门户和日志界面定期监控同步组和数据库运行状况。

## <a name="modifying-your-sync-group"></a> 修改同步组

请勿尝试从同步组中删除数据库，然后在不先部署其中一个更改的情况下编辑同步组。

首先，从同步组中删除数据库。 然后，部署更改并等待取消预配完成。 此操作完成后，即可编辑同步组并部署更改。

如果你尝试删除数据库，然后编辑同步组而不先部署其中一个更改，则一个或另一个操作会失败，且门户界面可能会进入不一致状态。 在此情况下，你可以刷新页面以还原正确的状态。

## <a name="next-steps"></a>后续步骤
有关 SQL 数据同步的详细信息，请参阅：

-   [使用 Azure SQL 数据同步跨多个云和本地数据库同步数据](sql-database-sync-data.md)
-   [Azure SQL 数据同步入门](sql-database-get-started-sql-data-sync.md)
-   [Azure SQL 数据同步问题疑难解答](sql-database-troubleshoot-data-sync.md)

-   演示如何配置 SQL 数据同步的完整 PowerShell 示例：
    -   [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [下载 SQL 数据同步 REST API 文档](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

有关 SQL 数据库的详细信息，请参阅：

-   [SQL 数据库概述](sql-database-technical-overview.md)
-   [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)
