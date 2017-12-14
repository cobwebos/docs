---
title: "在 Azure 上的 Linux VM 中使用 cloud-init 运行 bash 脚本 | Microsoft Docs"
description: "如何通过 Azure CLI 2.0 使用 cloud-init 在创建期间在 Linux VM 中运行 bash 脚本"
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
ms.openlocfilehash: 1fcc432e8437a7fd284a75aa40454848a2af3006
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>在 Azure 上的 Linux VM 中使用 cloud-init 运行 bash 脚本
本文介绍如何在 Azure 中使用 [cloud-init](https://cloudinit.readthedocs.io) 在预配时在 Linux 虚拟机 (VM) 或虚拟机规模集 (VMSS) 中运行现有的 bash 脚本。 Azure 预配资源后首次启动时，会运行这些 cloud-init 脚本。 有关 cloud-init 如何在 Azure 以及受支持的 Linux 发行版中本机工作的详细信息，请参阅 [cloud-init 概述](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>使用 cloud-init 运行 bash 脚本
使用 cloud-init 时，不需要将现有的脚本转换为 cloud-config，因为 cloud-init 接受多种输入类型，其中一种类型就是 bash 脚本。

如果已在使用 Linux 自定义脚本 Azure 扩展运行脚本，则可以迁移它们以使用 cloud-init。 但是，Azure 扩展已集成报告功能，当脚本失败时会发出警报，因此如果脚本失败，cloud-init 映像部署不会失败。

若要了解此功能的工作原理，请创建一个简单的 bash 脚本用于测试。 与 cloud-init `#cloud-config` 文件一样，此脚本必须位于预配虚拟机时运行 AzureCLI 命令的本地位置。  对于此示例，请在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor simple_bash.sh` 以创建文件并查看可用编辑器的列表。 选择“#1”以使用 **nano** 编辑器。 请确保已正确复制整个 cloud-init 文件，尤其是第一行。  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

在部署此映像之前，需要使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

现在，使用 [az vm create](/cli/azure/vm#create) 创建 VM，并通过 `--custom-data simple_bash.sh` 指定 bash 脚本文件，如下所示：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>验证 bash 脚本是否已运行
通过 SSH 连接到 VM 的公共 IP 地址显示在先前命令的输出中。 按如下所示输入自己的 **publicIpAddress**：

```bash
ssh <publicIpAddress>
```

切换到 **/tmp** 目录，检查该 myScript.txt 文件是否存在并且包含相应的文本。  如果不是，请检查 **/var/log/cloud-init.log** 了解更多详细信息。  搜索以下条目：

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>后续步骤
有关配置更改的其他 cloud-init 示例，请参阅以下文章：
 
- [向 VM 添加其他 Linux 用户](cloudinit-add-user.md)
- [运行包管理器以在首次启动时更新现有包](cloudinit-update-vm.md)
- [更改 VM 本地主机名](cloudinit-update-vm-hostname.md) 
- [安装应用程序包、更新配置文件和注入密钥](tutorial-automate-vm-deployment.md)