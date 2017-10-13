---
title: "创建和使用适用于 Azure 中 Linux VM 的 SSH 密钥对 | Microsoft Docs"
description: "如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对，提高身份验证过程的安全性。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2017
ms.author: iainfou
ms.openlocfilehash: 6fcdcc96c7762e2362aebf909ef25f4a5ab62f99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-use-an-ssh-public-and-private-key-pair-for-linux-vms-in-azure"></a>如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对
使用安全外壳 (SSH) 密钥对，可以在 Azure 上创建使用 SSH 密钥进行身份验证的虚拟机 (VM)，从而无需密码即可登录。 本文介绍如何快速生成和使用适用于 Linux VM 的 SSH 协议版本 2 RSA 公钥和私钥文件对。 如需更详细的步骤和其他示例，请参阅[创建 SSH 密钥对和证书的详细步骤](create-ssh-keys-detailed.md)。

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对
请使用 `ssh-keygen` 命令创建 SSH 公钥和私钥文件，这些文件默认在 `~/.ssh` 目录中创建，但是可以在系统提示时指定其他位置和其他通行短语（用于访问私钥文件的密码）。 请通过 Bash 外壳程序运行以下命令，在出现提示时使用自己的信息进行回应。

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="use-the-ssh-key-pair"></a>使用 SSH 密钥对
放置在 Azure 中 Linux VM 上的公钥默认存储在 `~/.ssh/id_rsa.pub` 中，除非在创建该公钥时更改了位置。 如果使用 [Azure CLI 2.0](/cli/azure) 创建 VM，请在将 [az vm create](/cli/azure/vm#create) 与 `--ssh-key-path` 选项结合使用时指定该公钥的位置。 如果复制和粘贴要在 Azure 门户或 Resource Manager 模板中使用的公钥文件的内容，请确保不复制额外的空格。 例如，如果使用 OS X，则可将公钥文件（默认为 **~/.ssh/id_rsa.pub**）通过管道传送到 **pbcopy**，以便复制内容（也可通过其他 Linux 程序（例如 `xclip`）执行此类操作）。

如果不熟悉 SSH 公钥，则可通过运行 `cat` 来查看公钥（如下所示），注意需将 `~/.ssh/id_rsa.pub` 替换成自己的公钥文件位置：

```bash
cat ~/.ssh/id_rsa.pub
```

请使用 Azure VM 上的公钥，通过 SSH 使用 VM 的 IP 地址或 DNS 名称连接到 VM（记住将下面的 `azureuser` 和 `myvm.westus.cloudapp.azure.com` 替换为管理员用户名和完全限定域名或 IP 地址）：

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

如果在创建密钥对时提供的是通行短语，则在登录过程中遇到提示时，请输入该通行短语。 （服务器添加到 `~/.ssh/known_hosts` 文件夹。系统不会要求再次进行连接，除非更改了 Azure VM 上的公钥，或者从 `~/.ssh/known_hosts` 中删除了服务器名称。）

## <a name="next-steps"></a>后续步骤

使用 SSH 密钥创建的 VM 默认配置为禁用密码，使得强力猜测尝试代价相当高昂，因此也更为困难。 本主题介绍如何创建一个简单的、可以快速使用的 SSH 密钥对。 如果在创建 SSH 密钥对方面需要更多帮助，或者需要其他的证书，请参阅[创建 SSH 密钥对和证书的详细步骤](create-ssh-keys-detailed.md)。

可以通过 Azure 门户、CLI 和模板创建使用 SSH 密钥对的 VM：

* [使用 Azure 门户创建安全 Linux VM](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure CLI 2.0 创建安全 Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure 模板创建安全 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
