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
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299443"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>支持的区域
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

您可以使用 Azure PowerShell 创建增量快照。 您将需要最新版本的 Azure PowerShell，以下命令将安装它或更新现有安装到最新：

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

安装后，使用`az login`登录到 PowerShell 会话。

要使用 Azure PowerShell 创建增量快照，请使用`-Incremental`该参数设置[使用 New-AzSnapConfig 的](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0)配置，然后将该配置作为变量传递到[New-AzSnapshot。](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) `-Snapshot`

使用`<yourDiskNameHere>``<yourResourceGroupNameHere>`值替换`<yourDesiredSnapShotNameHere>`和 ，然后可以使用以下脚本创建增量快照：

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

您可以使用 快照`SourceResourceId`的属性和快照`SourceUniqueId`的属性标识同一磁盘中的增量快照。 `SourceResourceId`是父磁盘的 Azure 资源管理器资源 ID。 `SourceUniqueId`是从磁盘`UniqueId`属性继承的值。 如果要删除磁盘，然后创建同名的新磁盘，`UniqueId`则属性的值将更改。

可以使用`SourceResourceId`和`SourceUniqueId`创建与特定磁盘关联的所有快照的列表。 替换为`<yourResourceGroupNameHere>`值，然后可以使用以下示例列出现有的增量快照：

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>资源管理器模板

您还可以使用 Azure 资源管理器模板创建增量快照。 您需要确保 apiVersion 设置为**2019-03-01，** 并且增量属性也设置为 true。 以下代码段是如何使用资源管理器模板创建增量快照的示例：

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

如果要查看示例代码，演示使用 .NET 的增量快照的差分功能，请参阅[将 Azure 托管磁盘备份复制到具有增量快照差异功能的另一个区域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
