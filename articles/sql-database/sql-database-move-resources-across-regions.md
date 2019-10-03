---
title: 如何将 Azure SQL 数据库资源移动到另一个区域 |Microsoft Docs
description: 了解如何将 Azure SQL 数据库、Azure SQL 弹性池或 Azure SQL 托管实例移到另一个区域。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 2158d4120445de4c62461fb89555a1b73bc1e2b4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567168"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>如何将 Azure SQL 资源移到另一个区域

本文介绍如何将 Azure SQL 数据库单一数据库、弹性池和托管实例迁移到新区域的一般工作流。 

## <a name="overview"></a>概述

在各种情况下, 你想要将现有的 Azure SQL 资源从一个区域移到另一个区域。 例如, 你将业务扩展到新区域, 并想要针对新的客户群优化该区域。 或者出于合规性原因, 需要将操作移动到另一个区域。 或 Azure 发布了一个全新的区域, 该区域提供更好的邻近性并改善客户体验。  

本文提供了将资源移到不同区域的常规工作流。 工作流包括以下步骤: 

- 验证迁移的先决条件 
- 准备移动范围内的资源
- 监视准备过程
- 测试移动过程
- 启动实际移动 
- 从源区域中删除资源 


> [!NOTE]
> 本文适用于 Azure 公有云内或同一主权云中的迁移。 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>移动单个数据库

### <a name="verify-prerequisites"></a>验证先决条件 

1. 为每个源服务器创建目标逻辑服务器。 
1. 使用[PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)将防火墙配置为使用正确的例外。  
1. 用正确的登录名配置逻辑服务器。 如果你不是订阅管理员或 SQL server 管理员, 请与管理员合作, 以分配所需的权限。 有关详细信息, 请参阅[灾难恢复后如何管理 AZURE SQL 数据库安全性](sql-database-geo-replication-security-config.md)。 
1. 如果使用 TDE 对数据库进行加密, 并在 Azure 密钥保管库中使用自己的加密密钥, 请确保在目标区域中预配正确的加密材料。 有关详细信息, 请参阅[AZURE SQL 透明数据加密与客户托管的密钥 Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. 如果启用了数据库级审核, 请将其禁用, 并改为启用服务器级审核。 故障转移后, 数据库级审核需要跨区域流量, 这在移动后将不需要也不可能。 
1. 对于服务器级审核, 请确保:
   - 具有现有审核日志的存储容器、Log Analytics 或事件中心会移动到目标区域。 
   - 在目标服务器上配置了审核。 有关详细信息，请参阅 [SQL 数据库审核入门](sql-database-auditing.md)。 
1. 如果实例具有长期保留策略 (LTR), 则现有的 LTR 备份仍将与当前服务器关联。 由于目标服务器不同, 因此你将能够使用源服务器访问源区域中的较旧的 LTR 备份, 即使已删除服务器也是如此。 

  > [!NOTE]
  > 这将无法在主权云和公共区域之间移动。 此类迁移需要将 LTR 备份移动到当前不支持的目标服务器。 

### <a name="prepare-resources"></a>准备资源

1. 在源的逻辑服务器与目标的逻辑服务器之间创建[故障转移组](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group)。  
1. 将要移动的数据库添加到故障转移组。 
    - 将自动启动所有添加的数据库的复制。 有关详细信息, 请参阅将[故障转移组与单一数据库配合使用的最佳做法](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)。 
 
### <a name="monitor-the-preparation-process"></a>监视准备过程

可以定期调用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup)来监视从源到目标的数据库的复制。 的输出对象`Get-AzSqlDatabaseFailoverGroup`包含**ReplicationState**的属性: 
   - **ReplicationState = 2**(CATCH_UP) 指示数据库已同步, 可以安全地进行故障转移。 
   - **ReplicationState = 0**(种子设定) 指示数据库尚未设定种子, 尝试故障转移失败。 

### <a name="test-synchronization"></a>测试同步

**ReplicationState**为`2`后, 连接到每个数据库或使用辅助终结点`<fog-name>.secondary.database.windows.net`的数据库子集, 并对数据库执行任何查询, 以确保连接性、适当的安全配置和数据副本. 

### <a name="initiate-the-move"></a>启动移动

