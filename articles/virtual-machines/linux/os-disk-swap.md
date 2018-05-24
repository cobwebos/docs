---
title: 使用 CLI 交换 Azure VM 的 OS 磁盘 | Microsoft 文档
description: 使用 CLI 更改由 Azure 虚拟机使用的操作系统磁盘。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 1732b60ee135b765cdeea43f981bcef9575ff32c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195970"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>使用 CLI 更改由 Azure VM 使用的 OS 磁盘


如果有现有 VM，但希望将磁盘交换为备份磁盘或其他 OS 磁盘，则可使用 Azure CLI 交换 OS 磁盘。 你无需删除和重新创建 VM。 甚至可在另一个资源组中使用托管磁盘，只要该磁盘尚未使用。

VM 需要停止\取消分配，然后可用其他托管磁盘的资源 ID 替换托管磁盘的资源 ID。 

确保 VM 大小和存储类型与要附加的磁盘兼容。 例如，如果要使用的磁盘位于高级存储中，则 VM 需要能使用高级存储（如 DS 系列大小）。

本文需要 Azure CLI 2.0.25 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


使用 [az disk list](/cli/azure/disk#list) 获取资源组中的磁盘列表。

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


在交换磁盘之前，使用 [az vm stop](/cli/azure/vm#stop) 停止\取消分配 VM。

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


使用 [az vm update](/cli/azure/vm#az-vm-update) 以及新磁盘的完整资源 ID 获取 `--osdisk` 参数 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
使用 [az vm start](/cli/azure/vm#start) 重启 VM：

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**后续步骤**

要创建磁盘副本，请参阅[拍摄磁盘快照](snapshot-copy-managed-disk.md)。