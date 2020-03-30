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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343038"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

您可以使用 Azure CLI 创建增量快照，则需要最新版本的 Azure CLI。 

在 Windows 上，以下命令将安装或更新现有安装到最新版本：
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
在 Linux 上，CLI 安装将因操作系统版本而异。  请参阅为特定 Linux 版本[安装 Azure CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli)

要创建增量快照，请使用使用[az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create)参数`--incremental`创建的 az 快照。

下面的示例`<yourDesiredSnapShotNameHere>`创建增量快照，替换 、`<yourResourceGroupNameHere>``<exampleDiskName>`和 使用`<exampleLocation>`您自己的值，然后运行示例：

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

您可以使用 快照`SourceResourceId`的属性和快照`SourceUniqueId`的属性标识同一磁盘中的增量快照。 `SourceResourceId`是父磁盘的 Azure 资源管理器资源 ID。 `SourceUniqueId`是从磁盘`UniqueId`属性继承的值。 如果要删除磁盘，然后创建同名的新磁盘，`UniqueId`则属性的值将更改。

可以使用`SourceResourceId`和`SourceUniqueId`创建与特定磁盘关联的所有快照的列表。 下面的示例将列出与特定磁盘关联的所有增量快照，但需要一些设置。

此示例使用 jq 查询数据。 要运行该示例，必须[安装 jq](https://stedolan.github.io/jq/download/)。

替换`<yourResourceGroupNameHere>`并使用`<exampleDiskName>`值替换和值，然后可以使用以下示例列出现有的增量快照，只要您也安装了 jq：

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
