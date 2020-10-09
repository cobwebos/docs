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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204428"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

可以使用 Azure CLI 创建增量快照（需要 Azure CLI 的最新版本）。 

在 Windows 上，以下命令将安装最新版本或将现有安装更新到最新版本：
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
在 Linux 上，CLI 安装因操作系统版本而异。  请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 以便为特定 Linux 版本进行安装。

若要创建增量快照，请使用具有 `--incremental` 参数的 [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create)。

下面的示例创建一个增量快照，将 `<yourDesiredSnapShotNameHere>`、`<yourResourceGroupNameHere>`、`<exampleDiskName>` 和 `<exampleLocation>` 替换为你自己的值，然后运行该示例：

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

可以使用快照的 `SourceResourceId` 和 `SourceUniqueId` 属性标识同一磁盘中的增量快照。 `SourceResourceId` 是父磁盘的 Azure 资源管理器资源 ID。 `SourceUniqueId` 是从磁盘的 `UniqueId` 属性继承的值。 如果要删除某个磁盘，然后创建具有相同名称的新磁盘，则 `UniqueId` 属性的值将更改。

可以使用 `SourceResourceId` 和 `SourceUniqueId` 来创建与特定磁盘关联的所有快照的列表。 下面的示例将列出与特定磁盘关联的所有增量快照，但需要进行一些设置。

此示例使用 jq 来查询数据。 若要运行该示例，必须[安装 jq](https://stedolan.github.io/jq/download/)。

将 `<yourResourceGroupNameHere>` 和 `<exampleDiskName>` 替换为你的值，然后可以使用以下示例列出现有增量快照（前提是已安装 jq）：

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
