---
title: "使用 cloud-init 自定义 Linux VM | Microsoft 文档"
description: "如何通过 Azure CLI 2.0 使用 cloud-init 在创建期间自定义 Linux VM"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>使用 cloud-init 在 Azure 中自定义 Linux VM
本文说明如何在 Azure 中的虚拟机 (VM) 上使用 [cloud-init](https://cloudinit.readthedocs.io) 设置主机名称、更新包以及管理用户帐户。 使用 Azure CLI 2.0 创建 VM 时，这些 cloud-init 脚本将在启动时运行。 有关如何安装应用程序、写入配置文件和插入密钥保管库中密钥的更详细概述，请参阅[本教程](tutorial-automate-vm-deployment.md)。 也可以使用 [Azure CLI 1.0](using-cloud-init-nodejs.md) 执行这些步骤。


## <a name="cloud-init-overview"></a>Cloud-init 概述
[Cloud-init](https://cloudinit.readthedocs.io) 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 安装程序包和写入文件，或者配置用户和安全。 在初始启动期间运行 cloud-init 时，无需额外的步骤且无需代理来应用配置。

Cloud-init 还支持不同的分发。 例如，不要使用 apt-get 安装或 yum 安装来安装包。 可定义要安装的程序包的列表。 Cloud-init 将为所选发行版自动使用本机包管理工具。

我们正在与合作伙伴协作，将 cloud-init 纳入用户向 Azure 提供的映像中并使其在映像中正常运行。 下表概述了 cloud-init 当前在 Azure 平台映像上的可用性：

| 别名 | 发布者 | 产品 | SKU | 版本 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04-LTS |最新 |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |最新 |
| CoreOS |CoreOS |CoreOS |Stable |最新 |


## <a name="set-the-hostname-with-cloud-init"></a>使用 cloud-init 设置主机名称
以 [YAML](http://www.yaml.org) 格式写入 cloud-init 文件。 若要使用 [az vm create](/cli/azure/vm#create) 在 Azure 中创建 VM 时运行 cloud-init 脚本，则通过 `--custom-data` 开关指定 cloud-init 文件。 让我们看一些可以使用 cloud-init 文件配置的示例。 一种常见方案是设置 VM 的主机名称。 默认情况下，主机名称与 VM 名称相同。 

首先，使用 [az group create](/cli/azure/group#create) 创建资源组。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组：

```azurecli
az group create --name myResourceGroup --location eastus
```

在当前 shell 中，创建一个名为“cloud_init_hostname.txt”的文件并粘贴下面的配置。 例如，在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 在 Cloud Shell 中，输入 `sensible-editor cloud_init_hostname.txt` 以创建文件并查看可用编辑器的列表。 请确保已正确复制整个 cloud-init 文件，尤其是第一行：

```yaml
#cloud-config
hostname: myhostname
```

现在，使用 [az vm create](/cli/azure/vm#create) 创建 VM，并通过 `--custom-data cloud_init_hostname.txt` 指定 cloud-init 文件，如下所示：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

创建后，Azure CLI 将显示有关 VM 的信息。 使用 `publicIpAddress` 通过 SSH 连接到 VM。 按如下所示输入自己的地址：

```bash
ssh azureuser@publicIpAddress
```

若要查看 VM 名称，请使用 `hostname` 命令，如下所示：

```bash
hostname
```

VM 应将主机名称报告为在 cloud-init 文件中设置的值，如以下示例输出中所示：

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>使用 cloud-init 更新 VM
出于安全目的，你需要配置 VM，以便在首次启动时应用最新的更新。 由于 cloud-init 支持不同 Linux 发行版，因此无需为包管理器指定 `apt` 或 `yum`。 相反，你需要定义 `package_upgrade`，并让 cloud-init 进程确定正在使用的发行版的适当机制。 此工作流允许跨发行版使用相同的 cloud-init 脚本。

若要查看在操作中的升级过程，请创建一个名为 *cloud_init_upgrade.txt* 的 cloud-init 文件并粘贴下列配置：

```yaml
#cloud-config
package_upgrade: true
```

现在，使用 [az vm create](/cli/azure/vm#create) 创建 VM，并通过 `--custom-data cloud_init_upgrade.txt` 指定 cloud-init 文件，如下所示：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

通过 SSH 连接到 VM 的公共 IP 地址显示在先前命令的输出中。 按如下所示输入自己的公共 IP 地址：

```bash
ssh azureuser@publicIpAddress
```

运行包管理工具并检查更新。 以下示例在 Ubuntu VM 上使用 `apt-get`。

```bash
sudo apt-get upgrade
```

Cloud-init 在启动时检查和安装更新后，没有要应用的更新，如下例输出所示：

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>使用 cloud-init 向 VM 添加用户
任何新 Linux VM 的首要任务之一就是，为自己添加用户以避免使用“root”。 SSH 密钥是安全性和可用性的最佳做法。 使用此 cloud-init 脚本将密钥添加到“~/.ssh/authorized_keys”文件。

若要将用户添加到 Linux VM，请创建一个名为“cloud_init_add_user.txt”的文件并粘贴以下配置。 为“ssh-authorized-keys”提供你自己的公钥（例如“~/.ssh/id_rsa.pub”的内容）。

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

现在，使用 [az vm create](/cli/azure/vm#create) 创建 VM，并通过 `--custom-data cloud_init_add_user.txt` 指定 cloud-init 文件，如下所示：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

通过 SSH 连接到 VM 的公共 IP 地址显示在先前命令的输出中。 按如下所示输入自己的公共 IP 地址：

```bash
ssh myadminuser@publicIpAddress
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
Cloud-init 是一种在 Linux VM 启动时对其进行修改的标准方法。 在 Azure 中，你还可以使用 VM 扩展在 Linux VM 启动时或开始运行后对其进行修改。 例如，你可以使用 Azure VM 扩展在运行中的 VM 上执行脚本，而不只是在首次启动时执行。 有关 VM 扩展的详细信息，请参阅 [VM 扩展和功能](extensions-features.md)，或者，例如有关如何使用扩展，请参阅[使用 VMAccess 扩展管理用户、SSH 并查看或修复 Azure Linux VM 上的磁盘](using-vmaccess-extension.md)。