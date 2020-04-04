---
title: 加入 Ubuntu VM 到 Azure AD 域服务 |微软文档
description: 了解如何配置 Ubuntu Linux 虚拟机并将其加入 Azure AD 域服务托管域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 95373ab8ff78c5bcb856e6d7e6d67d8525cd3f7e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655132"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>将 Ubuntu Linux 虚拟机加入 Azure AD 域服务托管域

要允许用户使用一组凭据登录到 Azure 中的虚拟机 （VM），可以将 VM 加入 Azure 活动目录域服务 （AD DS） 托管域。 将 VM 加入 Azure AD DS 托管域时，可以使用域中的用户帐户和凭据登录和管理服务器。 Azure AD DS 托管域中的组成员身份也应用于允许您控制对 VM 上文件或服务的访问。

本文介绍如何将 Ubuntu Linux VM 加入 Azure AD DS 托管域。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果没有 Azure 订阅，[请创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请参考第一篇教程[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。
* 属于 Azure AD DS 托管域的用户帐户。

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>创建并连接到 Ubuntu Linux VM

如果在 Azure 中具有现有的 Ubuntu Linux VM，请使用 SSH 连接到它，然后继续执行下一步[以开始配置 VM](#configure-the-hosts-file)。

如果需要创建 Ubuntu Linux VM，或者想要创建用于本文的测试 VM，可以使用以下方法之一：

* [Azure 门户](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

创建 VM 时，请注意虚拟网络设置，以确保 VM 能够与 Azure AD DS 托管域通信：

* 将 VM 部署到启用 Azure AD 域服务的相同或对等虚拟网络。
* 将 VM 部署到与 Azure AD 域服务实例不同的子网中。

部署 VM 后，按照步骤使用 SSH 连接到 VM。

## <a name="configure-the-hosts-file"></a>配置主机文件

要确保为托管域正确配置 VM 主机名，请编辑 */etc/主机*文件并设置主机名：

```console
sudo vi /etc/hosts
```

在*主机*文件中，更新*本地主机*地址。 在以下示例中：

* *aaddscontoso.com*是 Azure AD DS 托管域的 DNS 域名。
* *ubuntu*是要加入托管域的 Ubuntu VM 的主机名。

使用您自己的值更新这些名称：

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

完成后，使用编辑器`:wq`的命令保存并退出*主机*文件。

## <a name="install-required-packages"></a>安装所需程序包

VM 需要一些其他包才能将 VM 加入 Azure AD DS 托管域。 要安装和配置这些包，请使用`apt-get`

在 Kerberos 安装期间 *，krb5 用户*包会提示所有 UPPERCASE 中的域名称。 例如，如果 Azure AD DS 托管域的名称*aaddscontoso.com，**则AADDSCONTOSO.COM*作为域输入。 安装在`[realm]`*/etc/krb5.conf*配置文件中写入 和`[domain_realm]`节。 请确保指定所有"大写"的域：

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>配置网络时间协议 （NTP）

要使域通信正常工作，Ubuntu VM 的日期和时间必须与 Azure AD DS 托管域同步。 将 Azure AD DS 托管域的 NTP 主机名添加到 */etc/ntp.conf*文件中。

1. 使用编辑器打开*ntp.conf*文件：

    ```console
    sudo vi /etc/ntp.conf
    ```

1. 在*ntp.conf*文件中，创建一行以添加 Azure AD DS 托管域的 DNS 名称。 在下面的示例中，将添加*aaddscontoso.com*的条目。 使用您自己的 DNS 名称：

    ```console
    server aaddscontoso.com
    ```

    完成后，使用编辑器`:wq`的命令保存并退出*ntp.conf*文件。

1. 要确保 VM 与 Azure AD DS 托管域同步，需要执行以下步骤：

    * 停止 NTP 服务器
    * 从托管域更新日期和时间
    * 启动 NTP 服务

    运行以下命令以完成这些步骤。 将您自己的 DNS 名称与`ntpdate`命令一起使用：

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>将 VM 加入托管域

现在，所需的软件包安装在 VM 上并配置了 NTP，请将 VM 加入 Azure AD DS 托管域。

1. 使用`realm discover`命令发现 Azure AD DS 托管域。 下面的示例发现*AADDSCONTOSO.COM*的域。 在"所有上写"中指定您自己的 Azure AD DS 托管域名：

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   如果`realm discover`命令找不到 Azure AD DS 托管域，请查看以下故障排除步骤：

    * 确保可以从 VM 联系到域。 尝试`ping aaddscontoso.com`查看是否返回了肯定的答复。
    * 检查 VM 是否部署到 Azure AD DS 托管域可用的同一虚拟网络或对等虚拟网络。
    * 确认虚拟网络的 DNS 服务器设置已更新以指向 Azure AD DS 托管域的域控制器。

1. 现在使用`kinit`命令初始化 Kerberos。 指定属于 Azure AD DS 托管域的用户。 如果需要[，将用户帐户添加到 Azure AD 中的组](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    同样，必须在所有上部操作中输入 Azure AD DS 托管域名。 在下面的示例中，命名的`contosoadmin@aaddscontoso.com`帐户用于初始化 Kerberos。 输入属于 Azure AD DS 托管域的用户帐户：

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. 最后，使用 命令`realm join`将计算机加入 Azure AD DS 托管域。 使用上一命令中`kinit`指定的 Azure AD DS 托管域的一部分的相同用户帐户，例如： `contosoadmin@AADDSCONTOSO.COM`

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

将 VM 加入 Azure AD DS 托管域需要一些时间。 以下示例输出显示 VM 已成功加入 Azure AD DS 托管域：

```output
Successfully enrolled machine in realm
```

如果 VM 无法成功完成域加入过程，请确保 VM 的网络安全组允许在 TCP + UDP 端口 464 上向 Azure AD DS 托管域的虚拟网络子网进行出站 Kerberos 流量。

## <a name="update-the-sssd-configuration"></a>更新 SSSD 配置

上一步中安装的软件包之一是系统安全服务守护进程 （SSSD）。 当用户尝试使用域凭据登录到 VM 时，SSSD 会将请求中继到身份验证提供程序。 在这种情况下，SSSD 使用 Azure AD DS 对请求进行身份验证。

1. 使用编辑器打开*ssd.conf*文件：

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. 注释掉*use_fully_qualified_names*行，如下所示：

    ```console
    # use_fully_qualified_names = True
    ```

    完成后，使用编辑器`:wq`的命令保存并退出*ssd.conf*文件。

1. 要应用更改，请重新启动 SSSD 服务：

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>配置用户帐户和组设置

将 VM 连接到 Azure AD DS 托管域并配置为身份验证后，需要完成一些用户配置选项。 这些配置更改包括允许基于密码的身份验证，并在域用户首次登录时自动在本地 VM 上创建主目录。

### <a name="allow-password-authentication-for-ssh"></a>允许 SSH 的密码身份验证

默认情况下，用户只能使用基于 SSH 公钥的身份验证登录到 VM。 基于密码的身份验证失败。 将 VM 加入 Azure AD DS 托管域时，这些域帐户需要使用基于密码的身份验证。 更新 SSH 配置以允许基于密码的身份验证，如下所示。

1. 使用编辑器打开*sshd_conf*文件：

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. 将*密码身份验证*行更新为 *"是*" ：

    ```console
    PasswordAuthentication yes
    ```

    完成后，使用编辑器`:wq`的命令保存并退出*sshd_conf*文件。

1. 要应用更改并允许用户使用密码登录，请重新启动 SSH 服务：

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>配置自动主目录创建

要在用户首次登录时启用自动创建主目录，请完成以下步骤：

1. 在编辑器中打开 */etc/pam.d/公用会话*文件：

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. 在此文件中添加以下行`session optional pam_sss.so`，

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    完成后，使用编辑器`:wq`的命令保存并退出*公用会话*文件。

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>为“AAD DC 管理员”组授予 sudo 特权

要授予 Ubuntu VM 上的*AAD DC 管理员*组管理权限，您需要向 */etc/sudoers*添加一个条目。 添加后 *，AAD DC 管理员*组的成员可以在 Ubuntu VM 上使用`sudo`该命令。

1. 打开*sudoers*文件进行编辑：

    ```console
    sudo visudo
    ```

1. 将以下条目添加到 */etc/sudoers*文件的末尾：

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    完成后，使用`Ctrl-X`命令保存并退出编辑器。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用域帐户登录到 VM

要验证 VM 已成功加入 Azure AD DS 托管域，请使用域用户帐户启动新的 SSH 连接。 确认已创建主目录，并应用域中的组成员身份。

1. 从控制台创建新的 SSH 连接。 使用`ssh -l`命令使用属于托管域的域帐户，例如`contosoadmin@aaddscontoso.com`，然后输入 VM 的地址，如*ubuntu.aaddscontoso.com*。 如果使用 Azure 云外壳，请使用 VM 的公共 IP 地址，而不是内部 DNS 名称。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. 成功连接到 VM 后，请验证主目录是否已正确初始化：

    ```console
    pwd
    ```

    您应该位于 */home*目录中，有自己的目录与用户帐户匹配。

1. 现在检查组成员身份是否得到正确解决：

    ```console
    id
    ```

    您应该从 Azure AD DS 托管域中看到组成员身份。

1. 如果您以*AAD DC 管理员*组的成员身份登录到 VM，请检查是否可以正确使用以下`sudo`命令：

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>后续步骤

如果在将 VM 连接到 Azure AD DS 托管域或使用域帐户登录时遇到问题，请参阅[排除域联接问题](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
