---
title: 详细步骤 - Azure Linux VM 的 SSH 密钥对 | Microsoft Docs
description: 了解创建和管理适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对的详细步骤。
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: 827c80a70047fd0f1ad67e4f19cb2300e45b2c6b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2018
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>详细步骤：创建和管理用于 Azure 中 Linux VM 的身份验证的 SSH 密钥 
使用安全外壳 (SSH) 密钥对，可在 Azure 上创建默认使用 SSH 密钥进行身份验证的 Linux 虚拟机，从而无需密码即可登录。 使用 Azure 门户、Azure CLI、资源管理器模板或其他工具创建的 VM 可在部署中包含 SSH 公钥，为 SSH 连接设置 SSH 密钥身份验证。 

本文提供创建和管理用于 SSH 客户端连接的 SSH RSA 公钥/私钥文件对的详细背景和步骤。 如果想要快捷命令，请参阅[如何创建适用于 Azure 中 Linux VM 的 SSH 公钥/私钥对](mac-create-ssh-keys.md)。

有关在 Windows 计算机上生成和使用 SSH 密钥的其他方式，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](ssh-from-windows.md)。

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>私钥密码
SSH 私钥应使用非常安全的密码来保护它。 此密码只用于访问 SSH 私钥文件，不是用户帐户密码。 向 SSH 密钥添加密码时，会使用 128 位 AES 加密私钥，因此在不能通过密码解密的情况下，私钥是没有用的。 如果攻击者窃取了私钥，并且该私钥没有密码，那么他们就能使用私钥登录到有相应公钥的任何服务器。 如果私钥受密码保护，攻击者就无法使用，从而为 Azure 基础结构提供一个额外的安全层。

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>SSH 密钥的使用和优势

通过指定公钥创建 Azure VM 时，Azure 将公钥（以 `.pub` 格式）复制到 VM 上的 `~/.ssh/authorized_keys` 文件夹。 `~/.ssh/authorized_keys` 中的 SSH 密钥用于在 SSH 登录连接时质询客户端以匹配相应的私钥。 在使用 SSH 密钥进行身份验证的 Azure Linux VM 中，Azure 会将 SSHD 服务器配置为不允许密码登录，仅允许 SSH 密钥登录。 因此，使用 SSH 密钥创建 Azure Linux VM 可确保 VM 部署的安全，不必进行通常在部署完后需要进行的配置步骤（即在 `sshd_config` 文件中禁用密码）。

如果不希望使用 SSH 密钥，可以将 Linux VM 设置为使用密码身份验证。 如果 VM 不向 Internet 公开，使用密码可能已足够。 但是，仍需要管理每台 Linux VM 的密码和维护正常密码策略和做法（如最小密码长度）并定期进行更新。 使用 SSH 密钥可降低跨多台 VM 管理单个凭据的复杂性。

## <a name="generate-keys-with-ssh-keygen"></a>使用 ssh-keygen 生成密钥

