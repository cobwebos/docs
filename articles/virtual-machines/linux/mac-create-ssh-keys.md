---
title: 创建和使用适用于 Azure 中 Linux VM 的 SSH 密钥对 | Microsoft Docs
description: 如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥-私钥对，提高身份验证过程的安全性。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: d442d09c8c8ded3aa50faf74e28c8d95ded24a5e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614406"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>快速步骤：创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥-私钥对

使用安全外壳 (SSH) 密钥对，可以在 Azure 中创建使用 SSH 密钥进行身份验证的虚拟机 (VM)，从而无需密码即可登录。 本文介绍如何快速生成和使用适用于 Linux VM 的 SSH 公钥-私钥文件对。 可使用 Azure Cloud Shell、macOS 或 Linux 主机或者适用于 Linux 的 Windows 子系统以及其他支持 OpenSSH 的工具完成这些步骤。 

> [!NOTE]
> 使用 SSH 密钥创建的 VM 默认配置为已禁用密码，这将大大增加暴力猜测攻击的难度。 

有关详细背景和示例，请参阅[创建 SSH 密钥对的详细步骤](create-ssh-keys-detailed.md)。

有关在 Windows 计算机上生成和使用 SSH 密钥的其他方式，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](ssh-from-windows.md)。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

使用 `ssh-keygen` 命令生成 SSH 公钥和私钥文件。 默认情况下，这些文件在 ~/.ssh 目录中创建。 可以指定其他位置，以及访问私钥文件的密码 (passphrase)  。 如果给定位置存在具有相同名称的 SSH 密钥对，则这些文件将被覆盖。

以下命令使用 RSA 加密和 2048 位长度创建 SSH 密钥对：

```bash
ssh-keygen -t rsa -b 2048
```

如果通过 [Azure CLI](/cli/azure) 使用 [az vm create](/cli/azure/vm#az-vm-create) 命令创建 VM，可以使用 `--generate-ssh-keys` 选项生成 SSH 公钥和私钥文件。 密钥文件存储在 ~/.ssh 目录中，除非 `--ssh-dest-key-path` 选项另有规定。 `--generate-ssh-keys` 选项不会覆盖现有密钥文件，而是会返回错误。 在以下命令中，将 VMname 和 RGname 替换为你自己的值   ：

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>在部署 VM 时提供 SSH 公钥

若要创建使用 SSH 密钥进行身份验证的 Linux VM，请在使用 Azure 门户、Azure CLI、Azure 资源管理器模板或其他方法创建 VM 时指定 SSH 公钥：

* [使用 Azure 门户创建 Linux 虚拟机](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure CLI 创建 Linux 虚拟机](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure 模板创建 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

如果不熟悉 SSH 公钥的格式，可以使用以下 `cat` 命令显示公钥，将 `~/.ssh/id_rsa.pub` 替换为你自己的公钥文件的路径和文件名（如有必要）：

```bash
cat ~/.ssh/id_rsa.pub
```

典型的公钥值如此示例所示：

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

如果复制和粘贴要在 Azure 门户或 Resource Manager 模板中使用的公钥文件的内容，请确保不复制尾随空格。 要在 macOS 中复制公钥，可以通过管道将公钥文件传递到 pbcopy  。 与此类似，要在 Linux 中复制公钥，可以将公钥文件通过管道传递到 xclip 等程序  。

放置在 Azure 中 Linux VM 上的公钥默认存储在 ~/.ssh/id_rsa.pub，除非在创建密钥对时指定了其他位置。 要借助现有公钥使用 [Azure CLI 2.0](/cli/azure) 创建 VM，请通过使用具有 `--ssh-key-value` 选项的 [az vm create](/cli/azure/vm#az-vm-create) 命令来指定此公钥的值或位置。 在以下命令中，将 VMname、RGname 和 keyFile 替换为你自己的值    ：

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>通过 SSH 连接到 VM

凭借部署在 Azure VM 上的公钥和本地系统上的私钥，使用 VM 的 IP 地址或 DNS 名称通过 SSH 连接到 VM。 在以下命令中，将 azureuser 和 myvm.westus.cloudapp.azure.com 替换为管理员用户名和完全限定的域名（或 IP 地址）   ：

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

如果创建密钥对时指定了通行短语，则在登录过程中遇到提示时，请输入该通行短语。 VM 已添加到 ~/.ssh/known_hosts 文件，系统不会要求再次连接，除非 Azure VM 上的公钥发生更改或服务器名称从 ~/.ssh/known_hosts 删除。

如果 VM 使用的是实时访问策略，则需要先请求访问权限，然后才能连接到 VM。 有关实时策略的详细信息，请参阅[使用实时策略管理虚拟机访问](../../security-center/security-center-just-in-time.md)。

## <a name="next-steps"></a>后续步骤

* 有关使用 SSH 密钥对的详细信息，请参阅[创建和管理 SSH 密钥对的详细步骤](create-ssh-keys-detailed.md)。

* 如果使用 SSH 连接 Azure VM 时遇到问题，请参阅[排除使用 SSH 连接 Azure Linux VM 的故障](troubleshoot-ssh-connection.md)。
