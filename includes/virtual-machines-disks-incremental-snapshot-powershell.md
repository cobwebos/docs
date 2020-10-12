---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9e7386e21442b5a76aae656a36e2858b52ecef65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102482"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>支持的区域
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

可以使用 Azure PowerShell 创建增量快照。 将需要最新版本的 Azure PowerShell，以下命令将安装它或将现有安装更新到最新版本：

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

安装之后，使用 `Connect-AzAccount` 登录到 PowerShell 会话。

若要使用 Azure PowerShell 创建增量快照，请使用含有 `-Incremental` 参数的 [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) 设置配置，然后将其作为变量通过 `-Snapshot` 参数传递给 [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0)。

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

可以使用快照的 `SourceResourceId` 和 `SourceUniqueId` 属性标识同一磁盘中的增量快照。 `SourceResourceId` 是父磁盘的 Azure 资源管理器资源 ID。 `SourceUniqueId` 是从磁盘的 `UniqueId` 属性继承的值。 如果要删除某个磁盘，然后创建具有相同名称的新磁盘，则 `UniqueId` 属性的值将更改。

可以使用 `SourceResourceId` 和 `SourceUniqueId` 来创建与特定磁盘关联的所有快照的列表。 将 `<yourResourceGroupNameHere>` 替换为你的值，然后可以使用以下示例列出现有增量快照：

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Resource Manager 模板

还可以使用 Azure 资源管理器模板创建增量快照。 需要确保 apiVersion 设置为 2019-03-01 且增量属性也设置为 true****。 以下代码片段是使用资源管理器模板创建增量快照的示例：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>后续步骤

要查看演示使用 .NET 的增量快照的差异功能的示例代码，请参阅[通过增量快照差异功能将 Azure 托管磁盘备份复制到另一区域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
