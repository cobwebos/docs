---
title: 在 Azure 中为 Linux VM 创建并使用 SSH 密钥对
description: 如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥-私钥对，提高身份验证过程的安全性。
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: 33ba816227db4cf958fd30c9dac1a0745505c504
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513683"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>快速步骤：创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥-私钥对

使用安全外壳 (SSH) 密钥对，可以创建使用 SSH 密钥进行身份验证的 Azure 虚拟机 (VM)。 本文介绍如何快速生成和使用适用于 Linux VM 的 SSH 公钥-私钥文件对。 你可以使用 Azure Cloud Shell、macOS 或 Linux 主机完成这些步骤。 

> [!NOTE]
> 使用 SSH 密钥创建的 VM 默认配置为禁用密码，这极大地增加了暴力破解猜测攻击的难度。 

有关详细背景和示例，请参阅[创建 SSH 密钥对的详细步骤](create-ssh-keys-detailed.md)。

有关在 Windows 计算机上生成和使用 SSH 密钥的其他方式，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](ssh-from-windows.md)。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

使用 `ssh-keygen` 命令生成 SSH 公钥和私钥文件。 默认情况下，这些文件在 ~/.ssh 目录中创建。 可以指定不同的位置，并指定可选的密码（通行短语）用于访问私钥文件。 如果给定的位置存在同名的 SSH 密钥对，则会覆盖这些文件。

以下命令使用 RSA 加密和 4096 位长度创建 SSH 密钥对：

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

如果通过 [Azure CLI](/cli/azure) 使用 [az vm create](/cli/azure/vm#az-vm-create) 命令创建 VM，可以使用 `--generate-ssh-keys` 选项生成 SSH 公钥和私钥文件。 除非使用 `--ssh-dest-key-path` 选项另行指定，否则将在 ~/.ssh 目录中存储密钥文件。 如果已存在 ssh 密钥对且使用的是 `--generate-ssh-keys` 选项，则不会生成新的密钥对，而是会使用现有的密钥对。 在以下命令中，请将 *VMname* 和 *RGname* 替换为自己的值：

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>部署 VM 时提供 SSH 公钥

若要创建使用 SSH 密钥进行身份验证的 Linux VM，请在使用 Azure 门户、Azure CLI、Azure 资源管理器模板或其他方法创建 VM 时指定 SSH 公钥：

* [使用 Azure 门户创建 Linux 虚拟机](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure CLI 创建 Linux 虚拟机](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure 模板创建 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

如果你不熟悉 SSH 公钥的格式，可使用以下 `cat` 命令显示公钥（请根据需要，将 `~/.ssh/id_rsa.pub` 替换为自己的公钥文件的路径和文件名）：

```bash
cat ~/.ssh/id_rsa.pub
```

典型的公钥值如此示例所示：

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

如果复制并粘贴要在 Azure 门户或资源管理器模板中使用的公钥文件的内容，请务必不要复制任何尾部空格。 若要在 macOS 中复制公钥，可以通过管道将公钥文件传递给 `pbcopy`。 类似地，在 Linux 中，可以通过管道将公钥文件传递给 `xclip` 等程序。

放置在 Azure 中 Linux VM 上的公钥默认存储在 ~/.ssh/id_rsa.pub 中，除非在创建密钥对时指定了不同的位置。 若要借助现有公钥使用 [Azure CLI 2.0](/cli/azure) 创建 VM，请结合 `--ssh-key-values` 选项使用 [az vm create](/cli/azure/vm#az-vm-create) 命令，来指定此公钥的值和（可选的）位置。 在下面的命令中，将 myVM、myResourceGroup、UbuntuLTS、azureuser 和 mysshkey.pub 替换为自己的值    ：


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

如果希望在 VM 中使用多个 SSH 密钥，可以空格分隔列表的形式输入它们，如此 (`--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`) 所示。


## <a name="ssh-into-your-vm"></a>通过 SSH 连接到 VM

凭借部署在 Azure VM 上的公钥和本地系统上的私钥，使用 VM 的 IP 地址或 DNS 名称通过 SSH 连接到 VM。 在以下命令中，将 azureuser 和 myvm.westus.cloudapp.azure.com 替换为管理员用户名和完全限定的域名（或 IP 地址） ：

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

如果在创建密钥对时指定了通行短语，则在登录过程中看到提示时，请输入该通行短语。 VM 将添加到 ~/.ssh/known_hosts 文件。系统不会要求再次进行连接，除非更改了 Azure VM 上的公钥，或者从 ~/.ssh/known_hosts 中删除了服务器名称。

如果 VM 使用的是实时访问策略，则需要先请求访问权限，然后才能连接到 VM。 有关实时策略的详细信息，请参阅[使用实时策略管理虚拟机访问](../../security-center/security-center-just-in-time.md)。

## <a name="next-steps"></a>后续步骤

* 有关使用 SSH 密钥对的详细信息，请参阅[创建和管理 SSH 密钥对的详细步骤](create-ssh-keys-detailed.md)。

* 如果使用 SSH 连接 Azure VM 时遇到问题，请参阅[排查 Azure Linux VM 的 SSH 连接问题](../troubleshooting/troubleshoot-ssh-connection.md)。
