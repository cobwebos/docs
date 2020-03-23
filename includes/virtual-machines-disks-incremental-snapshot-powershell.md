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
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299443"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>支持的区域
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

您可以使用 Azure PowerShell 来创建增量快照。 你将需要最新版本的 Azure PowerShell，以下命令将安装它或将现有安装更新到最新版本：

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

安装之后，请登录到 PowerShell 会话并 `az login`。

若要使用 Azure PowerShell 创建增量快照，请使用 AzSnapshot 参数将 `-Incremental` [AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0)设置为，并通过 `-Snapshot` 参数将其作为变量传递到[](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) 。

将 `<yourDiskNameHere>`、`<yourResourceGroupNameHere>`和 `<yourDesiredSnapShotNameHere>` 替换为你的值，然后可以使用以下脚本创建增量快照：

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

可以通过快照的 "`SourceResourceId`" 和 "`SourceUniqueId`" 属性来标识同一磁盘中的增量快照。 `SourceResourceId` 是父磁盘的 Azure 资源管理器资源 ID。 `SourceUniqueId` 是从磁盘的 `UniqueId` 属性继承的值。 如果要删除某一磁盘，然后创建同名的新磁盘，则 `UniqueId` 属性的值将更改。

你可以使用 `SourceResourceId` 和 `SourceUniqueId` 来创建与特定磁盘关联的所有快照的列表。 将 `<yourResourceGroupNameHere>` 替换为你的值，然后可以使用以下示例列出现有增量快照：

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

还可以使用 Azure 资源管理器模板创建增量快照。 需要确保 apiVersion 设置为**2019-03-01** ，并且增量属性也设置为 true。 以下代码片段举例说明了如何使用资源管理器模板创建增量快照：

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

若要查看演示增量快照的差异功能的示例代码，请参阅使用 .NET，请参阅[将 Azure 托管磁盘备份复制到另一个区域，其差异功能为增量快照](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
