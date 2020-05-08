---
title: 使用 Azure CLI 复制 Linux VM
description: 了解如何使用 Azure CLI 和托管磁盘创建 Azure Linux VM 的副本。
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: legacy
ms.openlocfilehash: 406eda6902ae451c7fdf79e1bd1215c035a66750
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792218"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>使用 Azure CLI 和托管磁盘创建 Azure Linux VM 的副本

本文介绍如何使用 Azure CLI 创建运行 Linux 的 Azure 虚拟机（VM）的副本。 若要大规模复制、创建、存储和共享 VM 映像，请参阅[共享映像库](shared-images.md)。

还可以[上传 VHD 并从中创建 VM](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="prerequisites"></a>先决条件

-   安装 [Azure CLI](/cli/azure/install-az-cli2)。

-   使用 [az login](/cli/azure/reference-index#az-login) 登录到一个 Azure 帐户。

-   使用一个 Azure VM 作为副本的来源。

## <a name="stop-the-source-vm"></a>停止源 VM

使用 [az vm deallocate](/cli/azure/vm#az-vm-deallocate) 解除分配源 VM。
以下示例解除分配资源组*myResourceGroup* 中名为 *myVM* 的 VM：

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>复制源 VM

若要复制 VM，请创建基础虚拟硬盘的副本。 此过程将一个专用虚拟硬盘 (VHD) 创建为托管磁盘，其中包含与源 VM 相同的配置和设置。

有关 Azure 托管磁盘的详细信息，请参阅 [Azure 托管磁盘概述](../windows/managed-disks-overview.md)。 

1.  使用 [az vm list](/cli/azure/vm#az-vm-list) 列出每个 VM 及其 OS 磁盘的名称。 以下示例列出了名为 *myResourceGroup* 的资源组中的所有 VM：
    
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

1.  通过使用 [az disk create](/cli/azure/disk#az-disk-create) 创建新的托管磁盘来复制磁盘。 以下示例基于名为 *myDisk* 的托管磁盘创建名为 *myCopiedDisk* 的磁盘：

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  现在请使用 [az disk list](/cli/azure/disk#az-disk-list) 验证资源组中的托管磁盘。 以下示例列出了名为 *myResourceGroup* 的资源组中的托管磁盘：

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>设置虚拟网络

以下可选步骤可创建新的虚拟网络、子网、公共 IP 地址和虚拟网络接口卡 (NIC)。

在复制 VM 来完成故障排除或其他部署操作时，用户可能不希望使用现有虚拟网络中的 VM。

如果希望为复制的 VM 创建虚拟网络基础结构，请按后续几个步骤操作。 如果不希望创建虚拟网络，请跳到[创建 VM](#create-a-vm)。

1.  使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 创建虚拟网络。 以下示例创建一个名为 *myVnet* 的虚拟网络和一个名为 *mySubnet* 的子网：

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 创建公共 IP。 以下示例创建一个名为 *myPublicIP* 的公共 IP，其 DNS 名称为 *mypublicdns*。 （由于 DNS 名称必须唯一，因此请提供唯一名称。）

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 创建 NIC。
    以下示例创建一个附加到 *mySubnet* 子网且名为 *myNic* 的 NIC：

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>创建 VM

使用 [az vm create](/cli/azure/vm#az-vm-create) 创建 VM。

指定复制的托管磁盘，将其用作 OS 磁盘（`--attach-os-disk`），如下所示：

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>后续步骤

了解如何使用[共享映像库](shared-images.md)管理 VM 映像。
