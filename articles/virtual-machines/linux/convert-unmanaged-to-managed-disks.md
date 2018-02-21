---
title: "将 Azure 中的 Linux 虚拟机从非托管磁盘转换为托管磁盘 - Azure 托管磁盘 | Microsoft Docs"
description: "如何在 Resource Manager 部署模型中使用 Azure CLI 2.0 将 Linux VM 从非托管磁盘转换为托管磁盘"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 75031b6189710286625406246e6dcde6f1c2b938
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>将 Linux 虚拟机从非托管磁盘转换为托管磁盘

如果有使用非托管磁盘的现有 Linux 虚拟机 (VM)，可以将这些 VM 转换为使用 [Azure 托管磁盘](../linux/managed-disks-overview.md)。 此过程将同时转换 OS 磁盘和任何附加的数据磁盘。

本文介绍如何使用 Azure CLI 转换 VM。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="before-you-begin"></a>开始之前
* 请查看[有关迁移到托管磁盘的常见问题](faq-for-disks.md#migrate-to-managed-disks)。

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## <a name="convert-single-instance-vms"></a>转换单实例 VM
本节介绍如何将单实例 Azure VM 从非托管磁盘转换为托管磁盘。 （如果 VM 位于可用性集中，请参阅下一节。）可通过此过程，将 VM 从高级 (SSD) 非托管磁盘转换为高级托管磁盘，或将标准 (HDD) 非托管磁盘转换为标准托管磁盘。

1. 使用 [az vm deallocate](/cli/azure/vm#az_vm_deallocate) 解除分配 VM。 以下示例在名为 `myResourceGroup` 的资源组中解除分配名为 `myVM` 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. 使用 [az vm convert](/cli/azure/vm#az_vm_convert) 将 VM 转换为托管磁盘。 以下过程转换名为 `myVM` 的 VM，包括 OS 磁盘和任何数据磁盘：

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. 使用 [az vm start](/cli/azure/vm#az_vm_start) 在转换为托管磁盘后启动 VM。 以下示例启动名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>转换可用性集中的 VM

如果要转换为托管磁盘的 VM 位于可用性集中，则需要先将可用性集转换为托管可用性集。

可用性集中的所有 VM 都必须在转换可用性集之前解除分配。 可用性集转换为托管可用性集后，计划将所有 VM 转换为托管磁盘。 然后，启动所有 VM，并继续照常操作。

1. 使用 [az vm availability-set list](/cli/azure/vm/availability-set#az_vm_availability_set_list) 列出可用性集中的所有 VM。 以下示例列出了名为 `myResourceGroup` 的资源组中名为 `myAvailabilitySet` 的可用性集中的所有 VM：

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. 使用 [az vm deallocate](/cli/azure/vm#az_vm_deallocate) 解除分配所有 VM。 以下示例在名为 `myResourceGroup` 的资源组中解除分配名为 `myVM` 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. 使用 [az vm availability-set convert](/cli/azure/vm/availability-set#az_vm_availability_set_convert) 转换可用性集。 以下示例转换名为 `myResourceGroup` 的资源组中名为 `myAvailabilitySet` 的可用性集：

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. 使用 [az vm convert](/cli/azure/vm#az_vm_convert) 将所有 VM 转换为托管磁盘。 以下过程转换名为 `myVM` 的 VM，包括 OS 磁盘和任何数据磁盘：

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. 使用 [az vm start](/cli/azure/vm#az_vm_start) 在转换为托管磁盘后启动所有 VM。 以下示例在名为 `myResourceGroup` 的资源组中启动名为 `myVM` 的 VM：

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>后续步骤
有关存储选项的详细信息，请参阅 [Azure 托管磁盘概述](../windows/managed-disks-overview.md)。
