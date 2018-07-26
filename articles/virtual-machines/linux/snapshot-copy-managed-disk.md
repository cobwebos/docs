---
title: 在 Azure 中创建 VHD 快照 | Microsoft Docs
description: 了解如何在 Azure 中创建 VHD 的副本作为备份或用于解决问题。
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 224f017decc3f48a23cb3fbf14f9a4e744bfaded
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006999"
---
# <a name="create-a-snapshot"></a>创建快照 

创建 OS 或数据磁盘的快照作为备份，或用于解决 VM 问题。 快照是 VHD 的完整只读副本。 

## <a name="use-azure-cli"></a>使用 Azure CLI 

以下示例要求使用 [Cloud Shell](https://shell.azure.com/bash) 或已安装 Azure CLI 2.0。 若要查找版本，请运行 **az --version**。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

以下步骤说明如何使用带有 **--source-disk** 参数的 **az snapshot create** 命令创建快照。 以下示例假设 *myResourceGroup* 资源组中存在名为 *myVM* 的 VM。

使用 [az vm show](/cli/azure/vm#az-vm-show) 获取磁盘 ID。

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

使用 [az snapshot create](/cli/azure/snapshot#az-snapshot-create) 创建名为 *osDisk-backup* 的快照。

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> 如果希望将快照存储在具有区域复原能力的存储中，需要在支持[可用性区域](../../availability-zones/az-overview.md)的区域中创建该快照并包括 **--sku Standard_ZRS** 参数。

可以使用 [az snapshot list](/cli/azure/snapshot#az-snapshot-list) 查看快照列表。

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>使用 Azure 门户 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 首先在左上角单击“创建资源”并搜索“快照”。 从搜索结果中选择“快照”。
3. 在“快照”边栏选项卡中，单击“创建”。
4. 输入快照的“名称”。
5. 选择现有的资源组，或键入新资源组的名称。 
7. 对于**源磁盘**，选择要获取其快照的托管磁盘。
8. 选择用于存储快照的“帐户类型”。 使用 **Standard HDD**，除非需要将其存储在高性能 SSD 上。
9. 单击“创建”。


## <a name="next-steps"></a>后续步骤

 通过从快照创建托管磁盘，然后将新的托管磁盘附加为 OS 磁盘来从快照创建虚拟机。 有关详细信息，请参阅[从快照创建 VM](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) 脚本。

