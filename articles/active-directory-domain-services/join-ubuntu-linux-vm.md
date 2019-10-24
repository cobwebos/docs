---
title: 将 Ubuntu VM 加入 Azure AD 域服务 |Microsoft Docs
description: 了解如何配置 Ubuntu Linux 虚拟机并将其加入 Azure AD 域服务托管域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: iainfou
ms.openlocfilehash: 9fb41b08cb29a68b39fb416b4b7b7bcce9e821dd
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754354"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>将 Ubuntu Linux 虚拟机加入 Azure AD 域服务托管域

若要让用户使用一组凭据登录到 Azure 中的虚拟机（Vm），可以将 Vm 加入到 Azure Active Directory 域服务（AD DS）托管域。 将 VM 加入到 Azure AD DS 托管域时，可以使用域中的用户帐户和凭据来登录和管理服务器。 还将应用来自 Azure AD DS 托管域的组成员身份，以便控制对 VM 上的文件或服务的访问。

本文介绍如何将 Ubuntu Linux VM 加入 Azure AD DS 托管域。

## <a name="prerequisites"></a>必备组件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请参考第一篇教程[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>创建并连接到 Ubuntu Linux VM

如果 Azure 中有现有的 Ubuntu Linux VM，请使用 SSH 连接到该 VM，然后继续执行下一步，[开始配置 VM](#configure-the-hosts-file)。

如果需要创建 Ubuntu Linux VM，或者想要创建一个用于本文的测试 VM，可以使用以下方法之一：

* [Azure 门户](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

创建 VM 时，请注意虚拟网络设置，以确保 VM 能够与 Azure AD DS 托管域通信：

* 将 VM 部署到相同的或对等互连的虚拟网络，在该网络中已启用 Azure AD 域服务。
* 将 VM 部署到不同于 Azure AD 域服务实例的子网中。

部署 VM 后，请遵循使用 SSH 连接到 VM 的步骤。

## <a name="configure-the-hosts-file"></a>配置 hosts 文件

若要确保为托管域正确配置了 VM 主机名，请编辑 */etc/hosts*文件，并设置主机名：

```console
sudo vi /etc/hosts
```

在*hosts*文件中，更新*localhost*地址。 在以下示例中：

* *contoso.com*是 Azure AD DS 托管域的 DNS 域名。
* *ubuntu*是你要加入到托管域的 ubuntu VM 的主机名。

请用自己的值更新这些名称：

```console
127.0.0.1 ubuntu.contoso.com ubuntu
```

完成后，使用编辑器的 `:wq` 命令保存并退出*hosts*文件。

## <a name="install-required-packages"></a>安装所需程序包

VM 需要其他一些包才能将 VM 加入到 Azure AD DS 托管域。 若要安装和配置这些包，请使用 `apt-get` 更新和安装域加入工具

在 Kerberos 安装过程中， *krb5.conf*包会提示所有大写的领域名称。 例如，如果 Azure AD DS 托管域的名称为*contoso.com*，请输入*CONTOSO.COM*作为领域。 安装会在 */etc/krb5.conf*配置文件中写入 `[realm]` 和 `[domain_realm]` 部分。 请确保将整个领域指定为全部大写：

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>配置网络时间协议（NTP）

为了使域通信正常工作，Ubuntu VM 的日期和时间必须与 Azure AD DS 托管域同步。 将 Azure AD DS 托管域的 NTP 主机名添加到 */etc/ntp.conf*文件。

1. 使用编辑器打开*ntp*文件：

    ```console
    sudo vi /etc/ntp.conf
    ```

1. 在*ntp*文件中，创建一个行以添加 Azure AD DS 托管域的 DNS 名称。 在下面的示例中，添加了*contoso.com*的条目。 使用自己的 DNS 名称：

    ```console
    server contoso.com
    ```

    完成后，使用编辑器的 `:wq` 命令保存并退出*ntp.*

1. 若要确保 VM 与 Azure AD DS 托管域同步，需要执行以下步骤：

    * 停止 NTP 服务器
    * 更新托管域中的日期和时间
    * 启动 NTP 服务

    运行以下命令来完成这些步骤。 使用你自己的 DNS 名称和 `ntpdate` 命令：

    ```console
    sudo systemctl stop ntp
    sudo ntpdate contoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>将 VM 加入托管域

现在，已在 VM 上安装了所需的包，并且已配置了 NTP，接下来将 VM 加入 Azure AD DS 托管域。

1. 使用 `realm discover` 命令发现 Azure AD DS 托管域。 以下示例发现领域*CONTOSO.COM*。 以全部大写的形式指定你自己 Azure AD DS 托管域名：

    ```console
    sudo realm discover CONTOSO.COM
    ```

   如果 `realm discover` 命令找不到你的 Azure AD DS 托管域，请查看以下故障排除步骤：

    * 请确保可从 VM 访问域。 尝试 `ping contoso.com` 以查看是否返回了肯定回复。
    * 检查是否已将 VM 部署到相同的或对等互连的虚拟网络，Azure AD DS 托管域在该网络中可用。
    * 确认已将虚拟网络的 DNS 服务器设置更新为指向 Azure AD DS 托管域的域控制器。

1. 现在使用 `kinit` 命令初始化 Kerberos。 指定属于*AAD DC 管理员*组的用户。 如果需要，请[将用户帐户添加到 Azure AD 中的组](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    同样，必须以全部大写的形式输入 Azure AD DS 托管域名。 在下面的示例中，名为 `contosoadmin@contoso.com` 的帐户用于初始化 Kerberos。 输入您自己的用户帐户，该帐户是*AAD DC Administrators*组的成员：

    ```console
    kinit contosoadmin@CONTOSO.COM
    ```

1. 最后，使用 `realm join` 命令将计算机加入到 Azure AD DS 托管域。 使用同一个用户帐户，该帐户是在上一个 `kinit` 命令中指定的*AAD DC Administrators*组的成员，如 `contosoadmin@CONTOSO.COM`：

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'contosoadmin@CONTOSO.COM' --install=/
    ```

将 VM 加入到 Azure AD DS 托管域需要一段时间。 以下示例输出显示 VM 已成功加入到 Azure AD DS 托管域：

```output
Successfully enrolled machine in realm
```

如果 VM 无法成功完成域加入过程，请确保 VM 的网络安全组允许 TCP + UDP 端口464上的出站 Kerberos 流量到 Azure AD DS 托管域的虚拟网络子网。

## <a name="update-the-sssd-configuration"></a>更新 SSSD 配置

上一步中安装的其中一个包适用于系统安全服务守护程序（SSSD）。 当用户尝试使用域凭据登录到 VM 时，SSSD 会将请求中继到身份验证提供程序。 在此方案中，SSSD 使用 Azure AD DS 来对请求进行身份验证。

1. 使用编辑器打开*sssd*文件：

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. 注释掉*use_fully_qualified_names*的行，如下所示：

    ```console
    # use_fully_qualified_names = True
    ```

    完成后，使用编辑器的 `:wq` 命令保存并退出*sssd*文件。

1. 若要应用更改，请重新启动 SSSD 服务：

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>配置用户帐户和组设置

VM 加入到 Azure AD DS 托管域并配置为进行身份验证时，需要完成一些用户配置选项。 这些配置更改包括允许基于密码的身份验证，并在域用户首次登录时在本地 VM 上自动创建主目录。

### <a name="allow-password-authentication-for-ssh"></a>允许对 SSH 进行密码身份验证

默认情况下，用户只能使用基于 SSH 公钥的身份验证登录到 VM。 基于密码的身份验证失败。 将 VM 加入到 Azure AD DS 托管域时，这些域帐户需要使用基于密码的身份验证。 更新 SSH 配置，以允许基于密码的身份验证，如下所示。

1. 使用编辑器打开*sshd_conf*文件：

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. 将*PasswordAuthentication*的行更新为 *"是"* ：

    ```console
    PasswordAuthentication yes
    ```

    完成后，使用编辑器的 `:wq` 命令保存并退出*sshd_conf*文件。

1. 若要应用更改并让用户使用密码进行登录，请重新启动 SSH 服务：

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>配置自动主目录创建

若要在用户首次登录时启用主目录的自动创建，请完成以下步骤：

1. 在编辑器中打开 */etc/pam.d/common-session*文件：

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. 将以下行添加到下面的行 `session optional pam_sss.so`：

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    完成后，使用编辑器的 `:wq` 命令保存并退出*公用会话*文件。

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>为“AAD DC 管理员”组授予 sudo 特权

若要授予*AAD DC Administrators*组成员对 Ubuntu VM 的管理权限，请向 */etc/sudoers*添加一个条目。 添加后， *AAD DC 管理员*组的成员可以在 Ubuntu VM 上使用 `sudo` 命令。

1. 打开*sudoers*文件进行编辑：

    ```console
    sudo visudo
    ```

1. 将以下条目添加到 */etc/sudoers*文件的末尾：

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    完成后，请使用 `Ctrl-X` 命令保存并退出编辑器。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用域帐户登录到 VM

若要验证 VM 是否已成功加入到 Azure AD DS 托管域，请使用域用户帐户启动新的 SSH 连接。 确认已创建主目录，并且已应用域的组成员身份。

1. 从控制台创建新的 SSH 连接。 使用 "`ssh -l`" 命令（如 `contosoadmin@contoso.com`）使用属于托管域的域帐户，然后输入 VM 的地址，例如*ubuntu.contoso.com*。 如果使用 Azure Cloud Shell，请使用 VM 的公共 IP 地址，而不使用内部 DNS 名称。

    ```console
    ssh -l contosoadmin@CONTOSO.com ubuntu.contoso.com
    ```

1. 成功连接到 VM 后，验证是否已正确初始化主目录：

    ```console
    pwd
    ```

    你应在 */home*目录中，并将你自己的目录与用户帐户相匹配。

1. 现在请检查是否已正确解析组成员身份：

    ```console
    id
    ```

    应会看到来自 Azure AD DS 托管域的组成员身份。

1. 如果以*AAD DC Administrators*组成员的身份登录到 VM，请检查是否可以正确使用 `sudo` 命令：

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>后续步骤

如果在将 VM 连接到 Azure AD DS 托管域或使用域帐户登录时遇到问题，请参阅[排查域加入问题](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