1. 使用辅助终结点`<fog-name>.secondary.database.windows.net`连接到目标服务器。
1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)将辅助托管实例切换为具有完全同步的主实例。 此操作要么会成功, 要么会回滚。 
1. 验证该命令是否已成功完成, 方法`nslook up <fog-name>.secondary.database.windows.net`是使用确定 DNS CNAME 条目是否指向目标区域的 IP 地址。 如果 switch 命令失败, 则不会更新 CNAME。 

### <a name="remove-the-source-databases"></a>删除源数据库

移动完成后, 请删除源区域中的资源, 以避免产生不必要的费用。 

1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)删除故障转移组。 
1. 使用[AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)为源服务器上的每个数据库删除每个源数据库。 这会自动终止异地复制链接。 
1. 使用[AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)删除源服务器。 
1. 删除密钥保管库、审核存储容器、事件中心、AAD 实例以及其他相关资源, 以停止对这些资源进行计费。 

## <a name="move-elastic-pools"></a>移动弹性池

### <a name="verify-prerequisites"></a>验证先决条件 

1. 为每个源服务器创建目标逻辑服务器。 
1. 使用[PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)将防火墙配置为使用正确的例外。 
1. 用正确的登录名配置逻辑服务器。 如果你不是订阅管理员或 SQL server 管理员, 请与管理员合作, 以分配所需的权限。 有关详细信息, 请参阅[灾难恢复后如何管理 AZURE SQL 数据库安全性](sql-database-geo-replication-security-config.md)。 
1. 如果使用 TDE 对数据库进行加密, 并在 Azure 密钥保管库中使用自己的加密密钥, 请确保在目标区域中预配正确的加密材料。
1. 为每个源弹性池创建目标弹性池, 并确保在相同的服务层中创建具有相同名称和相同大小的池。 
1. 如果启用了数据库级审核, 请将其禁用并改为启用服务器级审核。 故障转移后, 数据库级审核需要跨区域流量, 这是不需要的, 也可能是移动后的可能。 
1. 对于服务器级审核, 请确保:
    - 具有现有审核日志的存储容器、Log Analytics 或事件中心会移动到目标区域。
    - 审核配置是在目标服务器上配置的。 有关详细信息, 请参阅[SQL 数据库审核](sql-database-auditing.md)。
1. 如果实例具有长期保留策略 (LTR), 则现有的 LTR 备份仍将与当前服务器关联。 由于目标服务器不同, 因此你将能够使用源服务器访问源区域中的较旧的 LTR 备份, 即使已删除服务器也是如此。 

  > [!NOTE]
  > 这将无法在主权云和公共区域之间移动。 此类迁移需要将 LTR 备份移动到当前不支持的目标服务器。 

### <a name="prepare-to-move"></a>准备移动
 
1.  在源逻辑服务器上的每个弹性池与目标服务器上的对应弹性池之间创建单独的[故障转移组](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group)。 
1.  将池中的所有数据库添加到故障转移组。 
    - 将自动启动已添加数据库的复制。 有关详细信息, 请参阅[包含弹性池的故障转移组的最佳实践](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools)。 

  > [!NOTE]
  > 尽管可以创建包含多个弹性池的故障转移组, 但我们强烈建议为每个池创建一个单独的故障转移组。 如果需要移动的多个弹性池有大量数据库, 可以并行运行准备步骤, 并并行启动移动步骤。 与在同一故障转移组中具有多个弹性池相比, 此过程将会更好地扩展, 并且需要更少的时间。 

### <a name="monitor-the-preparation-process"></a>监视准备过程

可以定期调用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup)来监视从源到目标的数据库的复制。 的输出对象`Get-AzSqlDatabaseFailoverGroup`包含**ReplicationState**的属性: 
   - **ReplicationState = 2**(CATCH_UP) 指示数据库已同步, 可以安全地进行故障转移。 
   - **ReplicationState = 0**(种子设定) 指示数据库尚未设定种子, 尝试故障转移失败。 

### <a name="test-synchronization"></a>测试同步
 
**ReplicationState**为`2`后, 连接到每个数据库或使用辅助终结点`<fog-name>.secondary.database.windows.net`的数据库子集, 并对数据库执行任何查询, 以确保连接性、适当的安全配置和数据副本. 

### <a name="initiate-the-move"></a>启动移动
 
1. 使用辅助终结点`<fog-name>.secondary.database.windows.net`连接到目标服务器。
1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)将辅助托管实例切换为具有完全同步的主实例。 此操作要么会成功, 要么会回滚。 
1. 验证该命令是否已成功完成, 方法`nslook up <fog-name>.secondary.database.windows.net`是使用确定 DNS CNAME 条目是否指向目标区域的 IP 地址。 如果 switch 命令失败, 则不会更新 CNAME。 

