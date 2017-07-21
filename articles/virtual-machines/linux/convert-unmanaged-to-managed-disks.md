---
title: "将 Azure 中的 Linux VM 从非托管磁盘转换为托管磁盘 | Microsoft Docs"
description: "如何在 Resource Manager 部署模型中使用 Azure CLI 2.0 将 Linux VM 从非托管磁盘转换为托管磁盘"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 06/23/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 37c47061b0774d9670b9a2d304d069d5f683c2d2
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017

---

# 将 Linux VM 从非托管磁盘转换为 Azure 托管磁盘
<a id="convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks" class="xliff"></a>

如果有使用非托管磁盘的现有 Linux 虚拟机 (VM)，可以将这些 VM 转换为使用 [Azure 托管磁盘](../../storage/storage-managed-disks-overview.md)。 此过程将同时转换 OS 磁盘和任何附加的数据磁盘。

本文介绍了如何使用 Azure CLI 转换 VM。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli.md)。 

## 开始之前
<a id="before-you-begin" class="xliff"></a>

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]


## 转换单实例 VM
<a id="convert-single-instance-vms" class="xliff"></a>
本部分绍了如何将单实例 Azure VM 从非托管磁盘转换为托管磁盘。 （如果 VM 位于可用性集中，请参阅下一部分。）可通过此过程，将高级 (SSD) 非托管磁盘转换为高级托管磁盘，或将标准 (HDD) 非托管磁盘转换为标准托管磁盘。

1. 使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除分配 VM。 以下示例在名为 `myResourceGroup` 的资源组中解除分配名为 `myVM` 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. 使用 [az vm convert](/cli/azure/vm#convert) 将 VM 转换为托管磁盘。 以下过程转换名为 `myVM` 的 VM，包括 OS 磁盘和任何数据磁盘：

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. 使用 [az vm start](/cli/azure/vm#start) 在转换为托管磁盘后启动 VM。 以下示例启动名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## 转换可用性集中的 VM
<a id="convert-vms-in-an-availability-set" class="xliff"></a>

如果要转换为托管磁盘的 VM 位于可用性集中，则需要先将可用性集转换为托管可用性集。

可用性集中的所有 VM 都必须在转换可用性集之前解除分配。 可用性集转换为托管可用性集后，计划将所有 VM 转换为托管磁盘。 然后，启动所有 VM，并继续照常操作。

1. 使用 [az vm availability-set list](/cli/azure/vm/availability-set#list) 列出可用性集中的所有 VM。 以下示例列出了名为 `myResourceGroup` 的资源组中名为 `myAvailabilitySet` 的可用性集中的所有 VM：

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. 使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除分配所有 VM。 以下示例在名为 `myResourceGroup` 的资源组中解除分配名为 `myVM` 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. 使用 [az vm availability-set convert](/cli/azure/vm/availability-set#convert) 转换可用性集。 以下示例转换名为 `myResourceGroup` 的资源组中名为 `myAvailabilitySet` 的可用性集：

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. 使用 [az vm convert](/cli/azure/vm#convert) 将所有 VM 转换为托管磁盘。 以下过程转换名为 `myVM` 的 VM，包括 OS 磁盘和任何数据磁盘：

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. 使用 [az vm start](/cli/azure/vm#start) 在转换为托管磁盘后启动所有 VM。 以下示例启动名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM。

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## 托管磁盘和 Azure 存储服务加密
<a id="managed-disks-and-azure-storage-service-encryption" class="xliff"></a>
如果非托管磁盘所在的存储帐户曾使用 [Azure 存储服务加密](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)进行了加密，则无法使用之前的步骤将其转换为托管磁盘。 下列步骤详细说明了如何复制和使用曾位于加密存储帐户中的非托管磁盘：

1. 使用 [az storage blob copy start](/cli/azure/storage/blob/copy#start) 将虚拟硬盘 (VHD) 复制到从未启用 Azure 存储服务加密的存储帐户。

2. 采用以下任一方式使用复制的 VM：

* 使用 [az vm create](/cli/azure/vm#create) 创建使用托管磁盘的 VM 并在创建期间指定该 VHD 文件

* 使用 [az vm disk attach](/cli/azure/vm/disk#attach) 将复制的 VHD 附加到具有托管磁盘的正在运行中的 VM。

## 后续步骤
<a id="next-steps" class="xliff"></a>
有关存储选项的详细信息，请参阅 [Azure 托管磁盘概述](../../storage/storage-managed-disks-overview.md)。

