---
title: "使用 Azure CLI 2.0 捕获 Linux VM | Microsoft 文档"
description: "如何使用由 Azure CLI 2.0 创建的托管磁盘，捕获和通用化基于 Linux 的 Azure 虚拟机 (VM) 的映像"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 93abc8b1b14f58b0d0be52517a2b63dfe2dc32fb
ms.openlocfilehash: c72f576d992c9adfa83b9744672397045833c43f
ms.lasthandoff: 02/27/2017


---
# <a name="how-to-generalize-and-capture-a-linux-virtual-machine"></a>如何通用化和捕获 Linux 虚拟机
要重用在 Azure 中部署和配置的虚拟机 (VM)，需捕获 VM 的映像。 此过程还涉及从映像部署新 VM 之前，先通用化 VM 以删除个人帐户信息。 本文详细介绍了如何使用 Azure CLI 2.0 为使用 Azure 托管磁盘的 VM 捕获 VM 映像。 这些磁盘由 Azure 平台处理，无需任何准备或位置来存储它们。 有关详细信息，请参阅 [Azure 托管磁盘概述](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 本文详细介绍了如何使用 Azure CLI 2.0 来捕获 Linux VM。 还可以使用 [Azure CLI 1.0](virtual-machines-linux-capture-image-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 执行这些步骤。

> [!TIP]
> 如果要创建具有其专用备份或调试状态的现有 Linux VM 的副本，请参阅[创建在 Azure 上运行的 Linux 虚拟机的副本](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 如果要从本地 VM 上载 Linux VHD，请参阅[上载自定义磁盘映像并从其创建 Linux VM](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。  


## <a name="before-you-begin"></a>开始之前
确保符合以下先决条件：

* **在 Resource Manager 部署模型中创建的 Azure VM** - 如果尚未创建 Linux VM，则可以使用[门户](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[Azure CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [Resource Manager 模板](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 根据需要配置 VM。 例如， [添加数据磁盘](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、应用更新和安装应用程序。 

还需要安装最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) 并已使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。

## <a name="quick-commands"></a>快速命令
如果需要快速完成任务，请参阅以下部分，其中详细说明了在 Azure 中捕获 Linux VM 映像的基本命令。 本文档的余下部分（从[此处](#detailed-steps)开始）提供了每个步骤的更详细信息和上下文。 在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 `myResourceGroup`、`myVM` 和 `myImage`。

1. 取消设置源 VM：

    ```bash
    ssh ops@myvm.westus.cloudapp.azure.com
    sudo waagent -deprovision+user -force
    exit
    ```

2. 使用 [az vm deallocate](/cli/azure/vm#deallocate) 解除分配 VM：

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. 使用 [az vm generalize](/cli/azure/vm#generalize) 通用化 VM：
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

4. 使用 [az image create](/cli/azure/image#create) 从 VM 资源创建映像：
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```

5. 使用 [az vm create](/cli/azure/vm#create) 从你的映像资源创建 VM：

    ```azurecli
    az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="detailed-steps"></a>详细步骤
通过下列步骤可以取消设置现有 VM，解除分配并通用化 VM 资源，然后创建映像。 可以使用此映像，在订阅内的任何资源组中创建 VM。 此过程使 [Azure 托管磁盘](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)比非托管磁盘更具优势。 使用非托管磁盘时，创建基础虚拟硬盘 (VHD) 的 blob 副本之后，便只能在与复制的 VHD blob 相同的存储帐户中创建 VM。 使用托管磁盘时，所创建的映像资源可部署在整个订阅之中。

## <a name="step-1-remove-the-azure-linux-agent"></a>步骤 1：删除 Azure Linux 代理
要使 VM 做好进行通用化的准备，请使用 Azure VM 代理取消设置 VM，以删除文件和数据。 在目标 Linux VM 上，使用带 **deprovision** 参数的 **waagent** 命令。 有关详细信息，请参阅 [Azure Linux 代理用户指南](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

1. 使用 SSH 客户端连接到 Linux VM。
2. 在 SSH 窗口中，键入以下命令：
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > 仅在要捕获为映像的 VM 上运行此命令。 它无法保证映像中的所有敏感信息均已清除，或该映像适合再分发。
 
3. 键入 **y** 继续。 添加 **-force** 参数即可免除此确认步骤。
4. 该命令完成后，键入 **exit**。 此步骤将关闭 SSH 客户端。

## <a name="step-2-capture-the-vm"></a>步骤 2：捕获 VM
使用 Azure CLI 2.0 通用化和捕获 VM。 在以下示例中，请将示例参数名称替换为自己的值。 示例参数名称包括 **myResourceGroup**、**myVnet** 和 **myVM**。

1. 对使用 [az vm deallocate](/cli//azure/vm#deallocate) 取消设置的 VM 解除分配。 以下示例在名为 `myResourceGroup` 的资源组中解除分配名为 `myVM` 的 VM：
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. 使用 [az vm generalize](/cli//azure/vm#generalize) 通用化 VM。 以下示例在名为 `myResourceGroup` 的资源组中通用化名为 `myVM` 的 VM：
   
    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ```

3. 现在，使用 [az image create](/cli//azure/image#create) 创建 VM 资源的映像。 以下示例使用名为 `myVM` 的 VM 资源在名为 `myResourceGroup` 的资源组中创建名为 `myImage` 的映像：
   
    ```azurecli
    az image create --resource-group myResourceGroup --name myImage --source myVM
    ```
   
   > [!NOTE]
   > 该映像在与源 VM 相同的资源组中创建。 可以在订阅内的任何资源组中从此映像创建虚拟机。 从管理角度来看，你可能希望为 VM 资源和映像创建特定的资源组。

## <a name="step-3-create-a-vm-from-the-captured-image"></a>步骤 3：从捕获的映像创建 VM
使用通过 [az vm create](/cli/azure/vm#create) 创建的映像来创建 VM。 以下示例从名为 `myImage` 的映像创建名为 `myVMDeployed` 的 VM：

```azurecli
az vm create --resource-group myResourceGroup --name myVMDeployed --image myImage
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```

使用托管磁盘，可在订阅内的任何资源组中从映像创建 VM。 此行为与非托管磁盘有所不同，在非托管磁盘中你只能在与源 VHD 相同的存储帐户中创建 VM。 要在与映像不同的资源组中创建 VM，请指定映像的完整资源 ID。 使用 [az image list](/cli/azure/image#list) 查看映像列表。 输出类似于以下示例：

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

以下示例使用 **az vm create**，通过指定映像资源 ID，在与源映像不同的资源组中创建 VM：

```azurecli
az vm create --resource-group myOtherResourceGroup --name myOtherVMDeployed 
    --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage"
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub
```


### <a name="verify-the-deployment"></a>验证部署
现在将 SSH 连接到你创建的虚拟机以验证部署并开始使用新的 VM。 要通过 SSH 连接，请使用 [az vm show](/cli/azure/vm#show) 查找 VM 的 IP 地址或 FQDN：

```azurecli
az vm show --resource-group myResourceGroup --name myVM --show-details
```

## <a name="next-steps"></a>后续步骤
可以从源 VM 映像创建多个 VM。 如果需要对映像进行更改，请执行以下操作： 

- 从映像创建 VM。
- 进行任何更新或配置更改。
- 再次执行相关步骤，对 VM 执行取消预配、解除分配、通用化和创建操作。
- 将此新映像用于将来的部署。 如果需要，请删除原始映像。

有关使用 CLI 管理 VM 的详细信息，请参阅 [Azure CLI 2.0](/cli/azure/overview)。

