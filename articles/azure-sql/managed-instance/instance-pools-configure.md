---
title: 将 SQL 托管实例部署到实例池
titleSuffix: Azure SQL Managed Instance
description: 本文介绍如何 (预览版) 创建和管理 Azure SQL 托管实例池。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: 5798220ad92a99f32f757ffa20f9233b8c79151b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617428"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>将 Azure SQL 托管实例部署到实例池
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文提供了有关如何创建 [实例池](instance-pools-overview.md) 并向其部署 Azure SQL 托管实例的详细信息。 

## <a name="instance-pool-operations"></a>实例池操作

下表显示了与实例池相关的可用操作及其在 Azure 门户和 PowerShell 中的可用性。

|Command|Azure 门户|PowerShell|
|:---|:---|:---|
|创建实例池|否|是|
| (有限数量的属性更新实例池) |否 |是 |
|检查实例池使用情况和属性|否|是 |
|删除实例池|否|是|
|在实例池中创建托管实例|否|是|
|更新托管实例的资源使用情况|是 |是|
|检查托管实例的使用情况和属性|是|是|
|从池中删除托管实例|是|是|
|在池中的实例中创建数据库|是|是|
|从 SQL 托管实例删除数据库|是|是|

可用的 [PowerShell 命令](https://docs.microsoft.com/powershell/module/az.sql/)：

|Cmdlet |说明 |
|:---|:---|
|[新-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | 创建 SQL 托管实例池。 |
|[AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | 返回有关实例池的信息。 |
|[AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | 在 SQL 托管实例中设置实例池的属性。 |
|[AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | 删除 SQL 托管实例中的实例池。 |
|[AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | 返回有关 SQL 托管实例池使用情况的信息。 |


若要使用 PowerShell，请 [安装最新版本的 Powershell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)，并按照说明 [安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)。

对于与池中的实例和单个实例相关的操作，请使用标准 [托管实例命令](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances)，但在对池中的实例使用这些命令时，必须填充 " *实例池名称* " 属性。

## <a name="deployment-process"></a>部署过程

若要将托管实例部署到实例池中，必须首先部署实例池，这是一个运行时间长的操作，该操作的持续时间与部署 [在一个空子网中创建的单个实例](sql-managed-instance-paas-overview.md#management-operations)的时间相同。 之后，你可以将托管实例部署到池中，这是一个相对较快的操作，通常需要长达五分钟的时间。 在此操作过程中，必须显式指定实例池参数。

在公共预览版中，仅支持使用 PowerShell 和 Azure 资源管理器模板进行这两项操作。 Azure 门户体验当前不可用。

将托管实例部署到池后，你 *可以* 使用 Azure 门户在 "定价层" 页上更改其属性。

## <a name="create-a-virtual-network-with-a-subnet"></a>创建带子网的虚拟网络 

若要在同一虚拟网络中放置多个实例池，请参阅以下文章：

- [为 AZURE SQL 托管实例确定 VNet 子网大小](vnet-subnet-determine-size.md)。
- 使用 [Azure 门户模板](virtual-network-subnet-create-arm-template.md) 创建新的虚拟网络和子网，或者按照 [准备现有虚拟网络](vnet-existing-add-subnet.md)的说明进行操作。
 

## <a name="create-an-instance-pool"></a>创建实例池 

完成前面的步骤后，便可以创建实例池了。

以下限制适用于实例池：

- 仅常规用途和 Gen5 在公共预览版中可用。
- 池名称只能包含小写字母、数字和连字符，并且不能以连字符开头。
- 如果要使用 Azure 混合权益，则将其应用于实例池级别。 可以在创建池时设置许可证类型，也可以在创建后随时对其进行更新。

> [!IMPORTANT]
> 部署实例池是长时间运行的操作，耗时大约4.5 小时。

获取网络参数：

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

创建实例池：

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> 由于部署实例池是长时间运行的操作，因此在运行本文中的任何步骤之前，需要等待它完成。

## <a name="create-a-managed-instance"></a>创建托管实例

成功部署实例池之后，就可以在其中创建托管实例了。

若要创建托管实例，请执行以下命令：

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

在池中部署实例只需几分钟即可完成。 创建第一个实例后，可以创建其他实例：

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>创建数据库 

若要创建和管理位于池中的托管实例中的数据库，请使用单个实例命令。

在托管实例内创建数据库：

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>获取池使用情况 
 
获取池中的实例列表：

```powershell
$instancePool | Get-AzSqlInstance
```


获取池资源使用情况：

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


获取池和其中的实例的详细使用情况概述：

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

列出实例中的数据库：

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> 每个池的数据库数限制为100， (每个实例不) 。


## <a name="scale"></a>缩放 


使用数据库填充托管实例后，可能会遇到有关存储或性能的实例限制。 在这种情况下，如果尚未超出池使用率，则可以缩放实例。
缩放池中的托管实例是一种操作，只需几分钟即可完成。 在实例池级别上，Vcore 和存储提供了缩放的先决条件。

更新 "Vcore" 和 "存储大小" 的数目：

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


仅更新存储大小：

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>连接 

若要连接到池中的托管实例，需要以下两个步骤：

1. [为实例启用公共终结点](#enable-the-public-endpoint)。
2. [将入站规则添加到网络安全组 (NSG) ](#add-an-inbound-rule-to-the-network-security-group)。

完成这两个步骤后，可以使用在实例创建过程中提供的公共终结点地址、端口和凭据连接到该实例。 

### <a name="enable-the-public-endpoint"></a>启用公共终结点

为实例启用公共终结点可通过 Azure 门户或使用以下 PowerShell 命令完成：


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

也可以在创建实例期间设置此参数。

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>向网络安全组添加入站规则 

可以通过 Azure 门户或使用 PowerShell 命令来完成此步骤，并且可以在为托管实例准备子网后随时执行此步骤。

有关详细信息，请参阅 [允许网络安全组上的公共终结点流量](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)。


## <a name="move-an-existing-single-instance-to-a-pool"></a>将现有的单个实例移到池中
 
将实例移入和移出池是公共预览版限制之一。 解决方法依赖于将数据库从池之外的实例还原到已在池中的实例。 

两个实例都必须在同一订阅和区域中。 当前不支持跨区域和跨订阅还原。

此过程有一段停机时间。

若要移动现有数据库：

1. 暂停要从中迁移的托管实例上的工作负荷。
2. 生成脚本以创建系统数据库，并在实例池中的实例上执行这些数据库。
3. 执行从单个实例到池中实例的每个数据库的时间点还原。

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

4. 将应用程序指向新实例，并恢复其工作负荷。

如果有多个数据库，请对每个数据库重复此过程。


## <a name="next-steps"></a>后续步骤

- 有关功能和比较列表，请参阅 [SQL 常用功能](../database/features-comparison.md)。
- 有关 VNet 配置的详细信息，请参阅 [SQL 托管实例 VNet 配置](connectivity-architecture-overview.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关使用 Azure 数据库迁移服务进行迁移的教程，请参阅[使用数据库迁移服务进行 SQL 托管实例迁移](../../dms/tutorial-sql-server-to-managed-instance.md)。
- 有关使用内置故障排除智能对 SQL 托管实例数据库性能进行的高级监视，请参阅[使用 Azure SQL Analytics 监视 Azure SQL 托管实例](../../azure-monitor/insights/azure-sql.md)。
- 有关定价信息，请参阅 [SQL 托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
