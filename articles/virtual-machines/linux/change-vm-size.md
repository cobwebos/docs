---
title: 如何使用 Azure CLI 调整 Linux VM 的大小
description: 如何通过更改 VM 大小来增加或减少 Linux 虚拟机。
author: DavidCBerry13
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: daberry
ms.openlocfilehash: cf2716ce5d24aa86e32f6f521134590c671d5011
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83120974"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>使用 Azure CLI 重设 Linux 虚拟机大小 

预配虚拟机 (VM) 后，可以通过更改 [VM 大小][vm-sizes]来扩展或缩减 VM。 在某些情况下，必须先解除分配 VM。 如果所需大小在托管 VM 的硬件群集上不可用，则需要解除分配 VM。 本文详细介绍了如何使用 Azure CLI 重设 Linux VM 大小。 

## <a name="resize-a-vm"></a>调整 VM 的大小
若要重设 VM 大小，需要安装最新版 [Azure CLI](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index) 登录 Azure 帐户。

1. 使用 [az vm list-vm-resize-options](/cli/azure/vm) 查看托管 VM 的硬件群集上可用的 VM 大小的列表。 以下示例列出资源组 `myResourceGroup` 区域中名为 `myVM` 的 VM 的 VM 大小：
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. 如果列出了所需的 VM 大小，则使用 [az vm resize](/cli/azure/vm) 调整 VM 大小。 以下示例将名为 `myVM` 的 VM 调整为 `Standard_DS3_v2` 大小：
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    在此过程中，VM 将重新启动。 重新启动后，现有 OS 和数据磁盘将重新映射。 临时磁盘上的所有内容会丢失。

3. 如果所需的 VM 大小未列出，则需先使用 [az vm deallocate](/cli/azure/vm)解除分配 VM。 然后，可以通过此过程将 VM 的大小调整为区域支持的任何可用大小，再启动该 VM。 以下步骤会将名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM 解除分配、调整大小并启动：
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > 解除分配 VM 也会释放分配给该 VM 的所有动态 IP 地址。 OS 和数据磁盘不受影响。

## <a name="next-steps"></a>后续步骤
若要提高可伸缩性，请运行多个 VM 实例并进行横向扩展。有关详细信息，请参阅[自动缩放虚拟机规模集中的 Linux 计算机][scale-set]。 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
