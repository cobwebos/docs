---
title: 将资源移到新区域
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 了解如何将数据库或托管实例移到另一个区域。
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: 0b78419f4fb37bb96e2c71c89f740a35914ccede
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446387"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>将资源移到新区域 - Azure SQL 数据库和 Azure SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文介绍讲解将数据库或托管实例迁移到新区域的一般工作流。

## <a name="overview"></a>概述

在多种情况下，可能需要将现有数据库或托管实例从一个区域移到另一个区域。 例如，你要将业务拓展到新的区域，并希望根据新的客户群对业务进行优化。 或者出于合规性原因，需要将运营部门转移到另一个区域。 或者，Azure 开通了距离更近、可改善客户体验的新区域。  

本文提供将资源移到不同区域的一般工作流。 工作流包括以下步骤：

1. 检查是否符合移动的先决条件。
1. 准备在范围内移动资源。
1. 监视准备过程。
1. 测试移动过程。
1. 启动实际移动。
1. 删除源区域中的资源。

> [!NOTE]
> 本文适用于 Azure 公有云内或同一主权云中的迁移。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>移动数据库

### <a name="verify-prerequisites"></a>验证先决条件

1. 为每个源服务器创建目标服务器。
1. 使用 [PowerShell](scripts/create-and-configure-database-powershell.md) 配置包含适当例外项的防火墙。  
1. 使用适当的登录名配置服务器。 如果你不是订阅管理员或 SQL 服务器管理员，请让管理员为你分配所需的权限。 有关详细信息，请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](active-geo-replication-security-configure.md)。
1. 如果数据库已通过透明数据加密进行加密并使用 Azure Key Vault 中你自己的加密密钥，请确保在目标区域中预配正确的加密材料。 有关详细信息，请参阅[使用 Azure Key Vault 中由客户管理的密钥进行 Azure SQL 透明数据加密](transparent-data-encryption-byok-overview.md)。
1. 如果启用了数据库级审核，请将其禁用，并改为启用服务器级审核。 故障转移后，数据库级审核需要跨区域的流量，而在移动后，这种情况是不适当的或者无法实现的。
1. 对于服务器级审核，请确保：
   - 已将包含现有审核日志的存储容器、Log Analytics 或事件中心移到目标区域。
   - 已在目标服务器上配置审核。 有关详细信息，请参阅 [SQL 数据库审核入门](../../azure-sql/database/auditing-overview.md)。
1. 如果实例具有长期保留策略 (LTR)，则现有的 LTR 备份将与当前服务器保持关联。 由于目标服务器不同，因此可以使用源服务器访问源区域中的旧 LTR 备份，即使删除了该服务器，也是如此。

      > [!NOTE]
      > 这将无法在主权云和公共区域之间移动。 此类迁移需要将 LTR 备份移动到当前不支持的目标服务器。

### <a name="prepare-resources"></a>准备资源

