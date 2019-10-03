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
ms.openlocfilehash: ee8a711a867f8abdc831b0d1d9d0b504b1104955
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310127"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>为托管磁盘创建增量快照（预览版）

增量快照（预览版）是托管磁盘的时间点备份，在这种情况下，只包含自上一次快照以来所做的所有更改。 尝试下载或使用增量快照时，将使用完整的 VHD。 托管磁盘快照的这一新功能可能会使它们更加经济高效，因为你不再需要在每个单独的快照中存储整个磁盘，除非你选择。 与常规快照一样，增量快照可用于创建完全托管的磁盘，或创建一个常规快照。

增量快照和常规快照之间存在一些差异。 增量快照始终使用标准 Hdd 存储，而不考虑磁盘的存储类型，而常规快照可以使用高级 Ssd。 如果在高级存储上使用常规快照来扩展 VM 部署，建议在[共享映像库](../articles/virtual-machines/linux/shared-image-galleries.md)中的标准存储上使用自定义映像。 它将帮助你以较低的成本实现更大的规模。 此外，增量快照可能会通过[区域冗余存储](../articles/storage/common/storage-redundancy-zrs.md)（ZRS）提供更好的可靠性。 如果 ZRS 在所选区域中可用，则增量快照将自动使用 ZRS。 如果 ZRS 在区域中不可用，则快照将默认为[本地冗余存储](../articles/storage/common/storage-redundancy-lrs.md)（LRS）。 您可以重写此行为，并手动选择一个，但我们不建议这样做。

增量快照还提供了独特的功能，可用于托管磁盘。 它们使你能够在相同的托管磁盘的两个增量快照之间进行更改，减小到块级别。 跨区域复制快照时，可以使用此功能来减少数据占用量。

如果尚未注册预览版，并且想要开始使用增量快照，请通过电子邮件发送AzureDisks@microsoft.com到公共预览版。

## <a name="restrictions"></a>限制

- 增量快照目前仅在美国西部提供。
- 更改磁盘大小时，当前无法创建增量快照。
- 当前不能在订阅之间移动增量快照。
- 在任何给定时间，当前最多只能生成一个特定快照系列的最多五个快照的 SAS Uri。
- 不能为该磁盘的订阅之外的特定磁盘创建增量快照。
- 每个磁盘最多可以创建7个增量快照，每5分钟一次。
- 总共可以为单个磁盘创建200个增量快照。

## <a name="powershell"></a>PowerShell

您可以使用 Azure PowerShell 来创建增量快照。 你将需要最新版本的 Azure PowerShell，以下命令将安装它或将现有安装更新到最新版本：

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

安装后，请通过`az login`登录到 PowerShell 会话。

若要使用 Azure PowerShell 创建增量快照，请使用[AzSnapShotConfig](https://docs.microsoft.com/en-us/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) `-Incremental`参数设置配置，然后通过`-Snapshot`参数将其作为变量传递给[AzSnapshot](https://docs.microsoft.com/en-us/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) 。

将`<yourDiskNameHere>`、 `<yourResourceGroupNameHere>`和`<yourDesiredSnapShotNameHere>`替换为你的值，然后可以使用以下脚本创建增量快照：

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

可以通过`SourceResourceId`快照的`SourceUniqueId`和属性标识同一磁盘中的增量快照。 `SourceResourceId`父磁盘的 Azure 资源管理器资源 ID。 `SourceUniqueId`是从`UniqueId`磁盘的属性继承的值。 如果要删除某一磁盘，然后创建同名的新磁盘，则该`UniqueId`属性的值将更改。

你可以使用`SourceResourceId`和`SourceUniqueId`来创建与特定磁盘关联的所有快照的列表。 将`<yourResourceGroupNameHere>`替换为你的值，然后可以使用以下示例列出现有增量快照：

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

## <a name="cli"></a>CLI

你可以使用 Azure CLI 创建增量快照，你将需要 Azure CLI 的最新版本。 以下命令将安装或更新现有安装到最新版本：

```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```

若要创建增量快照，请将[az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create)与`--incremental`参数一起使用。

下面的示例创建一个增量快照，将`<yourDesiredSnapShotNameHere>``<exampleDiskName>`、 `<yourResourceGroupNameHere>`、、和`<exampleLocation>`替换为自己的值，然后运行该示例：

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

可以通过`SourceResourceId`快照的`SourceUniqueId`和属性标识同一磁盘中的增量快照。 `SourceResourceId`父磁盘的 Azure 资源管理器资源 ID。 `SourceUniqueId`是从`UniqueId`磁盘的属性继承的值。 如果要删除某一磁盘，然后创建同名的新磁盘，则该`UniqueId`属性的值将更改。

你可以使用`SourceResourceId`和`SourceUniqueId`来创建与特定磁盘关联的所有快照的列表。 下面的示例将列出与特定磁盘关联的所有增量快照，但需要进行一些设置。

此示例使用 jq 来查询数据。 若要运行该示例，必须[安装 jq](https://stedolan.github.io/jq/download/)。

将`<yourResourceGroupNameHere>` 和`<exampleDiskName>`替换为你的值，然后可以使用以下示例列出现有增量快照，前提是你还安装了 jq：

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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

如果尚未注册预览版，并且想要开始使用增量快照，请通过电子邮件发送AzureDisks@microsoft.com到公共预览版。
