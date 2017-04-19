---
title: "在 Azure 中扩展 Linux VM 上的虚拟硬盘 | Microsoft Docs"
description: "了解如何使用 Azure CLI 2.0 在 Linux VM 上扩展虚拟硬盘"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/22/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 07d4afc5f3eedbdcd4686a64acccf40278f139c4
ms.lasthandoff: 04/03/2017

---

# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli-20"></a>如何使用 Azure CLI 2.0 在 Linux VM 上扩展虚拟硬盘
在 Azure 的 Linux 虚拟机 (VM) 上，操作系统 (OS) 的默认虚拟硬盘大小通常为 30 GB。 可通过[添加数据磁盘](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)来扩充存储空间，也可扩展 OS 磁盘或现有的数据磁盘。 本文详述如何使用 Azure CLI 2.0 扩展 Linux VM 的托管磁盘。 也可使用 [Azure CLI 1.0](expand-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 扩展非托管 OS 磁盘。

## <a name="expand-managed-disk"></a>扩展托管磁盘
确保已安装了最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并已使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。

在以下示例中，请将示例参数名称替换为你自己的值。 示例参数名称包括 `myResourceGroup` 和 `myVM`。

1. 当 VM 正在运行时，无法执行虚拟硬盘上的操作。 使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除分配 VM。 以下示例在名为 `myResourceGroup` 的资源组中解除分配名为 `myVM` 的 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `az vm stop` 不释放计算资源。 若要释放计算资源，请使用 `az vm deallocate`。 只有释放 VM 才能扩展虚拟硬盘。

2. 使用 [az disk list](/cli/azure/disk#list) 查看资源组中的托管磁盘列表。 以下示例显示名为 `myResourceGroup` 的资源组中的托管磁盘列表：

    ```azurecli
    az disk list -g myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    使用 [az disk update](/cli/azure/disk#update) 扩展所需磁盘。 以下示例将名为 `myDataDisk` 的托管磁盘的大小扩展为 `200` GB：

    ```azurecli
    az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 200
    ```

    > [!NOTE]
    > 扩展托管磁盘时，更新的大小会映射到最近的托管磁盘大小。 有关可用托管磁盘大小和层的表，请参阅 [Azure 托管磁盘概述 - 定价和计费](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#pricing-and-billing)。

3. 使用 [az vm start](/cli/azure/vm#start) 启动 VM。 以下示例在名为 `myResourceGroup` 的资源组中启动名为 `myVM` 的 VM：

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. 使用适当的凭据，通过 SSH 登录到 VM。 若要验证是否已调整 OS 磁盘的大小，请使用 `df -h`。 以下示例输出显示主分区 (`/dev/sda1`) 现在为 200 GB：

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        194G   52M   193G   1% /datadrive
    ```

## <a name="next-steps"></a>后续步骤
如需更多存储，也可[向 Linux VM 添加数据磁盘](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 有关磁盘加密的详细信息，请参阅[使用 Azure CLI 加密 Linux VM 上的磁盘](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

