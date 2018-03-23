---
title: 在 Linux VM 上使用 cloud-init 配置交换文件 | Microsoft Docs
description: 如何通过 Azure CLI 2.0 使用 cloud-init 在创建期间在 Linux VM 中配置交换文件
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 88a141922f113caf7ad67c89de48f84a821f7ba3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>在 Linux VM 上使用 cloud-init 配置交换文件
本文介绍如何使用 [cloud-init](https://cloudinit.readthedocs.io) 在各种 Linux 分发版中配置交换文件。 在传统上，交换文件由 Linux 代理 (WALA) 根据分发版的需要进行配置。  本文档概述在预配期间，使用 cloud-init 按需生成交换文件的过程。  有关 cloud-init 如何在 Azure 以及受支持的 Linux 发行版中本机工作的详细信息，请参阅 [cloud-init 概述](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>为基于 Ubuntu 的映像创建交换文件
在 Azure 上，Ubuntu 库映像默认不会创建交换文件。 若要在预配 VM 期间使用 cloud-init 启用交换文件配置，请参阅 Ubuntu wiki 中的 [AzureSwapPartitions 文档](https://wiki.ubuntu.com/AzureSwapPartitions)。

## <a name="create-swapfile-for-redhat-and-centos-based-images"></a>为基于 RedHat 和 CentOS 的映像创建交换文件

在当前 shell 中，创建名为 *cloud_init_swapfile.txt* 的文件并粘贴以下配置。 对于此示例，请在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor cloud_init_swapfile.txt` 以创建文件并查看可用编辑器的列表。 选择 #1 以使用 nano 编辑器。 请确保已正确复制整个 cloud-init 文件，尤其是第一行。  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

在部署此映像之前，需要使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

现在，使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM，并通过 `--custom-data cloud_init_swapfile.txt` 指定 cloud-init 文件，如下所示：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>验证是否已创建交换文件
通过 SSH 连接到 VM 的公共 IP 地址显示在先前命令的输出中。 按如下所示输入自己的 **publicIpAddress**：

```bash
ssh <publicIpAddress>
```

通过 SSH 连接到 VM 后，请验证是否已创建交换文件

```bash
swapon -s
```

此命令的输出应如下所示：

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> 如果在现有的 Azure 映像中配置了交换文件，而你想要更改新映像的交换文件配置，则应删除现有的交换文件。 有关更多详细信息，请参阅“自定义 cloud-init 预配的映像”文档。

## <a name="next-steps"></a>后续步骤
有关配置更改的其他 cloud-init 示例，请参阅以下文章：
 
- [向 VM 添加其他 Linux 用户](cloudinit-add-user.md)
- [运行包管理器以在首次启动时更新现有包](cloudinit-update-vm.md)
- [更改 VM 本地主机名](cloudinit-update-vm-hostname.md) 
- [安装应用程序包、更新配置文件和注入密钥](tutorial-automate-vm-deployment.md)