### <a name="remove-the-source-elastic-pools"></a>删除源弹性池
 
移动完成后, 请删除源区域中的资源, 以避免产生不必要的费用。 

1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)删除故障转移组。
1. 使用[AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)删除源服务器上的每个源弹性池。 
1. 使用[AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)删除源服务器。 
1. 删除密钥保管库、审核存储容器、事件中心、AAD 实例以及其他相关资源, 以停止对这些资源进行计费。 

## <a name="move-managed-instance"></a>移动托管实例

### <a name="verify-prerequisites"></a>验证先决条件
 
1. 对于每个源托管实例, 在目标区域中创建一个相同大小的目标托管实例。  
1. 为托管实例配置网络。 有关详细信息, 请参阅[网络配置](sql-database-howto-managed-instance.md#network-configuration)。
1. 用正确的登录名配置目标 master 数据库。 如果你不是订阅管理员或 SQL server 管理员, 请与管理员合作, 以分配所需的权限。 
1. 如果使用 TDE 对数据库进行加密, 并在 Azure 密钥保管库中使用自己的加密密钥, 请确保源区域和目标区域中都存在具有相同加密密钥的 AKV。 有关详细信息, 请参阅[Azure Key Vault 中的 TDE 与客户管理的密钥](transparent-data-encryption-byok-azure-sql.md)。
1. 如果为实例启用了审核, 请确保:
    - 具有现有日志的存储容器或事件中心将被移动到目标区域。 
    - 在目标实例上配置了审核。 有关详细信息, 请参阅[与托管实例的审核](sql-database-managed-instance-auditing.md)。
1. 如果实例具有长期保留策略 (LTR), 则现有的 LTR 备份仍将与当前服务器关联。 由于目标服务器不同, 因此你将能够使用源服务器访问源区域中的较旧的 LTR 备份, 即使已删除服务器也是如此。 

  > [!NOTE]
  > 这将无法在主权云和公共区域之间移动。 此类迁移需要将 LTR 备份移动到当前不支持的目标服务器。 

### <a name="prepare-resources"></a>准备资源

在每个源实例与相应的目标实例之间创建故障转移组。
    - 将自动启动每个实例上的所有数据库的复制。 有关详细信息, 请参阅[自动故障转移组](sql-database-auto-failover-group.md)。

 
### <a name="monitor-the-preparation-process"></a>监视准备过程

可以定期调用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2)来监视从源到目标的数据库的复制。 的输出对象`Get-AzSqlDatabaseFailoverGroup`包含**ReplicationState**的属性: 
   - **ReplicationState = 2**(CATCH_UP) 指示数据库已同步, 可以安全地进行故障转移。 
   - **ReplicationState = 0**(种子设定) 指示数据库尚未设定种子, 尝试故障转移失败。 

### <a name="test-synchronization"></a>测试同步

**ReplicationState**为`2`后, 连接到每个数据库或使用辅助终结点`<fog-name>.secondary.database.windows.net`的数据库子集, 并对数据库执行任何查询, 以确保连接性、适当的安全配置和数据副本. 

### <a name="initiate-the-move"></a>启动移动 

1. 使用辅助终结点`<fog-name>.secondary.database.windows.net`连接到目标服务器。
1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2)将辅助托管实例切换为具有完全同步的主实例。 此操作要么会成功, 要么会回滚。 
1. 验证该命令是否已成功完成, 方法`nslook up <fog-name>.secondary.database.windows.net`是使用确定 DNS CNAME 条目是否指向目标区域的 IP 地址。 如果 switch 命令失败, 则不会更新 CNAME。 

### <a name="remove-the-source-managed-instances"></a>删除源托管实例
移动完成后, 请删除源区域中的资源, 以避免产生不必要的费用。 

1. 使用[AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)删除故障转移组。 这会删除故障转移组配置, 并终止两个实例之间的异地复制链接。 
1. 使用[AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)删除源托管实例。 
1. 删除资源组中的任何其他资源, 如虚拟群集、虚拟网络和安全组。 

## <a name="next-steps"></a>后续步骤 

在迁移 Azure SQL 数据库后[管理](sql-database-manage-after-migration.md)Azure SQL 数据库。 

