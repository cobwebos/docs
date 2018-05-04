---
title: 针对 Linux VM 将 SSH 密钥与 Windows 配合使用 | Microsoft Docs
description: 了解如何在 Windows 计算机上生成和使用 SSH 密钥连接到 Azure 上的 Linux 虚拟机。
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: d0762f80267fa927681344a3e0de78b0800c8306
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2018
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>如何在 Azure 上将 SSH 密钥与 Windows 配合使用

本文介绍如何在 Windows 计算机上生成和使用安全外壳 (SSH) 密钥以在 Azure 中创建并连接到 Linux 虚拟机 (VM)。 若要使用来自 Linux 或 macOS 客户端的 SSH 密钥，请参阅[快速](mac-create-ssh-keys.md)或[详细](create-ssh-keys-detailed.md)指南。

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows 程序包和 SSH 客户端
可使用 *SSH 客户端*连接到 Azure 中的 Linux VM，并对其进行管理。 运行 Linux 或 macOS 的计算机通常具有一套 SSH 命令来生成和管理 SSH 密钥并建立 SSH 连接。 

Windows 计算机并不总是装有类似的 SSH 命令。 通过包含[适用于 Linux 的 Windows 子系统](https://docs.microsoft.com/windows/wsl/about)的 Windows 10 版本，可在 Bash shell 中以本机方式运行并访问 SSH 客户端等实用工具。 

如果不希望使用 Bash for Windows，以下包中包含可本地安装的常见 Windows SSH 客户端：

* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git For Windows](https://git-for-windows.github.io/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

另一种选择是使用 [Azure Cloud Shell](../../cloud-shell/overview.md) 的 Bash 中提供的 SSH 实用工具。 

* 在 Web 浏览器中输入 [https://shell.azure.com](https://shell.azure.com) 或通过 [Azure 门户](https://portal.azure.com)来访问 Cloud Shell。 
* 通过安装 [Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)，从 Visual Studio Code 内访问作为终端的 Cloud Shell。

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对
本节介绍在 Windows 上创建 SSH 密钥对的两种选项。

### <a name="create-ssh-keys-with-ssh-keygen"></a>使用 ssh-keygen 创建 SSH 密钥

如果可运行 Bash for Windows 或 GitBash（或 Azure Cloud Shell 中的 Bash）等命令 shell，请使用 `ssh-keygen` 命令创建一个 SSH 密钥对。 键入以下命令，并回答提示。 如果当前位置存在 SSH 密钥对，这些文件将被覆盖。 

```bash
ssh-keygen -t rsa -b 2048
```

有关详细背景和信息，请参阅[快速](mac-create-ssh-keys.md)或[详细](create-ssh-keys-detailed.md)步骤以使用 `ssh-keygen` 创建密钥。

### <a name="create-ssh-keys-with-puttygen"></a>使用 PuTTYgen 创建 SSH 密钥

如果更喜欢使用基于 GUI 的工具创建 SSH 密钥，可使用 [PuTTY 下载包](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)附带的 PuTTYgen 密钥生成器。 

使用 PuTTYgen 创建 SSH RSA 密钥对：

1. 启动 PuTTYgen。

2. 单击“生成”。 默认情况下，PuTTYgen 生成一个 2048 位的 SSH-2 RSA 密钥。

4. 将鼠标悬停在空白区域上，为密钥生成某些随机性。

5. 生成公钥后，可选择输入并确认通行短语。 使用 SSH 密钥面向 VM 进行身份验证时，系统会提示输入通行短语。 没有通行短语，如果有人获取了私钥，他们可以登录到使用该密钥的任何 VM 或服务。 建议创建一个通行短语。 但是，如果忘记了通行短语，将没有办法恢复它。

6. 公钥会显示在窗口的顶部。 创建 Linux VM 时，将此单行格式公钥复制并粘贴到 Azure 门户或 Azure 资源管理器模板中。 也可以单击“保存公钥”将副本保存到计算机中：

    ![保存 PuTTY 公钥文件](./media/ssh-from-windows/save-public-key.png)

7. 或者，若要将私钥以 PuTTy 私钥格式（.ppk 文件）保存，请单击“保存私钥”。 如果希望稍后使用 PuTTY 来通过 SSH 连接到 VM，则需要该 .ppk 文件。

    ![保存 PuTTY 私钥文件](./media/ssh-from-windows/save-ppk-file.png)

    如果要将私钥以 OpenSSH 格式（许多 SSH 客户端使用的私钥格式）保存，请单击“转换” > “导出 OpenSSH 密钥”。

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>部署 VM 时提供 SSH 公钥

若要创建使用 SSH 密钥进行身份验证的 Linux VM，请在使用 Azure 门户或其他方法创建 VM 时提供 SSH 公钥。

以下示例演示在创建 Linux VM 时如何将此公钥复制并粘贴到 Azure 门户。 然后，此公钥通常存储在新 VM 上的 `~/.ssh/authorized_keys` 中。

   ![在 Azure 门户中创建 VM 时使用公钥](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>连接到 VM

在 Windows 中建立 SSH 以连接 Linux VM 的方法之一是使用 SSH 客户端。 如果 Windows 系统上安装了 SSH 客户端，或者在 Azure Cloud Shell 的 Bash 中使用 SSH 工具，这是首选方法。 如果更喜欢基于 GUI 的工具，可使用 PuTTY 进行连接。  

### <a name="use-an-ssh-client"></a>使用 SSH 客户端
凭借部署在 Azure VM 上的公钥和本地系统上的私钥，使用 VM 的 IP 地址或 DNS 名称通过 SSH 连接到 VM。 将以下命令中的 azureuser 和 myvm.westus.cloudapp.azure.com 替换为管理员用户名和完全限定的域名（或 IP 地址）：

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

如果创建密钥对时配置了通行短语，则在登录过程中遇到提示时，请输入该通行短语。

### <a name="connect-with-putty"></a>通过 PuTTY 连接

如果安装了 [PuTTY 下载包](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)并且之前生成了 PuTTY 私钥（.ppk 文件），可通过 PuTTY 连接到 Linux VM。

1. 启动 PuTTY。

2. 在 Azure 门户中填写 VM 的主机名或 IP 地址：

    ![打开新的 PuTTY 连接](./media/ssh-from-windows/putty-new-connection.png)

3. 在选择“打开”之前，单击“连接” > “SSH” > “身份验证”选项卡。浏览并选择 PuTTY 私钥（.ppk 文件）：

    ![选择用于身份验证的 PuTTY 私钥](./media/ssh-from-windows/putty-auth-dialog.png)

4. 单击“打开”以连接到 VM。

## <a name="next-steps"></a>后续步骤

* 有关使用 SSH 密钥的详细步骤、选项以及高级示例，请参阅[创建 SSH 密钥对的详细步骤](create-ssh-keys-detailed.md)。

* 也可使用 Azure Cloud Shell 中的 PowerShell 来生成 SSH 密钥并通过 SSH 连接到 Linux VM。 请参阅 [PowerShell 快速入门](../../cloud-shell/quickstart-powershell.md#ssh)。

* 如果在使用 SSH 连接到 Linux VM 时遇到问题，请参阅 [Troubleshoot SSH connections to an Azure Linux VM](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（通过 SSH 连接到 Azure Linux VM 故障排除）。