1. 在源的服务器与目标的服务器之间创建一个[故障转移组](failover-group-add-single-database-tutorial.md#2---create-the-failover-group)。  
1. 将要移动的数据库添加到该故障转移组中。
  
    系统会自动启动所有已添加的数据库的复制。 有关详细信息，请参阅[有关对单一数据库使用故障转移组的最佳做法](auto-failover-group-overview.md#best-practices-for-sql-database)。

### <a name="monitor-the-preparation-process"></a>监视准备过程

可以定期调用 [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) 来监视数据库从源到目标的复制。 `Get-AzSqlDatabaseFailoverGroup` 的输出对象包含 **ReplicationState** 的属性：

- **ReplicationState = 2** (CATCH_UP) 表示数据库已同步，可以安全故障转移。
- **ReplicationState = 0** (SEEDING) 表示数据库尚未设定种子，尝试故障转移会失败。

### <a name="test-synchronization"></a>测试同步

“ReplicationState”变为 `2` 后，使用辅助终结点 `<fog-name>.secondary.database.windows.net` 连接到每个数据库或数据库子集，并对数据库执行任何查询，以确保连接、安全性配置和数据复制正常。

### <a name="initiate-the-move"></a>启动移动

1. 使用辅助终结点 `<fog-name>.secondary.database.windows.net` 连接到目标服务器。
1. 在完全同步的情况下，使用 [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) 将辅助托管实例切换为主托管实例。 此操作将成功或者回滚。
1. 使用 `nslook up <fog-name>.secondary.database.windows.net` 验证该命令是否已成功完成，确定 DNS CNAME 条目指向目标区域的 IP 地址。 如果 switch 命令失败，不会更新 CNAME。

### <a name="remove-the-source-databases"></a>删除源数据库

移动操作完成后，删除源区域中的资源以避免产生不必要的费用。

1. 使用 [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) 删除故障转移组。
1. 使用 [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) 删除源服务器上每个数据库的每个源数据库。 这会自动终止异地复制链接。
1. 使用 [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver) 删除源服务器。
1. 删除密钥保管库、审核存储容器、事件中心、Azure Active Directory (Azure AD) 实例和其他相关资源，以停止其计费。

## <a name="move-elastic-pools"></a>移动弹性池

### <a name="verify-prerequisites"></a>验证先决条件

1. 为每个源服务器创建目标服务器。
1. 使用 [PowerShell](scripts/create-and-configure-database-powershell.md) 配置包含适当例外项的防火墙。
1. 使用适当的登录名配置服务器。 如果你不是订阅管理员或服务器管理员，请让管理员为你分配所需的权限。 有关详细信息，请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](active-geo-replication-security-configure.md)。
1. 如果数据库已通过透明数据加密进行加密并使用 Azure Key Vault 中你自己的加密密钥，请确保在目标区域中预配正确的加密材料。
1. 为每个源弹性池创建一个目标弹性池，并确保在同一个服务层级中创建具有相同名称和相同大小的池。
1. 如果启用了数据库级审核，请将其禁用，并改为启用服务器级审核。 故障转移后，数据库级审核需要跨区域的流量，而在移动后，这种情况是不适当的或者无法实现的。
1. 对于服务器级审核，请确保：
    - 已将包含现有审核日志的存储容器、Log Analytics 或事件中心移到目标区域。
    - 已在目标服务器上配置审核。 有关详细信息，请参阅 [SQL 数据库审核](../../azure-sql/database/auditing-overview.md)。
1. 如果实例具有长期保留策略 (LTR)，则现有的 LTR 备份将与当前服务器保持关联。 由于目标服务器不同，因此你可以使用源服务器访问源区域中的旧 LTR 备份，即使删除了该服务器，也是如此。

      > [!NOTE]
      > 这将无法在主权云和公共区域之间移动。 此类迁移需要将 LTR 备份移动到当前不支持的目标服务器。

### <a name="prepare-to-move"></a>准备移动

1. 在源服务器上的每个弹性池及其在目标服务器上的对应弹性池之间单独创建一个[故障转移组](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group)。
1. 将池中的所有数据库添加到该故障转移组中。

    系统会自动启动已添加的数据库的复制。 有关详细信息，请参阅[有关包含弹性池的故障转移组的最佳做法](auto-failover-group-overview.md#best-practices-for-sql-database)。

      > [!NOTE]
      > 尽管可以创建包含多个弹性池的故障转移组，但我们强烈建议为每个池单独创建一个故障转移组。 如果需要在多个弹性池之间移动大量的数据库，可以同时运行准备步骤，并同时启动移动步骤。 与在同一故障转移组中包含多个弹性池相比，此过程的伸缩性更好，并且所需的时间更少。

### <a name="monitor-the-preparation-process"></a>监视准备过程

可以定期调用 [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) 来监视数据库从源到目标的复制。 `Get-AzSqlDatabaseFailoverGroup` 的输出对象包含 **ReplicationState** 的属性：

- **ReplicationState = 2** (CATCH_UP) 表示数据库已同步，可以安全故障转移。
- **ReplicationState = 0** (SEEDING) 表示数据库尚未设定种子，尝试故障转移会失败。

### <a name="test-synchronization"></a>测试同步

“ReplicationState”变为 `2` 后，使用辅助终结点 `<fog-name>.secondary.database.windows.net` 连接到每个数据库或数据库子集，并对数据库执行任何查询，以确保连接、安全性配置和数据复制正常。

### <a name="initiate-the-move"></a>启动移动

1. 使用辅助终结点 `<fog-name>.secondary.database.windows.net` 连接到目标服务器。
1. 在完全同步的情况下，使用 [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) 将辅助托管实例切换为主托管实例。 此操作要么成功，要么回滚。
1. 使用 `nslook up <fog-name>.secondary.database.windows.net` 验证该命令是否已成功完成，确定 DNS CNAME 条目指向目标区域的 IP 地址。 如果 switch 命令失败，不会更新 CNAME。

### <a name="remove-the-source-elastic-pools"></a>删除源弹性池

移动操作完成后，删除源区域中的资源以避免产生不必要的费用。

1. 使用 [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) 删除故障转移组。
1. 使用 [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool) 删除源服务器上的每个源弹性池。
1. 使用 [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver) 删除源服务器。
1. 删除 Key Vault、审核存储容器、事件中心、Azure AD 实例和其他相关资源，以停止其计费。

## <a name="move-a-managed-instance"></a>移动托管实例

### <a name="verify-prerequisites"></a>验证先决条件

1. 对于每个源托管实例，请在目标区域中创建一个具有相同大小的 SQL 托管实例的目标实例。  
1. 为托管实例配置网络。 有关详细信息，请参阅[网络配置](../managed-instance/how-to-content-reference-guide.md#network-configuration)。
1. 使用正确的登录名配置目标 master 数据库。 如果你不是订阅管理员或 Azure SQL 托管实例管理员，请要求管理员分配你所需的权限。
1. 如果数据库已通过透明数据加密进行加密并使用 Azure Key Vault 中你自己的加密密钥，请确保源区域和目标区域中存在具有相同加密密钥的 Azure Key Vault。 有关详细信息，请参阅[使用 Azure Key Vault 中由客户管理的密钥进行透明数据加密](transparent-data-encryption-byok-overview.md)。
1. 如果为托管实例启用了审核，请确保：
    - 已将包含现有日志的存储容器或事件中心移到目标区域。
    - 已在目标实例上配置审核。 有关详细信息，请参阅 [SQL 托管实例的审核](../managed-instance/auditing-configure.md)。
1. 如果实例具有长期保留策略 (LTR)，则现有的 LTR 备份将与当前实例保持关联。 由于目标实例不同，因此可以使用源实例访问源区域中的旧 LTR 备份，即使删除了该实例，也可以做到。

  > [!NOTE]
  > 这将无法在主权云和公共区域之间移动。 此类迁移需要将 LTR 备份移动到目标实例，目前不支持该实例。

### <a name="prepare-resources"></a>准备资源

在每个源托管实例与相应的 Azure SQL 托管实例的目标实例之间创建故障转移组。

系统会自动启动每个实例上所有数据库的复制。 有关详细信息，请参阅[自动故障转移组](auto-failover-group-overview.md)。

### <a name="monitor-the-preparation-process"></a>监视准备过程

可以定期调用 [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) 来监视数据库从源到目标的复制。 `Get-AzSqlDatabaseFailoverGroup` 的输出对象包含 **ReplicationState** 的属性：

- **ReplicationState = 2** (CATCH_UP) 表示数据库已同步，可以安全故障转移。
- **ReplicationState = 0** (SEEDING) 表示数据库尚未设定种子，尝试故障转移会失败。

### <a name="test-synchronization"></a>测试同步

**ReplicationState** 变为 `2` 后，使用辅助终结点 `<fog-name>.secondary.database.windows.net` 连接到每个数据库或数据库子集，并对数据库执行任何查询，以确保连接、安全性配置和数据复制正常。

### <a name="initiate-the-move"></a>启动移动

1. 使用辅助终结点 `<fog-name>.secondary.database.windows.net` 连接到目标托管实例。
1. 在完全同步的情况下，使用 [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) 将辅助托管实例切换为主托管实例。 此操作将成功或者回滚。
1. 使用 `nslook up <fog-name>.secondary.database.windows.net` 验证该命令是否已成功完成，确定 DNS CNAME 条目指向目标区域的 IP 地址。 如果 switch 命令失败，不会更新 CNAME。

### <a name="remove-the-source-managed-instances"></a>删除源托管实例

移动操作完成后，删除源区域中的资源以避免产生不必要的费用。

1. 使用 [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) 删除故障转移组。 这会删除故障转移组配置，并终止两个实例之间的异地复制链接。
1. 使用 [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance) 删除源托管实例。
1. 删除资源组中的任何其他资源，例如虚拟群集、虚拟网络和安全组。

## <a name="next-steps"></a>后续步骤

[在迁移数据库后管理数据库](manage-data-after-migrating-to-database.md)。
