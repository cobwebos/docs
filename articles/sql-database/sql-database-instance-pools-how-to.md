---
title: 实例池指南（预览）
description: 本文介绍如何创建和管理 Azure SQL 数据库实例池（预览）。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299356"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Azure SQL 数据库实例池（预览）指南

本文详细介绍了如何创建和管理[实例池](sql-database-instance-pools.md)。

## <a name="instance-pool-operations"></a>实例池操作

下表显示了与实例池及其可用性相关的可用操作，这些操作在 Azure 门户和 PowerShell 中。

|命令|Azure 门户|PowerShell|
|:---|:---|:---|
|创建实例池|否|是|
|更新实例池（属性数量有限）|否 |是 |
|检查实例池使用情况和属性|否|是 |
|删除实例池|否|是|
|在实例池内创建托管实例|否|是|
|更新托管实例资源使用情况|是 |是|
|检查托管实例使用情况和属性|是|是|
|从池中删除托管实例|是|是|
|在放置在池中的托管实例中创建数据库|是|是|
|从托管实例中删除数据库|是|是|

可用的[电源外壳命令](https://docs.microsoft.com/powershell/module/az.sql/)

|Cmdlet |描述 |
|:---|:---|
|[新实例池](/powershell/module/az.sql/new-azsqlinstancepool/) | 创建 Azure SQL 数据库实例池。 |
|[获取-AzSql实例池](/powershell/module/az.sql/get-azsqlinstancepool/) | 返回有关 Azure SQL 实例池的信息。 |
|[设置-AzSql实例池](/powershell/module/az.sql/set-azsqlinstancepool/) | 设置 Azure SQL 数据库实例池的属性。 |
|[删除-AzSql实例池](/powershell/module/az.sql/remove-azsqlinstancepool/) | 删除 Azure SQL 数据库实例池。 |
|[获取-AzSql实例池使用](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | 返回有关 Azure SQL 实例池使用情况的信息。 |


要使用 PowerShell，[请安装最新版本的 PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)，然后按照说明[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。

对于与池内实例和单个实例相关的操作，请使用标准[托管实例命令](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances)，但在在池中为实例使用这些命令时，必须填充*实例池名称*属性。

## <a name="how-to-deploy-managed-instances-into-pools"></a>如何将托管实例部署到池中

将实例部署到池的过程包括以下两个步骤：

1. 一次性实例池部署。 这是一个长时间运行的操作，其中持续时间与部署[在空子网中创建的单个实例](sql-database-managed-instance.md#managed-instance-management-operations)相同。

2. 实例池中的重复实例部署。 实例池参数必须显式指定为此操作的一部分。 这是一个相对快速的操作，通常需要长达 5 分钟的时间。

在公共预览版中，仅支持使用 PowerShell 和资源管理器模板。 Azure 门户体验当前不可用。

将托管实例部署到池后，*可以使用*Azure 门户更改定价层页上的属性。


## <a name="create-an-instance-pool"></a>创建实例池

要创建实例池：

1. [使用子网创建虚拟网络](#create-a-virtual-network-with-a-subnet)。
2. [创建实例池](#create-an-instance-pool)。


### <a name="create-a-virtual-network-with-a-subnet"></a>创建带子网的虚拟网络 

要将多个实例池放置在同一虚拟网络中，请参阅以下文章：

- [确定 Azure SQL 数据库托管实例的 VNet 子网大小](sql-database-managed-instance-determine-size-vnet-subnet.md)。
- 使用[Azure 门户模板](sql-database-managed-instance-create-vnet-subnet.md)创建新的虚拟网络和子网，或按照[准备现有虚拟网络](sql-database-managed-instance-configure-vnet-subnet.md)的说明进行操作。
 


### <a name="create-an-instance-pool"></a>创建实例池 

完成上述步骤后，即可创建实例池。

以下限制适用于实例池：

- 公共预览版中仅提供通用和第 5 代版本。
- 池名称只能包含小写、数字和连字符，不能以连字符开头。
- 如果要使用 AHB（Azure 混合权益），它将在实例池级别应用。 您可以在池创建期间设置许可证类型，或在创建后随时更新它。

> [!IMPORTANT]
> 部署实例池是一项长时间运行的操作，大约需要 4.5 小时。

要获取网络参数：

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

要创建实例池：

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> 由于部署实例池是一项长时间运行的操作，因此您需要等待它完成，然后再运行本文中的任何以下步骤。

## <a name="create-a-managed-instance-inside-the-pool"></a>在池内创建托管实例 

成功部署实例池后，是时候在其中创建实例了。

要创建托管实例，请执行以下命令：

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

在池内部署实例需要几分钟时间。 创建第一个实例后，可以创建其他实例：

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>在实例内创建数据库 

要在池内的托管实例中创建和管理数据库，请使用单个实例命令。

要在托管实例内创建数据库，请：

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>获取实例池使用情况 
 
要获取池内的实例列表，可以：

```powershell
$instancePool | Get-AzSqlInstance
```


要获取池资源使用情况，

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


要获取有关池及其内部实例的详细使用情况概述，它：

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

要在实例中列出数据库，可以：

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> 每个池有 100 个数据库的限制（不是每个实例）。


## <a name="scale-a-managed-instance-inside-a-pool"></a>在池内缩放托管实例 


将托管实例填充到数据库后，可能会触及有关存储或性能的实例限制。 在这种情况下，如果未超过池使用率，则可以缩放实例。
在池内缩放托管实例是一项需要几分钟的操作。 缩放的先决条件是在实例池级别提供 vCore 和存储。

要更新 vCore 的数量和存储大小，

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


要仅更新存储大小：

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>连接到池内的托管实例

要连接到池中的托管实例，需要执行以下两个步骤：

1. [启用实例的公共终结点](#enable-the-public-endpoint-for-the-instance)。
2. [向网络安全组 （NSG） 添加入站规则](#add-an-inbound-rule-to-the-network-security-group)。

完成这两个步骤后，可以使用实例创建期间提供的公共终结点地址、端口和凭据连接到实例。 

### <a name="enable-the-public-endpoint-for-the-instance"></a>启用实例的公共终结点

可通过 Azure 门户或使用以下 PowerShell 命令为实例启用公共终结点：


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

此参数也可以在实例创建期间设置。

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>向网络安全组添加入站规则 

此步骤可以通过 Azure 门户或使用 PowerShell 命令完成，并且可以随时在为托管实例准备子网后完成。

有关详细信息，请参阅[在网络安全组上允许公共终结点流量](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)。


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>在实例池中移动现有单个实例 
 
将实例移出池是公共预览限制之一。 可以使用的解决方法依赖于将数据库从池外的实例还原到已在池中的实例的时间点还原。 

两个实例必须位于同一订阅和区域中。 当前不支持跨区域和跨订阅还原。

此过程确实有一段时间的停机时间。

要移动现有数据库：

1. 暂停要从中迁移的托管实例上的工作负载。
2. 生成脚本以创建系统数据库，并在实例池内的实例上执行它们。
3. 对每个数据库从单个实例到池中的实例进行时间点还原。

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. 将应用程序指向新实例并恢复其工作负载。

如果有多个数据库，请为每个数据库重复此过程。


## <a name="next-steps"></a>后续步骤

- 有关功能和比较列表，请参阅 [SQL 常用功能](sql-database-features.md)。
- 有关 VNet 配置的详细信息，请参阅[托管实例 VNet 配置](sql-database-managed-instance-connectivity-architecture.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
- 有关使用 Azure 数据库迁移服务 (DMS) 进行迁移的教程，请参阅[使用 DMS 进行托管实例迁移](../dms/tutorial-sql-server-to-managed-instance.md)。
- 有关使用内置故障排除智能对托管实例数据库性能的高级监视，请参阅[使用 Azure SQL 分析监视 Azure SQL 数据库](../azure-monitor/insights/azure-sql.md)。
- 有关定价信息，请参阅[SQL 数据库托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
