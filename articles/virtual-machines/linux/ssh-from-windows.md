---
title: 使用 SSH 密钥连接到 Linux Vm
description: 了解如何生成和使用来自 Windows 计算机的 SSH 密钥，以连接到 Azure 上的 Linux 虚拟机。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.date: 07/09/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 7e99c9191e93562211f6294cf671f431a5db455d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87825559"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>如何在 Azure 上将 SSH 密钥与 Windows 配合使用

本文适用于希望[创建](#create-an-ssh-key-pair)并使用安全外壳 (SSH) 密钥以[连接](#connect-to-your-vm)到 Azure 中的 Linux 虚拟机 (VM) 的 Windows 用户。 你还可以 [在 Azure 门户中生成并存储 SSH 密钥](../ssh-keys-portal.md) ，以便在门户中创建 vm 时使用。


若要从 Linux 或 macOS 客户端使用 SSH 密钥，请参阅[快速](mac-create-ssh-keys.md)指南。 有关 SSH 的更详细概述，请参阅[详细步骤：创建和管理用于在 Azure 中对 Linux VM 进行身份验证的 SSH 密钥](create-ssh-keys-detailed.md)。

## <a name="overview-of-ssh-and-keys"></a>SSH 和密钥概述

[SSH](https://www.ssh.com/ssh/) 是一种加密的连接协议，利用该协议可以通过未受保护的连接进行安全登录。 SSH 是在 Azure 中托管的 Linux VM 的默认连接协议。 虽然 SSH 本身提供加密连接，但是将密码用于 SSH 仍会使 VM 易受到暴力攻击。 建议使用公钥-私钥对（也称为“SSH 密钥”）通过 SSH 连接到 VM。 

公钥-私钥对类似于你前门的锁。 锁是向外界公开的，任何拥有正确钥匙的人都可以打开门。 钥匙是专用的，且仅提供给你信任的人，因为可以用它打开门锁。 

- 创建 VM 时，会将公钥放置在 Linux VM 上。 

- *私钥*仍保留在本地系统上。 请保护好私钥， 不要透露给其他人。

连接到 Linux VM 时，VM 会测试 SSH 客户端，以确保其具有正确的私钥。 如果客户端具有私钥，则授予其访问 VM 的权限。 

根据组织的安全策略，可重复使用单个密钥对来访问多个 Azure VM 和服务。 无需对每个 VM 使用单独的密钥对。 

可与任何人共享公钥；但只有你（或本地安全基础结构）才应具有对私钥的访问权限。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH 客户端

最新版本的 Windows 10 包括 [OpenSSH 客户端命令](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/)用于创建和使用 SSH 密钥，以及通过 PowerShell 或命令提示符建立 SSH 连接。 这是从 Windows 计算机创建到 Linux VM 的 SSH 连接的最简单方法。 

你还可以在 [Azure Cloud Shell](../../cloud-shell/overview.md) 中使用 Bash 来连接到 VM。 你可以在 [web 浏览器](https://shell.azure.com/bash)中使用 Cloud Shell，也可以在 [Azure 门户](https://portal.azure.com)中使用，也可以使用 [Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)在 Visual Studio Code 中使用。

你还可以安装[适用于 Linux 的 Windows 子系统](/windows/wsl/about)，以通过 SSH 连接到 VM，并在 Bash shell 中使用其他本机 Linux 工具。

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

使用 `ssh-keygen` 命令创建 SSH 密钥对。 输入文件名，或使用括号中显示的默认值（例如 `C:\Users\username/.ssh/id_rsa`）。  输入文件的密码，如果不想使用密码，请将密码留空。 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>使用密钥创建 VM

若要创建使用 SSH 密钥进行身份验证的 Linux VM，请在创建 VM 时提供 SSH 公钥。

使用 Azure CLI，可以使用 `az vm create` 和 `--ssh-key-value` 参数指定公钥的路径和文件名。

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

通过 PowerShell，使用 `New-AzVM` 并使用 ` 将 SSH 密钥添加到 VM 配置。 有关示例，请参阅[快速入门：使用 PowerShell 在 Azure 中创建 Linux 虚拟机](quick-create-powershell.md)。

如果你使用门户进行大量部署，则可能需要将公钥上传到 Azure，以便在从门户创建 VM 时可以轻松地选择它。 有关详细信息，请参阅 [上传 SSH 密钥](../ssh-keys-portal.md#upload-an-ssh-key)。


## <a name="connect-to-your-vm"></a>连接到 VM

凭借部署在 Azure VM 上的公钥和本地系统上的私钥，使用 VM 的 IP 地址或 DNS 名称通过 SSH 连接到 VM。 将以下命令中的 azureuser 和 10.111.12.123 替换为管理员用户名、IP 地址（或完全限定的域名）以及指向私钥的路径 ：

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

如果创建密钥对时配置了密码，请在出现提示时输入该密码。

如果 VM 使用的是实时访问策略，则需要先请求访问权限，然后才能连接到 VM。 有关实时策略的详细信息，请参阅[使用实时策略管理虚拟机访问](../../security-center/security-center-just-in-time.md)。


## <a name="next-steps"></a>后续步骤

- 有关 Azure 门户中的 SSH 密钥的信息，请参阅在门户中创建 Vm 时， [在 Azure 门户中生成并存储 ssh 密钥](../ssh-keys-portal.md) 。

- 有关使用 SSH 密钥的详细步骤、选项以及高级示例，请参阅[创建 SSH 密钥对的详细步骤](create-ssh-keys-detailed.md)。

- 也可使用 Azure Cloud Shell 中的 PowerShell 来生成 SSH 密钥并通过 SSH 连接到 Linux VM。 请参阅 [PowerShell 快速入门](../../cloud-shell/quickstart-powershell.md#ssh)。

- 如果在使用 SSH 连接到 Linux VM 时遇到麻烦，请参阅 [Troubleshoot SSH connections to an Azure Linux VM](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)（通过 SSH 连接到 Azure Linux VM 故障排除）。