若要创建密钥，首选命令是 `ssh-keygen`，它可与 Azure Cloud Shell、macOS 或 Linux 主机、[用于 Linux 的 Windows 子系统](https://docs.microsoft.com/windows/wsl/about)以及其他工具的 OpenSSH 实用程序配合使用。 `ssh-keygen` 会询问一系列问题，然后编写私钥和匹配的公钥。 

SSH 密钥默认保留在 `~/.ssh` 目录中。  如果没有 `~/.ssh` 目录，`ssh-keygen` 命令会使用正确的权限创建一个。

### <a name="basic-example"></a>基本示例

以下 `ssh-keygen` 命令默认在 `~/.ssh` 目录中生成 2048 位 SSH RSA 公钥和私钥文件。 如果当前位置存在 SSH 密钥对，这些文件将被覆盖。

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>详细示例
以下示例显示可用于创建 SSH RSA 密钥对的其他命令选项。 如果当前位置存在 SSH 密钥对，这些文件将被覆盖。 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**命令解释**

`ssh-keygen` = 用于创建密钥的程序

`-t rsa` = 要创建的密钥类型，本例中为 RSA 格式

`-b 4096` = 密钥的位数，本例中为 4096

`-C "azureuser@myserver"` = 追加到公钥文件末尾以便于识别的注释。 通常以电子邮件地址用作注释，但也可以使用任何最适合你基础结构的事物。

`-f ~/.ssh/mykeys/myprivatekey` = 私钥文件的文件名（如果选择不使用默认名称）。 追加了 `.pub` 的相应公钥文件在相同目录中生成。 该目录必须存在。

`-N mypassphrase` = 用于访问私钥文件的其他密码。 

### <a name="example-of-ssh-keygen"></a>ssh-keygen 示例

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

#### <a name="saved-key-files"></a>保存的密钥文件

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

本文中的密钥对名称。 系统默认提供名为 `id_rsa` 的密钥对，有些工具可能要求私钥文件名为 `id_rsa`，因此最好使用此密钥对。 目录 `~/.ssh/` 是 SSH 密钥对和 SSH 配置文件的默认位置。 如果未使用完全路径指定，则 `ssh-keygen` 会在当前的工作目录（而非默认的 `~/.ssh`）中创建密钥。

#### <a name="list-of-the-ssh-directory"></a>`~/.ssh` 目录列表

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>密钥密码

`Enter passphrase (empty for no passphrase):`

强烈建议为私钥添加密码。 如果不使用密码来保护密钥文件，任何人只要拥有该文件，就可以用它登录到拥有相应公钥的任何服务器。 添加密码可提升防护能力以防有人能够访问私钥文件，可让用户有时间更改密钥。

## <a name="generate-keys-automatically-during-deployment"></a>部署期间自动生成密钥

如果使用 [Azure CLI 2.0](/cli/azure) 创建 VM，则可以选择通过运行具有 `--generate-ssh-keys` 选项的 [az vm create](/cli/azure/vm#az_vm_create) 命令生成 SSH 公钥和私钥文件。 密钥存储在 ~/.ssh 目录中。 请注意，如果该位置已存在密钥，此命令选项不会覆盖这些密钥。

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>部署 VM 时提供 SSH 公钥

若要创建使用 SSH 密钥进行身份验证的 Linux VM，请在使用 Azure 门户、CLI、资源管理器模板或其他方法创建 VM 时提供 SSH 公钥。 使用门户时，请输入公钥本身。 如果借助现有公钥使用 [Azure CLI 2.0](/cli/azure) 创建 VM，请通过运行具有 `--ssh-key-value` 选项的 [az vm create](/cli/azure/vm#az_vm_create) 命令来指定此公钥的值或位置。 

如果不熟悉 SSH 公钥的格式，则可通过运行 `cat` 来查看公钥（如下所示），注意需将 `~/.ssh/id_rsa.pub` 替换成自己的公钥文件位置：

```bash
cat ~/.ssh/id_rsa.pub
```

输出如下所示（此处为密文形式）：

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

如果将公钥文件的内容复制粘贴到 Azure 门户或资源管理器模板，请确保不会复制额外的空格或添加额外的换行符。 例如，如果使用 macOS，则可将公钥文件（默认为 `~/.ssh/id_rsa.pub`）通过管道传送到 pbcopy，以便复制内容（也可通过其他 Linux 程序执行此类操作，例如 xclip）。

如果更愿意使用多行格式的公钥，则可基于之前创建的公钥在 pem 容器中生成 RFC4716 格式的密钥。

若要基于现有的 SSH 公钥创建 RFC4716 格式的密钥，请执行以下操作：

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>使用 SSH 客户端将 SSH 连接到 VM
凭借部署在 Azure VM 上的公钥和本地系统上的私钥，使用 VM 的 IP 地址或 DNS 名称通过 SSH 连接到 VM。 将以下命令中的 azureuser 和 myvm.westus.cloudapp.azure.com 替换为管理员用户名和完全限定的域名（或 IP 地址）：

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

如果在创建密钥对时提供的是通行短语，则在登录过程中遇到提示时，请输入该通行短语。 （服务器添加到 `~/.ssh/known_hosts` 文件夹。系统不会要求再次进行连接，除非更改了 Azure VM 上的公钥，或者从 `~/.ssh/known_hosts` 中删除了服务器名称。）

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>使用 ssh-agent 来存储私钥密码

为了避免在每次 SSH 登录时键入私钥文件密码，可以使用 `ssh-agent` 来缓存私钥文件密码。 如果使用 Mac，macOS Keychain 在用户调用 `ssh-agent` 时会安全存储私钥密码。

验证并使用 `ssh-agent` 和 `ssh-add` 将密钥文件的情况通知给 SSH 系统，这样就无需交互使用密码。

```bash
eval "$(ssh-agent -s)"
```

现在，使用命令 `ssh-add` 将私钥添加到 `ssh-agent`。

```bash
ssh-add ~/.ssh/id_rsa
```

私钥密码现在存储在 `ssh-agent` 中。

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>使用 ssh-copy-id 将密钥复制到现有 VM
如果已创建 VM，则可使用如下所示的命令将新的 SSH 公钥安装到 Linux VM：

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>创建并配置 SSH 配置文件

可创建并配置 SSH 配置文件 (`~/.ssh/config`)，以便加速登录和优化 SSH 客户端行为。 

以下示例显示一个简单配置，通过此配置，你可以使用默认的 SSH 私钥以用户身份快速登录到特定 VM。 

### <a name="create-the-file"></a>创建文件

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>编辑文件以添加新的 SSH 配置

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>示例配置

添加适用于主机 VM 的配置设置。

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

可为其他主机添加配置，让每台主机使用其自己的专用密钥对。 查看 [SSH 配置文件](https://www.ssh.com/ssh/config/)获取更多高级配置选项。

获得 SSH 密钥对并配置 SSH 配置文件后，便可以快速安全地登录到 Linux VM 了。 运行以下命令时，SSH 从 SSH 配置文件的 `Host myvm` 块中找到所有设置并加载它们。

```bash
ssh myvm
```

首次使用 SSH 密钥登录到服务器时，命令会提示用户输入该密钥文件的密码。

## <a name="next-steps"></a>后续步骤

下一步是使用新 SSH 公钥创建 Azure Linux VM。 使用 SSH 公钥作为登录名创建的 Azure VM 受到的保护优于使用默认登录方法（即密码）创建的 VM。

* [使用 Azure 门户创建 Linux 虚拟机](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure CLI 创建 Linux 虚拟机](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure 模板创建 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
