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
ms.date: 04/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: f7fd7d63e6bb1da7022cef782e3b84ea20a64c31
ms.lasthandoff: 04/26/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>使用 CLI 创建 Azure VM 的自定义映像

本教程介绍如何创建自己的 Azure 虚拟机自定义映像。 自定义映像类似于应用商店映像，不同的是自定义映像的创建者是你自己。 自定义映像可用于启动配置，例如预加载应用程序、应用程序配置和其他 OS 配置。 创建自定义映像时，VM 和所有附加磁盘均包括在映像中。 

可使用最新版 [Azure CLI 2.0](/cli/azure/install-azure-cli) 完成本教程中的步骤。

若要完成本教程中的示例，必须具备现有虚拟机。 如果需要，此[脚本示例](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md)可替你创建一个虚拟机。 通过教程操作时，根据需要替换资源组和 VM 名称。

## <a name="create-an-image"></a>创建映像

若要创建虚拟机的映像，需通过以下方式准备 VM：取消设置、解除分配，然后将源 VM 标记为通用化。

### <a name="deprovision-the-vm"></a>取消设置 VM 

取消设置可通过删除特定于计算机的信息通用化 VM。 通过此通用化，可从单个映像中部署多个 VM。 取消设置期间，主机名重置为 `localhost.localdomain`。 还会删除 SSH 主机密钥、名称服务器配置、根密码和缓存的 DHCP 租约。

若要取消设置 VM，请使用 Azure VM 代理 (waagent)。 Azure VM 代理安装在 VM 上，并管理预配及其与 Azure 结构控制器的交互。 有关详细信息，请参阅 [Azure Linux 代理用户指南](agent-user-guide.md)。

使用 SSH 连接到 VM 并运行命令以取消设置 VM。 使用 `+user` 参数还会删除上次设置的用户帐户和任何关联数据。 将示例 IP 地址替换为 VM 的公共 IP 地址。

```bash
ssh azureuser@52.174.34.95 sudo waagent -deprovision+user -force
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

最后，使用 [az vm generalize](/cli//azure/vm#generalize) 将 VM 状态设置为通用化。
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

### <a name="capture-the-image"></a>捕获映像

现在，可使用 [az image create](/cli//azure/image#create) 创建 VM 的映像。 以下示例从名为 `myVM` 的 VM 创建名为 `myImage` 的映像。
   
```azurecli
az image create --resource-group myResourceGroupImages --name myImage --source myVM
```
 
## <a name="create-a-vm-from-an-image"></a>从映像创建 VM

可结合使用映像和 [az vm create](/cli/azure/vm#create) 创建 VM。 以下示例从名为 `myImage` 的映像创建名为 `myVMfromImage` 的 VM。

```azurecli
az vm create --resource-group myResourceGroupImages --name myVMfromImage --image myImage --admin-username azureuser --generate-ssh-keys
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何创建自定义 VM 映像。 请转到下一教程，了解如何创建高度可用的虚拟机。

[创建高度可用的 VM](tutorial-availability-sets.md)。


