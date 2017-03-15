---
title: "如何使用 Azure CLI 2.0 调整 Linux VM 的大小 | Microsoft 文档"
description: "如何通过更改 VM 大小来扩展或缩减 Linux 虚拟机。"
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d149e51441dbf440732b1930245d27a6ff144970
ms.lasthandoff: 03/03/2017


---
# <a name="resize-a-linux-virtual-machine-using-cli-20"></a>使用 CLI 2.0 调整 Linux 虚拟机的大小

预配虚拟机 (VM) 后，可以通过更改 [VM 大小][vm-sizes]来扩展或缩减 VM。 在某些情况下，必须先解除分配 VM。 如果所需大小在托管 VM 的硬件群集上不可用，则需要解除分配 VM。 本文详细介绍了如何使用 Azure CLI 2.0 来调整 Linux VM 的大小。 还可以使用 [Azure CLI 1.0](virtual-machines-linux-change-vm-size-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 执行这些步骤。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](#resize-a-linux-vm) – 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0](virtual-machines-linux-change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 适用于资源管理部署模型的下一代 CLI



若要调整 VM 的大小，需要最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并已使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。

1. 使用 [az vm list-vm-resize-options](/cli/azure/vm#list-vm-resize-options) 查看托管 VM 的硬件群集上可用的 VM 大小的列表。 以下示例列出资源组 `myResourceGroup` 区域中名为 `myVM` 的 VM 的 VM 大小：
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. 如果列出了所需的 VM 大小，则使用 [az vm resize](/cli/azure/vm#resize) 调整 VM 大小。 以下示例将名为 `myVM` 的 VM 调整为 `Standard_DS3_v2` 大小：
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    在此过程中，VM 将重新启动。 重启后，现有 OS 和数据磁盘将重新映射。 临时磁盘上的所有内容将会丢失。

3. 如果未列出所需的 VM 大小，则需要先使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除分配 VM。 此过程允许将 VM 调整为该区域支持的任何可用大小然后将其启动。 以下步骤会将名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM 解除分配、调整大小并启动：
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > 解除分配 VM 也会释放分配给该 VM 的所有动态 IP 地址。 OS 和数据磁盘不受影响。

## <a name="next-steps"></a>后续步骤
若要提高可缩放性，请运行多个 VM 实例并进行横向扩展。 有关详细信息，请参阅[自动缩放虚拟机规模集中的 Linux 计算机][scale-set]。 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md

