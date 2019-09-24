---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e39f294f7902eabef401d4c8145f4f19a07f267f
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224571"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>为托管磁盘创建增量快照（预览版）

增量快照（预览版）是托管磁盘的时间点备份，在这种情况下，只包含自上一次快照以来所做的所有更改。 尝试下载或使用增量快照时，将使用完整的 VHD。 托管磁盘快照的这一新功能可能会使它们更加经济高效，因为你不再需要在每个单独的快照中存储整个磁盘，除非你选择。 与常规快照一样，增量快照可用于创建完全托管的磁盘，或创建一个常规快照。

增量快照和常规快照之间存在一些差异。 增量快照始终使用标准 Hdd 存储，而不考虑磁盘的存储类型，而常规快照可以使用高级 Ssd。 如果在高级存储上使用常规快照来扩展 VM 部署，建议在[共享映像库](../articles/virtual-machines/linux/shared-image-galleries.md)中的标准存储上使用自定义映像。 它将帮助你以较低的成本实现更大的规模。 此外，增量快照可能会通过[区域冗余存储](../articles/storage/common/storage-redundancy-zrs.md)（ZRS）提供更好的可靠性。 如果 ZRS 在所选区域中可用，则增量快照将自动使用 ZRS。 如果 ZRS 在区域中不可用，则快照将默认为[本地冗余存储](../articles/storage/common/storage-redundancy-lrs.md)（LRS）。 您可以重写此行为，并手动选择一个，但我们不建议这样做。

增量快照还提供了独特的功能，可用于托管磁盘。 它们使你能够在相同的托管磁盘的两个增量快照之间进行更改，减小到块级别。 跨区域复制快照时，可以使用此功能来减少数据占用量。

如果尚未注册预览，并且想要开始使用增量快照，请通过电子邮件发送AzureDisks@microsoft.com到公共预览版。

## <a name="restrictions"></a>限制

- 更改磁盘大小时，当前无法创建增量快照。
- 当前不能在订阅之间移动增量快照。
- 在任何给定时间，当前最多只能生成一个特定快照系列的最多五个快照的 SAS Uri。
- 不能为该磁盘的订阅之外的特定磁盘创建增量快照。
- 每个磁盘最多可以创建7个增量快照，每5分钟一次。
- 总共可以为单个磁盘创建200个增量快照。

## <a name="powershell"></a>PowerShell

您可以使用 Azure PowerShell 来创建增量快照。 你可以在本地安装最新版本的 PowerShell。 你将需要最新版本的 Azure PowerShell，以下命令将安装它或将现有安装更新到最新版本：

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

安装后，请通过`az login`登录到 PowerShell 会话。

将`<yourDiskNameHere>`、 `<yourResourceGroupNameHere>`和`<yourDesiredSnapShotNameHere>`替换为你的值，然后可以使用以下脚本创建增量快照：

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting:
# 1. Incremental property
# 2. SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 

# You can identify incremental snapshots of the same disk by using the SourceResourceId and SourceUniqueId properties of snapshots. 
# SourceResourceId is the Azure Resource Manager resource ID of the parent disk. 
# SourceUniqueId is the value inherited from the UniqueId property of the disk. If you delete a disk and then create a disk with the same name, the value of the UniqueId property will change. 
# Following script shows how to get all the incremental snapshots in a resource group of same disk
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

## <a name="cli"></a>CLI

你可以使用[az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create)创建包含 Azure CLI 的增量快照。 示例命令如下所示：

```bash
az snapshot create -g <exampleResourceGroup> \
-n <exampleSnapshotName> \
-l <exampleLocation> \
--source <exampleVMId> \
--incremental
```

还可以通过在`--query` [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-show)上使用参数来确定 CLI 中的增量快照。 您可以使用该参数直接查询快照的**SourceResourceId**和**SourceUniqueId**属性。 SourceResourceId 是父磁盘的 Azure 资源管理器资源 ID。 **SourceUniqueId**是从磁盘的**UniqueId**属性继承的值。 如果删除磁盘，然后创建同名磁盘，则**UniqueId**属性的值将更改。

这两个查询的示例如下所示：

```bash
az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceResourceId] -o tsv

az snapshot show -g <exampleResourceGroup> \
-n <yourSnapShotName> \
--query [creationData.sourceUniqueId] -o tsv
```

## <a name="next-steps"></a>后续步骤

如果尚未注册预览，并且想要开始使用增量快照，请通过电子邮件发送AzureDisks@microsoft.com到公共预览版。
