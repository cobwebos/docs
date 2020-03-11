---
title: 使用 cloud init 在 Linux VM 上配置交换分区
description: 如何在创建时使用 cloud init 在 Linux VM 中配置交换分区，Azure CLI
author: rickstercdn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1247652e536042ee249054d86aed3c3f8e7aa7bf
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969198"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>使用 cloud init 在 Linux VM 上配置交换分区
本文介绍如何使用[云初始化](https://cloudinit.readthedocs.io)在各种 Linux 发行版上配置交换分区。 交换分区传统上由 Linux 代理（WALA）根据所需的发行版进行配置。  本文档概述了如何在预配期间使用云初始化按需生成交换分区。  有关 cloud-init 如何在 Azure 以及受支持的 Linux 发行版中本机工作的详细信息，请参阅 [cloud-init 概述](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>创建基于 Ubuntu 的映像的交换分区
默认情况下，在 Azure 上，Ubuntu 库映像不创建交换分区。 若要在 VM 预配期间使用 cloud init 启用交换分区配置，请参阅 Ubuntu wiki 上的[AzureSwapPartitions 文档](https://wiki.ubuntu.com/AzureSwapPartitions)。

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>为基于 Red Hat 和 CentOS 的图像创建交换分区

在当前 shell 中创建一个名为*cloud_init_swappart .txt*的文件并粘贴以下配置。 对于此示例，请在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor cloud_init_swappart.txt` 以创建文件并查看可用编辑器的列表。 选择 #1 以使用 nano 编辑器。 请确保已正确复制整个 cloud-init 文件，尤其是第一行。  

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

在部署此映像之前，需要使用 [az group create](/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

现在，使用 [az vm create](/cli/azure/vm) 创建 VM，并通过 `--custom-data cloud_init_swappart.txt` 指定 cloud-init 文件，如下所示：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>验证是否已创建交换分区
通过 SSH 连接到 VM 的公共 IP 地址显示在先前命令的输出中。 按如下所示输入自己的 **publicIpAddress**：

```bash
ssh <publicIpAddress>
```

SSH'ed 到 vm 后，请检查是否已创建交换分区

```bash
swapon -s
```

此命令的输出应如下所示：

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> 如果现有的 Azure 映像已配置交换分区，并且要更改新映像的交换分区配置，则应删除现有的交换分区。 有关更多详细信息，请参阅“自定义 cloud-init 预配的映像”文档。

## <a name="next-steps"></a>后续步骤
有关配置更改的其他 cloud-init 示例，请参阅以下文章：
 
- [向 VM 添加其他 Linux 用户](cloudinit-add-user.md)
- [运行包管理器以在首次启动时更新现有包](cloudinit-update-vm.md)
- [更改 VM 本地主机名](cloudinit-update-vm-hostname.md) 
- [安装应用程序包、更新配置文件和注入密钥](tutorial-automate-vm-deployment.md)
