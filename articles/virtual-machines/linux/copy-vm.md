---
title: "使用 Azure CLI 2.0 复制 Linux VM | Microsoft 文档"
description: "了解如何使用 Azure CLI 2.0 和托管磁盘创建 Azure Linux VM 的副本。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 26e09f4e408b92034594215f602d5ca0ff259c5a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-and-managed-disks"></a>使用 Azure CLI 2.0 和托管磁盘创建 Azure Linux VM 的副本


本文说明了如何使用 Azure CLI 2.0 和 Azure 资源管理器部署模型创建运行 Linux 的 Azure 虚拟机 (VM) 的副本。 还可以使用 [Azure CLI 1.0](copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 执行这些步骤。

还可以[上传 VHD 并从中创建 VM](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="prerequisites"></a>先决条件


-   安装 [Azure CLI 2.0](/cli/azure/install-az-cli2)

-   使用 [az login](/cli/azure/#az_login) 登录到一个 Azure 帐户。

-   使用一个 Azure VM 作为副本的来源。

## <a name="step-1-stop-the-source-vm"></a>步骤 1：停止源 VM


使用 [az vm deallocate](/cli/azure/vm#az_vm_deallocate) 解除分配源 VM。
以下示例解除分配资源组**myResourceGroup** 中名为 **myVM** 的 VM：

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>步骤 2：复制源 VM


若要复制 VM，请创建基础虚拟硬盘的副本。 此过程将一个专用 VHD 创建为托管磁盘，其中包含与源 VM 相同的配置和设置。

有关 Azure 托管磁盘的详细信息，请参阅 [Azure 托管磁盘概述](../windows/managed-disks-overview.md)。 

1.  使用 [az vm list](/cli/azure/vm#az_vm_list) 列出每个 VM 及其 OS 磁盘的名称。 以下示例列出了名为 **myResourceGroup** 的资源组中的所有 VM：
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    输出类似于以下示例：

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  通过使用 [az disk create](/cli/azure/disk#az_disk_create) 创建新的托管磁盘来复制磁盘。 以下示例基于名为 **myDisk** 的托管磁盘创建名为 **myCopiedDisk** 的磁盘：

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  现在请使用 [az disk list](/cli/azure/disk#az_disk_list) 验证资源组中的托管磁盘。 以下示例列出了名为 **myResourceGroup** 的资源组中的托管磁盘：

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="step-3-set-up-a-virtual-network"></a>步骤 3：设置虚拟网络


以下可选步骤可创建新的虚拟网络、子网、公共 IP 地址和虚拟网络接口卡 (NIC)。

在复制 VM 来完成故障排除或其他部署操作时，用户可能不希望使用现有虚拟网络中的 VM。

如果希望为复制的 VM 创建虚拟网络基础结构，请按后续几个步骤操作。 如果不希望创建虚拟网络，请跳到[步骤 4：创建 VM](#step-4-create-a-vm)。

1.  使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络。 以下示例创建一个名为 **myVnet** 的虚拟网络和一个名为 **mySubnet** 的子网：

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 创建公共 IP。 以下示例创建一个名为 **myPublicIP** 的公共 IP，其 DNS 名称为 **mypublicdns**。 （DNS 名称必须唯一，因此请提供唯一名称。）

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  使用 [az network nic create](/cli/azure/network/nic#az_network_nic_create) 创建 NIC。
    以下示例创建一个附加到 **mySubnet** 子网且名为 **myNic** 的 NIC：

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="step-4-create-a-vm"></a>步骤 4：创建 VM

现在可使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。

指定复制的要用作 OS 磁盘的托管磁盘 (--attach-os-disk)，如下所示：

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>后续步骤

若要了解如何使用 Azure CLI 管理新 VM，请参阅 [Azure 资源管理器的 Azure CLI 命令](../azure-cli-arm-commands.md)。
