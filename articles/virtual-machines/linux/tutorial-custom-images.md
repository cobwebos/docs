---
title: "使用 Azure CLI 创建自定义 VM 映像 | Microsoft Docs"
description: "教程 - 使用 Azure CLI 创建自定义 VM 映像。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 2ce92b3f0a21f80eb4294161d6d3a5275c992600
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>使用 CLI 创建 Azure VM 的自定义映像

本教程介绍如何定义自己 Azure 虚拟机的自定义映像。 自定义映像使你能够使用已配置的映像创建 VM。 自定义映像可用于启动二进制文件和应用程序、应用程序配置、VM 数据磁盘定义以及其他 OS 配置的预加载。 创建自定义映像时，自定义的 VM 和所有附加磁盘均包括在映像中。

可使用最新版 [Azure CLI 2.0](/cli/azure/install-azure-cli) 完成本教程中的步骤。

## <a name="before-you-begin"></a>开始之前

下列步骤详细说明如何将现有 VM 转换为可重用自定义映像，以便用于创建新 VM 实例。

若要完成本教程中的示例，必须具备现有虚拟机。 如果需要，此[脚本示例](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md)可替你创建一个虚拟机。 通过教程操作时，根据需要替换资源组和 VM 名称。

## <a name="prepare-vm"></a>准备 VM

若要创建虚拟机的映像，需通过以下方式准备 VM：取消设置、解除分配，然后将源 VM 标记为通用化。

### <a name="deprovision-the-vm"></a>取消设置 VM 

取消设置可通过删除特定于计算机的信息通用化 VM。 通过此通用化，可从单个映像中部署多个 VM。 在取消预配期间，主机名将重置为“localhost.localdomain”。 还会删除 SSH 主机密钥、名称服务器配置、根密码和缓存的 DHCP 租约。

若要取消设置 VM，请使用 Azure VM 代理 (waagent)。 Azure VM 代理安装在 VM 上，并管理预配及其与 Azure 结构控制器的交互。 有关详细信息，请参阅 [Azure Linux 代理用户指南](agent-user-guide.md)。

使用 SSH 连接到 VM 并运行命令以取消设置 VM。 使用 `+user` 参数还会删除上次设置的用户帐户和任何关联数据。 将示例 IP 地址替换为 VM 的公共 IP 地址。

SSH 到 VM。
```bash
ssh azureuser@52.174.34.95
```
取消预配 VM。

```bash
sudo waagent -deprovision+user -force
```
关闭 SSH 会话。

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>解除分配并将 VM 标记为通用化

若要创建映像，需要解除分配 VM。 使用 [az vm deallocate](/cli//azure/vm#deallocate) 解除分配 VM。 
   
```azurecli
az vm deallocate --resource-group myRGCaptureImage --name myVM
```

最后，使用 [az vm generalize](/cli//azure/vm#generalize) 将 VM 的状态设置为“已通用化”，以便 Azure 平台知道已通用化 VM。 只能从通用化 VM 创建映像。
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

## <a name="create-the-image"></a>创建映像

现在，可使用 [az image create](/cli//azure/image#create) 创建 VM 的映像。 以下示例从名为“myVM”的 VM 创建名为“myImage”的映像。
   
```azurecli
az image create \
    --resource-group myResourceGroupImages \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>从映像创建 VM

在具有映像后，便可以使用 [az vm create](/cli/azure/vm#create) 从映像创建一个或多个新 VM。 以下示例从名为“myImage”的映像创建名为“myVMfromImage”的映像。

```azurecli
az vm create \
    --resource-group myResourceGroupImages \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何创建自定义 VM 映像。 请转到下一教程，了解如何创建高度可用的虚拟机。

[创建高度可用的 VM](tutorial-availability-sets.md)。


