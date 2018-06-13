---
title: 创建和使用适用于 Azure 中 Linux VM 的 SSH 密钥对 | Microsoft Docs
description: 如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥-私钥对，提高身份验证过程的安全性。
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: iainfou
ms.openlocfilehash: 137fb13ff286e5284b8e8910834913ec9f1d48a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2018
ms.locfileid: "31602624"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>快速步骤：创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥-私钥对
使用安全外壳 (SSH) 密钥对，可以在 Azure 上创建使用 SSH 密钥进行身份验证的虚拟机 (VM)，从而无需密码即可登录。 本文介绍如何快速生成和使用适用于 Linux VM 的 SSH 公钥-私钥文件对。 可使用 Azure Cloud Shell、macOS 或 Linux 主机或者适用于 Linux 的 Windows 子系统以及其他支持 OpenSSH 的工具完成这些步骤。 

有关详细背景和示例，请参阅[创建 SSH 密钥对的详细步骤](create-ssh-keys-detailed.md)。

有关在 Windows 计算机上生成和使用 SSH 密钥的其他方式，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](ssh-from-windows.md)。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对
使用 `ssh-keygen` 命令生成 `~/.ssh` 目录中默认创建的 SSH 公钥和私钥文件。 系统提示时，可指定不同的位置和其他通行短语（用于访问私钥文件的密码）。 如果当前位置存在 SSH 密钥对，这些文件将被覆盖。

```bash
ssh-keygen -t rsa -b 2048
```

如果使用 [Azure CLI 2.0](/cli/azure) 创建 VM，则可以选择通过运行具有 `--generate-ssh-keys` 选项的 [az vm create](/cli/azure/vm#az_vm_create) 命令生成 SSH 公钥和私钥文件。 密钥存储在 ~/.ssh 目录中。 请注意，如果该位置已存在密钥，此命令选项不会覆盖这些密钥。

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>部署 VM 时提供 SSH 公钥
若要创建使用 SSH 密钥进行身份验证的 Linux VM，请在使用 Azure 门户、CLI、资源管理器模板或其他方法创建 VM 时指定 SSH 公钥：

* [使用 Azure 门户创建 Linux 虚拟机](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure CLI 创建 Linux 虚拟机](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure 模板创建 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

如果不熟悉 SSH 公钥的格式，则可通过运行 `cat` 来查看公钥（如下所示），注意需将 `~/.ssh/id_rsa.pub` 替换成自己的公钥文件位置：

```bash
cat ~/.ssh/id_rsa.pub
```

如果复制和粘贴要在 Azure 门户或 Resource Manager 模板中使用的公钥文件的内容，请确保不复制额外的空格。 例如，如果使用 macOS，则可将公钥文件（默认为 `~/.ssh/id_rsa.pub`）通过管道传送到 pbcopy，以便复制内容（也可通过其他 Linux 程序执行此类操作，例如 xclip）。

放置在 Azure 中 Linux VM 上的公钥默认存储在 `~/.ssh/id_rsa.pub` 中，除非在创建密钥时更改了位置。 如果借助现有公钥使用 [Azure CLI 2.0](/cli/azure) 创建 VM，请通过运行具有 `--ssh-key-value` 选项的 [az vm create](/cli/azure/vm#az_vm_create) 命令来指定此公钥的值或位置。 

## <a name="ssh-to-your-vm"></a>通过 SSH 连接到 VM
凭借部署在 Azure VM 上的公钥和本地系统上的私钥，使用 VM 的 IP 地址或 DNS 名称通过 SSH 连接到 VM。 将以下命令中的 azureuser 和 myvm.westus.cloudapp.azure.com 替换为管理员用户名和完全限定的域名（或 IP 地址）：

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

如果在创建密钥对时提供的是通行短语，则在登录过程中遇到提示时，请输入该通行短语。 （服务器添加到 `~/.ssh/known_hosts` 文件夹。系统不会要求再次进行连接，除非更改了 Azure VM 上的公钥，或者从 `~/.ssh/known_hosts` 中删除了服务器名称。）

使用 SSH 密钥创建的 VM 默认配置为禁用密码，使得强力猜测尝试代价相当高昂，因此也更为困难。 

## <a name="next-steps"></a>后续步骤

本文介绍如何创建一个简单的、可以快速使用的 SSH 密钥对。 

* 如果需要使用 SSH 密钥对的详细帮助，请参阅[创建和管理 SSH 密钥对的详细步骤](create-ssh-keys-detailed.md)。

* 如果使用 SSH 连接 Azure VM 时遇到问题，请参阅[排除使用 SSH 连接 Azure Linux VM 的故障](troubleshoot-ssh-connection.md)。


