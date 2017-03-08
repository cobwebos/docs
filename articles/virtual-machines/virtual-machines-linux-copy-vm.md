---
title: "使用 Azure CLI 2.0 复制 Linux VM | Microsoft 文档"
description: "了解如何使用 Azure CLI 2.0（预览版）在 Resource Manager 部署模型中创建 Azure Linux VM 的副本。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1beaca707bfc5058083213b564b3898545f57556
ms.openlocfilehash: edf98951d4ed6709a9894e36ec5ae80b589dd639
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-preview"></a>使用 Azure CLI 2.0（预览版）创建 Linux VM 的副本
本文说明如何使用 Azure Resource Manager 部署模型创建运行 Linux 的 Azure 虚拟机 (VM) 副本。

将操作系统和数据磁盘复制到新容器，然后设置网络资源并创建 VM。

还可以[上载自定义磁盘映像并从中创建 VM](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="cli-versions"></a>CLI 版本
可以使用以下命令行界面 (CLI) 版本之一来完成任务：

* [Azure CLI 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)：适用于经典部署模型和资源管理部署模型。
* Azure CLI 2.0（预览版）：下一代 CLI，适用于本文介绍的资源管理部署模型。

## <a name="prerequisites"></a>先决条件
* [Azure CLI 2.0（预览版）](/cli/azure/install-az-cli2)，已安装并使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。
* Azure VM，用作副本的源。

## <a name="step-1-stop-the-source-vm"></a>步骤 1：停止源 VM
使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除分配源 VM。 以下示例在资源组 `myResourceGroup` 中解除分配 VM `myVM`：

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>步骤 2：复制源 VM
若要复制 VM，请创建基础虚拟硬盘的副本。 用户可以通过此过程创建专用 VM，其中包含与源 VM 相同的配置和设置。

对于 Azure 托管磁盘和非托管磁盘，复制虚拟磁盘的过程是不同的。 托管磁盘由 Azure 平台处理，不需要任何准备，也不需要位置来存储。 托管磁盘是顶级资源，因此更容易使用。 也就是说，可以直接复制磁盘资源。

有关 Azure 托管磁盘的详细信息，请参阅 [Azure 托管磁盘概述](../storage/storage-managed-disks-overview.md)。

请按随后两部分中任一部分的说明操作（具体取决于源 VM 的存储类型），然后转到“步骤 3：设置虚拟网络”。

### <a name="managed-disks"></a>托管磁盘

1. 使用 [az vm list](/cli/azure/vm#list) 列出每个 VM 及其 OS 托管磁盘的名称。 以下示例列出了资源组 `myResourceGroup` 中的所有 VM：

    ```azurecli
    az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
    ```

    输出类似于以下示例：

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
```

2. 若要复制磁盘，请使用 [az disk create](/cli/azure/disk#create) 创建新的托管磁盘。 以下示例基于托管磁盘 `myDisk` 创建磁盘 `myCopiedDisk`：

    ```azurecli
    az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
    ```

3. 现在请使用 [az disk list](/cli/azure/disk#list) 验证资源组中的托管磁盘。 以下示例列出了资源组 `myResourceGroup` 中的托管磁盘：

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```

4. 跳到[“步骤 3：设置虚拟网络”](#set-up-the-virtual-network)。


### <a name="unmanaged-disks"></a>非托管磁盘

1. 若要创建虚拟硬盘的副本，需要 Azure 存储帐户密钥和磁盘的 URI。 若要查看存储帐户密钥，请使用 [az storage account keys list](/cli/azure/storage/account/keys#list)。

 以下示例列出了资源组 `myResourceGroup` 中存储帐户 `mystorageaccount` 的密钥：

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --name mystorageaccount --output table
    ```

 输出类似于以下示例：

    ```azurecli
    KeyName    Permissions    Value
    ---------  -------------  ----------------------------------------------------------------------------------------
    key1       Full           gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug==
    key2       Full           UMoyQjWuKCBUAVDr1ANRe/IWTE2o0ZdmQH2JSZzXKNmDKq83368Fw9nyTBcTEkSKI9cm5tlTL8J15ArbPMo8eA==
    ```

2. 若要查看 VM 及其 URI 的列表，请使用 [az vm list](/cli/azure/vm#list)。 以下示例列出了资源组 `myResourceGroup` 中的 VM：

    ```azurecli
    az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
    ```

    输出类似于以下示例：

    ```azurecli
    Name    URI
    ------  -------------------------------------------------------------
    myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
    ```

3. 使用 [az storage blob copy start](/cli/azure/storage/blob/copy#start) 复制虚拟硬盘。 若要提供所需的存储帐户密钥和虚拟硬盘 URI，请使用 `az storage account keys` 和 `az vm` 列表中的信息。

    ```azurecli
    az storage blob copy start \
        --account-name mystorageaccount \
        --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
        --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
        --destination-container vhds --destination-blob myCopiedVHD.vhd
    ```

## <a name="step-3-set-up-a-virtual-network"></a>步骤 3：设置虚拟网络
以下可选步骤可创建新的虚拟网络、子网、公共 IP 地址和虚拟网络接口卡 (NIC)。

在复制 VM 来完成故障排除或其他部署操作时，用户可能不希望使用现有虚拟网络中的 VM。

如果希望为复制的 VM 创建虚拟网络基础结构，请按后续几个步骤操作。 如果不希望创建虚拟网络，请跳到[“步骤 4：创建 VM”](#create-a-vm)。

1. 使用 [az network vnet create](/cli/azure/network/vnet#create) 创建虚拟网络。 以下示例创建虚拟网络 `myVnet` 和子网 `mySubnet`：

    ```azurecli
    az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
        --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
    ```

2. 使用 [az network public-ip create](/cli/azure/network/public-ip#create) 创建公共 IP。 以下示例创建公共 IP `myPublicIP`，其 DNS 名称为 `mypublicdns`。 （DNS 名称必须唯一，因此请提供唯一名称。）

    ```azurecli
    az network public-ip create --resource-group myResourceGroup --location westus \
        --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
    ```

3. 使用 [az network nic create](/cli/azure/network/nic#create) 创建 NIC。 以下示例创建 NIC `myNic`，附加到 `mySubnet` 子网：

    ```azurecli
    az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
        --vnet-name myVnet --subnet mySubnet
    ```

## <a name="step-4-create-a-vm"></a>步骤 4：创建 VM
现在可使用 [az vm create](/cli/azure/vm#create) 创建 VM。 复制磁盘时，托管磁盘的过程与非托管磁盘的过程稍有不同。 请按随后两部分中任一部分的说明操作（具体取决于源 VM 的存储类型）。

### <a name="managed-disks"></a>托管磁盘
1. 使用 [az vm create](/cli/azure/vm#create) 创建 VM。
2. 指定复制的托管磁盘，将其用作 OS 磁盘（`--attach-os-disk`），如下所示：

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image UbuntuLTS
        --attach-os-disk myCopiedDisk
    ```

### <a name="unmanaged-disks"></a>非托管磁盘
1. 使用 [az vm create](/cli/azure/vm#create) 创建 VM。
2. 指定通过 `az storage blob copy start` (`--image`) 创建复制的磁盘时使用的存储帐户、容器名称、虚拟硬盘，如下所示：

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM  \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
        --use-unmanaged-disk
    ```

## <a name="next-steps"></a>后续步骤
若要了解如何使用 Azure CLI 管理新 VM，请参阅 [Azure Resource Manager 的 Azure CLI 命令](azure-cli-arm-commands.md)。

