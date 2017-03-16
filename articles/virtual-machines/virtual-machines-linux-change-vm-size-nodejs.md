---
title: "如何使用 Azure CLI 1.0 调整 Linux VM 的大小 | Microsoft Docs"
description: "如何通过更改 VM 大小来扩展或缩减 Linux 虚拟机。"
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: fecd0dc34fad70706ac0451a24dac0dc4bf55e40
ms.lasthandoff: 03/03/2017


---

# <a name="resize-a-linux-vm-with-azure-cli-10"></a>使用 Azure CLI 1.0 调整 Linux VM 的大小

## <a name="overview"></a>概述

预配虚拟机 (VM) 后，可以通过更改 [VM 大小][vm-sizes]来扩展或缩减 VM。 在某些情况下，必须先解除分配 VM。 如果新大小在托管 VM 的硬件群集上不可用，则可能会出现这种情况。

本文介绍了如何使用 [Azure CLI][azure-cli] 来调整 Linux VM 的大小。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](#resize-a-linux-vm) – 用于经典部署模型和资源管理部署模型（本文）的 CLI
- [Azure CLI 2.0](virtual-machines-linux-change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 适用于资源管理部署模型的下一代 CLI


## <a name="resize-a-linux-vm"></a>调整 Linux VM 的大小
若要调整 VM 的大小，请执行以下步骤。

1. 运行以下 CLI 命令。 此命令将列出托管 VM 的硬件群集上的可用 VM 大小。
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. 如果列出了所需大小，请运行以下命令来调整 VM 的大小。
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    在此过程中，VM 将重新启动。 重新启动后，现有 OS 和数据磁盘将重新映射。 临时磁盘上的所有内容将会丢失。
   
    使用 `--enable-boot-diagnostics` 选项启用[启动诊断][boot-diagnostics]，以记录所有与启动相关的错误。
3. 如果未列出所需大小，请运行以下命令来解除分配 VM、调整其大小，然后将它重新启动。
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > 解除分配 VM 也会释放分配给该 VM 的所有动态 IP 地址。 OS 和数据磁盘不受影响。
   > 
   > 

## <a name="next-steps"></a>后续步骤
若要提高可缩放性，请运行多个 VM 实例并进行横向扩展。 有关详细信息，请参阅[自动缩放虚拟机规模集中的 Linux 计算机][scale-set]。 

<!-- links -->

[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md

