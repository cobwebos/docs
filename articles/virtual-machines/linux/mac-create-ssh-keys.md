---
title: 在 Azure 中创建和使用适用于 Linux Vm 的 SSH 密钥对
description: 如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥-私钥对，提高身份验证过程的安全性。
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: af18a32143ebc9db7be923b09de106b79022321f
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969043"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>快速步骤：创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥-私钥对

使用安全外壳 (SSH) 密钥对，可以在 Azure 中创建使用 SSH 密钥进行身份验证的虚拟机 (VM)，从而无需密码即可登录。 本文介绍如何快速生成和使用适用于 Linux VM 的 SSH 公钥-私钥文件对。 可使用 Azure Cloud Shell、macOS 或 Linux 主机或者适用于 Linux 的 Windows 子系统以及其他支持 OpenSSH 的工具完成这些步骤。 

> [!NOTE]
> 使用 SSH 密钥创建的 VM 默认配置为已禁用密码，这将大大增加暴力猜测攻击的难度。 

有关详细背景和示例，请参阅[创建 SSH 密钥对的详细步骤](create-ssh-keys-detailed.md)。

有关在 Windows 计算机上生成和使用 SSH 密钥的其他方式，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](ssh-from-windows.md)。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

使用 `ssh-keygen` 命令生成 SSH 公钥和私钥文件。 默认情况下，这些文件在 ~/.ssh 目录中创建。 可以指定其他位置，以及访问私钥文件的密码 (passphrase)。 如果给定位置存在具有相同名称的 SSH 密钥对，则这些文件将被覆盖。

以下命令使用 RSA 加密创建 SSH 密钥对，并使用位长度4096：

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

如果通过 [Azure CLI](/cli/azure) 使用 [az vm create](/cli/azure/vm#az-vm-create) 命令创建 VM，可以使用 `--generate-ssh-keys` 选项生成 SSH 公钥和私钥文件。 密钥文件存储在 ~/.ssh 目录中，除非 `--ssh-dest-key-path` 选项另有规定。 `--generate-ssh-keys` 选项不会覆盖现有密钥文件，而是会返回错误。 在以下命令中，将 VMname 和 RGname 替换为你自己的值：

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

如果复制和粘贴要在 Azure 门户或 Resource Manager 模板中使用的公钥文件的内容，请确保不复制尾随空格。 若要复制 macOS 中的公钥，可以通过管道将公钥文件传递给 `pbcopy`。 同样，在 Linux 中，你可以通过管道将公钥文件传递给 `xclip`等程序。

放置在 Azure 中 Linux VM 上的公钥默认存储在 ~/.ssh/id_rsa.pub，除非在创建密钥对时指定了其他位置。 要借助现有公钥使用 [Azure CLI 2.0](/cli/azure) 创建 VM，请通过使用具有 [ 选项的 ](/cli/azure/vm#az-vm-create)az vm create`--ssh-key-values` 命令来指定此公钥的值或位置。 在以下命令中，将 VMname、RGname 和 keyFile 替换为你自己的值：

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-values mysshkey.pub
```

如果你想要将多个 SSH 密钥与 VM 一起使用，则可以在空格分隔的列表中输入这些密钥，如以下 `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`所示。


## <a name="ssh-into-your-vm"></a>通过 SSH 连接到 VM

凭借部署在 Azure VM 上的公钥和本地系统上的私钥，使用 VM 的 IP 地址或 DNS 名称通过 SSH 连接到 VM。 在以下命令中，将 azureuser 和 myvm.westus.cloudapp.azure.com 替换为管理员用户名和完全限定的域名（或 IP 地址）：

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

如果创建密钥对时指定了通行短语，则在登录过程中遇到提示时，请输入该通行短语。 VM 已添加到 ~/.ssh/known_hosts 文件，系统不会要求再次连接，除非 Azure VM 上的公钥发生更改或服务器名称从 ~/.ssh/known_hosts 删除。

如果 VM 使用的是实时访问策略，则需要先请求访问权限，然后才能连接到 VM。 有关实时策略的详细信息，请参阅[使用实时策略管理虚拟机访问](../../security-center/security-center-just-in-time.md)。

## <a name="next-steps"></a>后续步骤

* 有关使用 SSH 密钥对的详细信息，请参阅[创建和管理 SSH 密钥对的详细步骤](create-ssh-keys-detailed.md)。

* 如果使用 SSH 连接 Azure VM 时遇到问题，请参阅[排除使用 SSH 连接 Azure Linux VM 的故障](troubleshoot-ssh-connection.md)。
