---
title: 使用 cloud-init 将用户添加到 Azure 上的 Linux VM | Microsoft Docs
description: 如何通过 Azure CLI 2.0 使用 cloud-init 在创建期间将用户添加到 Linux VM
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
ms.openlocfilehash: ce4421fc8276f215564cb7a171a215cc166c8517
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
ms.locfileid: "29123457"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>使用 cloud-init 将用户添加到 Azure 中的 Linux VM
本文介绍如何在 Azure 中使用 [cloud-init](https://cloudinit.readthedocs.io) 在预配时将用户添加到 Linux 虚拟机 (VM) 或虚拟机规模集 (VMSS)。 Azure 预配资源后，此 cloud-init 脚本将在首次启动时运行。 有关 cloud-init 如何在 Azure 以及受支持的 Linux 发行版中本机工作的详细信息，请参阅 [cloud-init 概述](using-cloud-init.md)。

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>使用 cloud-init 向 VM 添加用户
任何新 Linux VM 的首要任务之一就是，自行添加附加的用户以避免使用 *root*。 SSH 密钥是安全性和可用性的最佳做法。 使用此 cloud-init 脚本将密钥添加到“~/.ssh/authorized_keys”文件。

若要将用户添加到 Linux VM，请在当前 shell 中创建名为 *cloud_init_add_user.txt* 的文件并粘贴以下配置。 对于此示例，请在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor cloud_init_add_user.txt` 以创建文件并查看可用编辑器的列表。 选择 #1 以使用 nano 编辑器。 请确保已正确复制整个 cloud-init 文件，尤其是第一行。  需要提供自己的公钥（例如 *~/.ssh/id_rsa.pub* 的内容）作为 `ssh-authorized-keys:` 的值 - 为简化示例，此处已缩短。

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> #cloud-config 文件包含 `- default` 参数。 这会将用户追加到预配期间创建的现有管理员用户。 如果不 `- default` 参数创建用户 - 将覆盖 Azure 平台自动生成的管理员用户。 

在部署此映像之前，需要使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

现在，使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM，并通过 `--custom-data cloud_init_add_user.txt` 指定 cloud-init 文件，如下所示：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

通过 SSH 连接到 VM 的公共 IP 地址显示在先前命令的输出中。 按如下所示输入自己的 **publicIpAddress**：

```bash
ssh <publicIpAddress>
```

若要确认已将用户添加到 VM 和指定的组，请查看“/etc/group”文件的内容，如下所示：

```bash
cat /etc/group
```

以下示例输出显示“cloud_init_add_user.txt”文件中的用户已被添加至 VM 和相应群组：

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>后续步骤
有关配置更改的其他 cloud-init 示例，请参阅以下文章：
 
- [向 VM 添加其他 Linux 用户](cloudinit-add-user.md)
- [运行包管理器以在首次启动时更新现有包](cloudinit-update-vm.md)
- [更改 VM 本地主机名](cloudinit-update-vm-hostname.md) 
- [安装应用程序包、更新配置文件和注入密钥](tutorial-automate-vm-deployment.md)