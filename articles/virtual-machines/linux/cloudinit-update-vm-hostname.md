---
title: "在 Azure 中使用 cloud-init 设置 Linux VM 的主机名 | Microsoft Docs"
description: "如何通过 Azure CLI 2.0 使用 cloud-init 在创建期间自定义 Linux VM"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 7455748b2e08104dadfed8cfe41581a400539d4f
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>在 Azure 中使用 cloud-init 设置 Linux VM 的主机名
本文演示如何在 Azure 中使用 [cloud-init](https://cloudinit.readthedocs.io) 在预配时间配置虚拟机 (VM) 或虚拟机规模集 (VMSS) 上特定的主机名。 Azure 预配资源后，这些 cloud-init 脚本即会在第一次启动时运行。 有关 cloud-init 如何在 Azure 以及受支持的 Linux 发行版中本机工作的详细信息，请参阅 [cloud-init 概述](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>使用 cloud-init 设置主机名称
默认情况下，在 Azure 中创建新的虚拟机时，主机名和 VM 名称相同。  如果希望在 Azure 中使用 [az vm create](/cli/azure/vm#create) 创建 VM 时通过运行 cloud-init 脚本来更改默认主机名，请通过 `--custom-data` 开关指定 cloud-init 文件。  

要查看操作中的升级进程，请在当前 shell 中创建一个名为“cloud_init_hostname.txt”的文件并粘贴下面的配置。 对于此示例，请在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor cloud_init_hostname.txt` 以创建文件并查看可用编辑器的列表。 选择 #1 以使用 nano 编辑器。 请确保已正确复制整个 cloud-init 文件，尤其是第一行。  

```yaml
#cloud-config
hostname: myhostname
```

在部署此映像之前，需要使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

现在，使用 [az vm create](/cli/azure/vm#create) 创建 VM，并通过 `--custom-data cloud_init_hostname.txt` 指定 cloud-init 文件，如下所示：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

创建后，Azure CLI 将显示有关 VM 的信息。 使用 `publicIpAddress` 通过 SSH 连接到 VM。 按如下所示输入自己的地址：

```bash
ssh <publicIpAddress>
```

若要查看 VM 名称，请使用 `hostname` 命令，如下所示：

```bash
hostname
```

VM 应将主机名称报告为在 cloud-init 文件中设置的值，如以下示例输出中所示：

```bash
myhostname
```

## <a name="next-steps"></a>后续步骤
有关配置更改的其他 cloud-init 示例，请参阅以下内容：
 
- [向 VM 添加其他 Linux 用户](cloudinit-add-user.md)
- [运行包管理器以在首次启动时更新现有包](cloudinit-update-vm.md)
- [更改 VM 本地主机名](cloudinit-update-vm-hostname.md) 
- [安装应用程序包、更新配置文件和注入密钥](tutorial-automate-vm-deployment.md)