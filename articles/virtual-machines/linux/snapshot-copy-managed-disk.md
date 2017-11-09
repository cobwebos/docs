---
title: "在 Azure 中创建 VHD 快照 | Microsoft Docs"
description: "了解如何在 Azure 中创建 VHD 的副本作为备份或用于解决问题。"
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: da00c48f7da5a9be146f4fdb626c93db746c0f9b
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>创建快照 

创建 OS 或数据磁盘 VHD 的快照作为备份，或用于解决 VM 问题。 快照是 VHD 的完整只读副本。 

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>使用 Azure CLI 2.0 拍摄快照

以下示例需要安装 Azure CLI 2.0 并登录到 Azure 帐户。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

以下步骤说明如何使用带有 `--source-disk` 参数的 `az snapshot create` 命令创建快照。 以下示例假设在 `myResourceGroup` 资源组中存在使用托管操作系统磁盘创建的名为 `myVM` 的VM。

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

输出应类似于：

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>使用 Azure 门户拍摄快照 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 首先在左上角单击“新建”并搜索“快照”。
3. 在“快照”边栏选项卡中，单击“创建”。
4. 输入快照的“名称”。
5. 选择现有的[资源组](../../azure-resource-manager/resource-group-overview.md#resource-groups)，或键入新资源组的名称。 
6. 选择 Azure 数据中心的位置。  
7. 对于**源磁盘**，选择要获取其快照的托管磁盘。
8. 选择用于存储快照的“帐户类型”。 建议使用 **Standard_LRS**，除非需要将其存储在高性能磁盘上。
9. 单击“创建” 。

如果计划使用快照创建托管磁盘并将其附加到需要具有高性能的 VM，请结合使用 `az snapshot create` 命令和参数 `--sku Premium_LRS`。 这会创建快照，以便将其存储为高级托管磁盘。 高级托管磁盘性能更好，因为它们是固态硬盘 (SSD)，但成本高于标准磁盘 (HDD)。


## <a name="next-steps"></a>后续步骤

 通过从快照创建托管磁盘，然后将新的托管磁盘附加为 OS 磁盘来从快照创建虚拟机。 有关详细信息，请参阅[从快照创建 VM](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) 脚本。

