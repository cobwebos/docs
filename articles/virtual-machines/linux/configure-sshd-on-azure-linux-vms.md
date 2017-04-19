---
title: "在 Azure Linux 虚拟机上配置 SSHD | Microsoft Docs"
description: "遵循安全最佳实践配置 SSHD，并将 SSH 锁定到 Azure Linux 虚拟机。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0195c385b00ab92b2b92ce8ff00983a0d91bf3a1
ms.lasthandoff: 04/03/2017


---

# <a name="configure-sshd-on-azure-linux-vms"></a>在 Azure Linux VM 上配置 SSHD

本文介绍如何将 SSH 服务器锁定到 Linux，以确保在安全方面符合最佳实践，并通过使用 SSH 密钥而非密码加快 SSH 登录过程。  为了进一步锁定 SSHD，我们将禁止根用户登录、限制可以通过已审批组列表登录的用户数（禁用 SSH 协议版本 1）、设置最小密钥位，以及配置空闲用户自动注销。  本文的要求如下：一个 Azure 帐户（[获取免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)）以及 [SSH 公钥和私钥文件](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="quick-commands"></a>快速命令

使用以下设置配置 `/etc/ssh/sshd_config`：

### <a name="disable-password-logins"></a>禁用密码登录

```bash
PasswordAuthentication no
```

### <a name="disable-login-by-the-root-user"></a>禁止通过根用户登录

```bash
PermitRootLogin no
```

### <a name="allowed-groups-list"></a>允许组列表

```bash
AllowGroups wheel
```

### <a name="allowed-users-list"></a>允许用户列表

```bash
AllowUsers ahmet ralph
```

### <a name="disable-ssh-protocol-version-1"></a>禁用 SSH 协议版本 1

```bash
Protocol 2
```

### <a name="minimum-key-bits"></a>最小密钥位

```bash
ServerKeyBits 2048
```

### <a name="disconnect-idle-users"></a>断开空闲用户的连接

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="detailed-walkthrough"></a>详细演练

SSHD 是在 Linux VM 上运行的 SSH 服务器。  SSH 是一个客户端，通过 MacBook、Linux 工作站上的 shell 或 Windows 上的 Bash 运行。  SSH 也是一个协议，用于保护和加密工作站与 Linux VM 之间的通信，让 SSH 也充当 VPN（虚拟专用网络）。

在本文中，必须让用户在 Linux VM 中保持登录状态，使整个演练得以顺畅进行。  SSH 连接一旦建立就始终是一个打开的会话，除非窗口关闭。  只要有一个终端处于登录状态，即可对 SSHD 服务进行更改，而不会被系统锁定（如果所做更改为重大更改）。  如果用户在进行重大 SSHD 配置时被锁定在 Linux VM 之外，则可利用 Azure 提供的功能，通过 [Azure VM 访问扩展](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)重置重大的 SSHD 配置。

因此，我们会打开两个终端，并从它们通过 SSH 登录到 Linux VM。  我们使用第一个终端更改 SSHD 配置文件，然后重新启动 SSHD 服务。  重新启动服务后，我们会使用第二个终端测试这些更改。  由于我们要禁用 SSH 密码并严重依赖于 SSH 密钥，因此如果 SSH 密钥不正确而关闭 VM 连接，VM 将永久被锁定，并且没有人能够登录。这样，只能将它删除后再重新创建。

## <a name="disable-password-logins"></a>禁用密码登录

若要确保 Linux VM 的安全，最快速的方法是禁用密码登录。  启用密码登录以后，爬网机器人就会立即开始尝试使用 SSH 暴力猜测 Linux VM 的密码。  彻底禁用密码登录以后，SSH 服务器就会忽略所有密码登录尝试。

```bash
PasswordAuthentication no
```

## <a name="disable-login-by-the-root-user"></a>禁止通过根用户登录

根据 Linux 最佳实践，`root` 用户不应通过 SSH 或 `sudo su` 登录。  需要根级别权限的所有命令均应通过 `sudo` 命令运行，该命令会记录所有操作供今后审核。  禁止 `root` 用户通过 SSH 登录是一项安全方面的最佳实践步骤，确保仅获得授权的用户能够使用 SSH。

```bash
PermitRootLogin no
```

## <a name="allowed-groups-list"></a>允许组列表

SSH 提供了一种限制用户和组的方法，允许或不允许他们通过 SSH 登录。  此功能使用列表批准或拒绝特定用户和组登录。  将 wheel 组设置到 `AllowGroups` 列表中，仅允 wheel 组中的用户帐户通过 SSH 进行批准的登录。

```bash
AllowGroups wheel
```

## <a name="allowed-users-list"></a>允许用户列表

若要完成 `AllowGroups` 这样的任务，更具体的方式是仅允许特定用户进行 SSH 登录。  

```bash
AllowUsers ahmet ralph
```

## <a name="disable-ssh-protocol-version-1"></a>禁用 SSH 协议版本 1

SSH 协议版本 1 不安全，应禁用。  SSH 协议版本 2 是最新的版本，可以安全地通过 SSH 登录到服务器。  禁用 SSH 版本 1 会拒绝任何 SSH 客户端尝试使用 SSH 版本 1 与 SSH 服务器建立连接。  通过协商方式与 SSH 服务器建立连接时，仅允许 SSH 版本 2 连接。

```bash
Protocol 2
```

## <a name="minimum-key-bits"></a>最小密钥位

根据安全方面的最佳实践，禁用密码 SSH 登录，仅允许使用 SSH 密钥通过 SSH 服务器进行身份验证。  这些 SSH 密钥在创建时可以使用不同的密钥长度（以位为单位）。  根据最佳实践，2048 位是可以接受的最小密钥长度。  小于 2048 位的密钥理论上是可以破解的。  将 `ServerKeyBits` 设置为 `2048` 以后，系统会允许使用的密钥为 2048 位或以上的连接，拒绝密钥不到 2048 位的连接。

```bash
ServerKeyBits 2048
```

## <a name="disconnect-idle-users"></a>断开空闲用户的连接

SSH 可以断开特定用户的连接，这些用户在开启连接以后连续处于空闲状态的时间超过了设置的秒数。  仅对那些处于活动状态的用户开启会话可以限制 Linux VM 的暴露机会。

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="restart-sshd"></a>重新启动 SSHD

若要在 `/etc/ssh/sshd_config` 中启用该设置，请重新启动 SSHD 过程，以便重新启动 SSH 服务器。  用于重新启动 SSH 服务器的终端窗口始终处于打开状态，不会失去已打开的 SSH 会话。  若要测试新的 SSH 服务器设置，请使用另一个终端窗口或选项卡。  使用单独的终端测试 SSH 连接，用户就可以返回去在第一个终端中对 `/etc/ssh/sshd_config` 进行其他更改，而不会因进行重大的 SSHD 更改被系统锁定。  

### <a name="on-redhat-centos-and-fedora"></a>在 Redhat、Centos 和 Fedora 上

```bash
service sshd restart
```

### <a name="on-debian--ubuntu"></a>在 Debian 和 Ubuntu 上

```bash
service ssh restart
```

## <a name="reset-sshd-using-azure-reset-access"></a>使用 Azure 的 reset-access 重置 SSHD

如果用户在对 SSHD 配置进行重大更改时被系统锁定，则可使用 Azure VM 的访问扩展将 SSHD 配置重置回原始配置。

将任何示例性名称替换为你自己的名称。

```azurecli
azure vm reset-access \
--resource-group myResourceGroup \
--name myVM \
--reset-ssh
```

## <a name="install-fail2ban"></a>安装 Fail2ban

强烈建议安装和设置开源应用 Fail2ban，阻止入侵者不断地尝试使用暴力破解方式通过 SSH 登录到 Linux VM。  Fail2ban 会记录不断地尝试通过 SSH 登录但却失败的行为，然后创建防火墙规则，阻止进行此类尝试的入侵者所在的 IP 地址。

* [Fail2ban 主页](http://www.fail2ban.org/wiki/index.php/Main_Page)

## <a name="next-steps"></a>后续步骤

在 Linux VM 上配置并锁定 SSH 服务器以后，即可按照其他安全方面的最佳实践进行操作。  

* [管理用户、SSH，并使用 VMAccess 扩展检查或修复 Azure Linux VM 上的磁盘](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [使用 Azure CLI 加密 Linux VM 上的磁盘](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Azure Resource Manager 模板中的访问权限和安全性](dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

