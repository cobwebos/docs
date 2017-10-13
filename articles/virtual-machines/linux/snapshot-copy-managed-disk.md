---
title: "复制 Azure 托管磁盘用于备份 | Microsoft Docs"
description: "了解如何创建 Azure 托管磁盘的副本以用于备份或解决磁盘问题。"
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 2/6/2017
ms.author: rasquill
ms.openlocfilehash: c91367ef11c9d531bebac7c069d2df586607ec29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>使用托管快照创建一个存储为 Azure 托管磁盘的 VHD 的副本
获取托管磁盘的快照以用于备份，或从快照创建托管磁盘，并将其附加到测试虚拟机进行故障排除。 托管快照是 VM 托管磁盘的完整时间点副本。 它创建 VHD 的只读副本，并且默认将其存储为标准托管磁盘。 

有关定价的详细信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/managed-disks/)。 <!--Add link to topic or blog post that explains managed disks. -->

使用 Azure 门户或 Azure CLI 2.0 获取托管磁盘的快照。

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>使用 Azure CLI 2.0 拍摄快照

> [!NOTE] 
> 以下示例需要安装 Azure CLI 2.0 并登录到 Azure 帐户。

以下步骤说明如何使用带有 `--source-disk` 参数的 `az snapshot create` 命令获取和拍摄托管操作系统磁盘的快照。 以下示例假设在 `myResourceGroup` 资源组中存在使用托管操作系统磁盘创建的名为 `myVM` 的VM。

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


