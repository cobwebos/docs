---
title: "使用 Azure CLI 2.0（预览版）复制 Linux VM | Microsoft Docs"
description: "了解如何使用 Azure CLI 2.0（预览版）在 Resource Manager 部署模型中创建 Azure Linux 虚拟机的副本"
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
ms.sourcegitcommit: 344949f10929d9a1ba8f63c5b30f242ddc66fdec
ms.openlocfilehash: 967e2bafdc702c9ddba634089899afee382c5b71


---
# <a name="create-a-copy-of-a-linux-virtual-machine-with-the-azure-cli-20-preview"></a>使用 Azure CLI 2.0（预览版）创建 Linux 虚拟机副本
本文说明如何使用 Resource Manager 部署模型创建运行 Linux 的 Azure 虚拟机 (VM) 副本。 首先，通过操作系统和数据磁盘复制到新容器，然后设置网络资源并创建虚拟机。

还可以[上载自定义磁盘映像并从中创建 VM](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本
可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 适用于经典部署模型和资源管理部署模型的 CLI
- Azure CLI 2.0（预览版）- 适用于资源管理部署模型（本文）的下一代 CLI

## <a name="prerequisites"></a>先决条件
- 需要最新的 [Azure CLI 2.0（预览版）](/cli/azure/install-az-cli2)并已使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。
- 在创建副本时，需要将 Azure VM 用作源。

## <a name="stop-the-vm"></a>停止 VM
使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除分配源 VM。 以下示例在名为 `myResourceGroup` 的资源组中解除分配名为 `myVM` 的 VM：

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="copy-the-vm"></a>复制 VM
若要复制 VM，请创建基础虚拟硬盘的副本。 此过程允许创建包含与源 VM 具有相同配置和设置的专用 VM。 对于 Azure 托管磁盘和非托管磁盘，复制虚拟磁盘的步骤是不同的。 托管磁盘由 Azure 平台处理，无需任何准备或位置来存储它们。 由于托管磁盘是顶级资源，因此更容易使用 - 可以直接复制磁盘资源。 有关 Azure 托管磁盘的详细信息，请参阅 [Azure 托管磁盘概述](../storage/storage-managed-disks-overview.md)。 为源 VM 的存储类型选择以下适当步骤之一：

- [托管磁盘](#managed-disks)
- [非托管磁盘](#unmanaged-disks) 

### <a name="managed-disks"></a>托管磁盘
使用 [az vm list](/cli/azure/vm#list) 列出每个 VM 及其 OS 托管磁盘的名称。 以下示例列出了名为 `myResourceGroup` 的资源组中的所有 VM：

```azurecli
az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
```

输出类似于以下示例：

```azurecli
Name    DiskName
------  --------
myVM    myDisk
```

通过使用 [az disk create](/cli/azure/disk#create) 创建新的托管磁盘来复制磁盘。 以下示例基于名为 `myDisk` 的托管磁盘创建名为 `myCopiedDisk` 的磁盘：

```azurecli
az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
``` 

使用 [az disk list](/cli/azure/disk#list) 验证资源组中的托管磁盘。 以下示例列出了名为 `myResourceGroup` 的资源组中的托管磁盘：

```azurecli
az disk list --resource-group myResourceGroup --output table
```

移至[创建和查看虚拟网络设置](#set-up-the-virtual-network)。


### <a name="unmanaged-disks"></a>非托管磁盘
若要创建 VHD 的副本，需要存储帐户密钥和磁盘的 URI。 使用 [az storage account keys list](/cli/azure/storage/account/keys#list) 查看存储帐户密钥。 以下示例列出了名为 `myResourceGroup` 的资源组中名为 `mystorageaccount` 的存储帐户的密钥

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

使用 [az vm list](/cli/azure/vm#list) 查看 VM 列表及其 URI。 以下示例列出了名为 `myResourceGroup` 的资源组中的 VM：

```azurecli
az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
```

输出类似于以下示例：

```azurecli
Name    URI
------  -------------------------------------------------------------
myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
```

使用 [az storage blob copy start](/cli/azure/storage/blob/copy#start) 复制 VHD。 使用 **az storage account keys list** 和 **az vm list** 中的信息提供所需的存储帐户密钥和 VHD URI。

```azurecli
az storage blob copy start \
    --account-name mystorageaccount \
    --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
    --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
    --destination-container vhds --destination-blob myCopiedVHD.vhd
```

## <a name="set-up-the-virtual-network"></a>设置虚拟网络
以下步骤可创建新的虚拟网络、子网、公共 IP 地址和虚拟网络接口卡 (NIC)。 这些步骤是可选的。 如果要复制虚拟机以进行故障排除或进行其他部署，建议不要在现有虚拟网络中使用 VM。 如果希望为复制的 VM 创建虚拟网络基础结构，请按照以下几个步骤操作，否则请跳至[创建虚拟机](#create-a-vm)。

使用 [az network vnet create](/cli/azure/network/vnet#create) 创建虚拟网络。 以下示例创建一个名为 `myVnet` 的虚拟网络和名为 `mySubnet` 的子网：

```azurecli
az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
    --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

使用 [az network public-ip create](/cli/azure/network/public-ip#create) 创建公共 IP。 以下示例创建一个名为 `myPublicIP` 的公共 IP，其 DNS 名称为 `mypublicdns`。 （DNS 名称必须唯一，因此，请提供自己的唯一名称。）

```azurecli
az network public-ip create --resource-group myResourceGroup --location westus \
    --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

使用 [az network nic create](/cli/azure/network/nic#create) 创建网络接口卡 (NIC)。 以下示例将创建一个名为 `myNic` 的 NIC，附加到 `mySubnet` 子网：

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
    --vnet-name myVnet --subnet mySubnet
```

## <a name="create-a-vm"></a>创建 VM
现在可使用 [az vm create](/cli/azure/vm#create) 创建 VM。 与复制磁盘一样，对于托管磁盘和非托管磁盘，此过程稍有不同。 使用以下适当命令之一创建 VM。

### <a name="managed-disks"></a>托管磁盘
使用 [az vm create](/cli/azure/vm#create) 创建 VM。 指定复制的托管磁盘以用作 OS 磁盘（`--attach-os-disk`），如下所示：

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image UbuntuLTS
    --attach-os-disk myCopiedDisk
```

### <a name="unmanaged-disks"></a>非托管磁盘
使用 [az vm create](/cli/azure/vm#create) 创建 VM。 通过 **az storage blob copy start** (`--image`) 指定在创建副本时使用的存储帐户、容器名称和 VHD，如下所示：

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM  \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
    --use-unmanaged-disk
```

## <a name="next-steps"></a>后续步骤
若要了解如何使用 Azure CLI 管理新虚拟机，请参阅 [Azure CLI commands for the Azure Resource Manager](azure-cli-arm-commands.md)（Azure Resource Manager 的 Azure CLI 命令）。




<!--HONumber=Feb17_HO2-->


