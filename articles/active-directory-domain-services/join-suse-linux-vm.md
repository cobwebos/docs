---
title: 将 SLE VM 加入到 Azure AD 域服务 | Microsoft Docs
description: 了解如何配置 SUSE Linux Enterprise 虚拟机并将其加入到 Azure AD 域服务托管域。
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: joflore
ms.openlocfilehash: 5d89f1a3d6028afb3450e0112a6081c9c706775b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962456"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>将 SUSE Linux Enterprise 虚拟机加入到 Azure Active Directory 域服务托管域

若要让用户使用一组凭据登录到 Azure 中的虚拟机 (VM)，可以将 VM 加入到 Azure Active Directory 域服务 (Azure AD DS) 托管域。 将 VM 加入到 Azure AD DS 托管域时，可以使用域中的用户帐户和凭据来登录和管理服务器。 托管域中的组成员身份也应用于控制对 VM 上的文件或服务的访问。

本文介绍了如何将 SUSE Linux Enterprise (SLE) VM 加入到托管域。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请参考第一篇教程[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]。
* 属于托管域的用户帐户。

## <a name="create-and-connect-to-a-sle-linux-vm"></a>创建和连接 SLE Linux VM

如果 Azure 中有现有的 SLE Linux VM，请使用 SSH 连接到该 VM，然后继续执行下一步，[开始配置 VM](#configure-the-hosts-file)。

如果需要创建 SLE Linux VM，或者想要创建用于本文的测试 VM，可以使用以下方法之一：

* [Azure 门户](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

创建 VM 时，请注意虚拟网络设置，确保 VM 可以与托管域通信：

* 将该 VM 部署到已启用 Azure AD 域服务的虚拟网络或与其对等互连的虚拟网络。
* 将 VM 部署到与 Azure AD 域服务托管域不同的子网中。

部署 VM 后，请遵循使用 SSH 连接到 VM 的步骤。

## <a name="configure-the-hosts-file"></a>配置主机文件

若要确保为托管域正确配置了 VM 主机名，请编辑“/etc/hosts”文件，并设置主机名：

```console
sudo vi /etc/hosts
```

在 hosts 文件中，更新 localhost 地址 。 在以下示例中：

* aaddscontoso.com 是托管域的 DNS 域名。
* linux-q2gr 是要加入到托管域的 SLE VM 的主机名。

将以下名称更新为你自己的值：

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

完成后，使用编辑器的 `:wq` 命令保存并退出 hosts 文件。

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>使用 SSSD 将 VM 加入托管域

若要使用 **SSSD** 和 YaST 的“用户登录管理”模块加入托管域，请完成以下步骤：

1. 安装 YaST 的“用户登录管理”模块：

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. 打开 YaST。

1. 要想以后成功使用 DNS 自动发现，请将托管域 IP 地址（Active Directory 服务器）配置为你的客户端的名称服务器。

    在 YaST 中，选择“系统”>“网络设置”。

1. 选择“主机名/DNS”选项卡，然后将托管域的 IP 地址输入到“名称服务器 1”文本框中。 这些 IP 地址显示在 Azure 门户中你的托管域的“属性”窗口中，例如 10.0.2.4 和 10.0.2.5。

    添加你自己的托管域 IP 地址，然后选择“确定”。

1. 从 YaST 主窗口中，选择“网络服务” > “用户登录管理”。

    此模块会打开，其中的概览显示你的计算机的各种网络属性以及当前在使用的身份验证方法，如以下示例屏幕截图所示：

    ![YaST 中“用户登录管理”窗口的示例屏幕截图](./media/join-suse-linux-vm/overview-window.png)

    若要开始编辑，请选择“更改设置”。

若要将 VM 加入托管域，请完成以下步骤：

1. 在对话框中，选择“添加域”。

1. 指定正确的域名，例如 aaddscontoso.com，然后指定用于标识数据和身份验证的服务。 对于两者，都选择“Microsoft Active Directory”。

    确保选中“启用域”选项。

1. 准备就绪后，选择“确定”。

1. 接受以下对话框中的默认设置，然后选择“确定”。

1. VM 会根据需要安装其他软件，然后检查托管域是否可用。

    如果所有内容都正确，则会显示以下示例对话框，指示 VM 已发现托管域，但你尚未注册。

    ![YaST 中 Active Directory 注册窗口的示例屏幕截图](./media/join-suse-linux-vm/enroll-window.png)

1. 在对话框中，指定属于托管域的某个用户的用户名和密码。  如有必要，[将用户帐户添加到 Azure AD 中的组](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    若要确保为 Samba 启用当前域，请激活“覆盖 Samba 配置以使用此 AD”。

1. 若要注册，请选择“确定”。

1. 此时会显示一条消息，确认你已成功注册。 若要完成此操作，请选择“确定”。

在托管域中注册 VM 后，使用“管理域用户登录”配置客户端，如以下示例屏幕截图所示：

![YaST 中“管理域用户登录”窗口的示例屏幕截图](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. 若要允许使用托管域提供的数据进行登录，请选中“允许域用户登录”复选框。

1. （可选）在“启用域数据源”下，根据需要为你的环境选中其他数据源。 这些选项包括哪些用户可以使用 **sudo** 或哪些网络驱动器可用。

1. 若要允许托管域中的用户在 VM 上具有主目录，请选中“创建主目录”复选框。

1. 从侧栏中选择“服务选项”›“Name 开关”，然后选择“扩展选项”。 从该窗口中选择 fallback_homedir 或 override_homedir，然后选择“添加”。

1. 指定主目录位置的值。 若要使主目录遵循 /home/USER_NAME 格式，请使用 /home/%u。 有关可能的变量的详细信息，请参阅 sssd.conf 手册页 (`man 5 sssd.conf`) 的 override_homedir 部分。

1. 选择“确定”。

1. 若要保存更改，请选择“确定”。 请确保现在显示的值正确。 若要退出对话框，请选择“取消”。

1. 如果要同时运行 SSSD 和 Winbind（例如先通过 SSSD 加入，然后运行 Samba 文件服务器），则应在 smb.conf 中将 Samba 选项“kerberos method”设置为“secrets and keytab”。 还应在 sssd.conf 中将 SSSD 选项 ad_update_samba_machine_account_password 设置为 true。 这些选项可防止系统密钥表不同步。

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>使用 Winbind 将 VM 加入托管域

若要使用 **winbind** 和 YaST 的“Windows 域成员身份”模块加入托管域，请完成以下步骤：

1. 在 YaST 中，选择“网络服务”>“Windows 域成员身份”。

1. 在“Windows 域成员身份”屏幕的“域或工作组”中输入要加入的域。 输入托管域名称，例如 aaddscontoso.com。

    ![YaST 中“Windows 域成员身份”窗口的示例屏幕截图](./media/join-suse-linux-vm/samba-client-window.png)

1. 若要将 SMB 源用于 Linux 身份验证，请选中“使用 SMB 信息进行 Linux 身份验证”选项。

1. 若要在 VM 上自动为托管域用户创建本地主目录，请选中“登录时创建主目录”选项。

1. 选中“脱机身份验证”选项，使域用户即便在托管域暂时不可用的情况下也能够登录。

1. 如果要更改 Samba 用户和组的 UID 和 GID 范围，请选择“专家设置”。

1. 通过选择“NTP 配置”为托管域配置 NTP 时间同步。 输入托管域的 IP 地址。 这些 IP 地址显示在 Azure 门户中你的托管域的“属性”窗口中，例如 10.0.2.4 和 10.0.2.5。

1. 在系统提示时选择“确定”以确认加入域。

1. 提供托管域中某个管理员的密码，然后选择“确定”。

    ![将 SLE VM 加入托管域时的身份验证对话提示的示例屏幕截图](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

加入托管域后，可以使用桌面的显示管理器或控制台从工作站登录到该托管域。

## <a name="allow-password-authentication-for-ssh"></a>允许对 SSH 进行密码身份验证

默认情况下，用户只能使用基于 SSH 公钥的身份验证登录到 VM。 基于密码的身份验证失败。 将 VM 加入托管域时，这些域帐户需要使用基于密码的身份验证。 更新 SSH 配置，以允许基于密码的身份验证，如下所示。

1. 使用编辑器打开 sshd_conf 文件：

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. 将 PasswordAuthentication 的行更新为 yes ：

    ```console
    PasswordAuthentication yes
    ```

    完成后，使用编辑器的 `:wq` 命令保存并退出 sshd_conf 文件。

1. 若要应用更改并让用户使用密码登录，请重新启动 SSH 服务：

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>为“AAD DC 管理员”组授予 sudo 特权

若要授予“AAD DC 管理员”组成员对 SLE VM 的管理特权，请向“/etc/sudoers”添加一个条目 。 添加后，“AAD DC 管理员”组的成员可以使用 SLE VM 上的 `sudo` 命令。

1. 打开 sudoers 文件进行编辑：

    ```console
    sudo visudo
    ```

1. 将以下条目添加到“/etc/sudoers”文件的末尾。 “AAD DC 管理员”组的名称中包含空格，因此请在组名称中包含反斜杠转义符。 添加自己的域名，例如 aaddscontoso.com：

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    完成后，使用编辑器的 `:wq` 命令进行保存并退出编辑器。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用域帐户登录到 VM

若要验证 VM 是否已成功加入托管域，请使用域用户帐户启动新的 SSH 连接。 确认已创建主目录，并且已应用域的组成员身份。

1. 从控制台创建新的 SSH 连接。 通过 `ssh -l` 命令使用属于托管域的域帐户（例如 `contosoadmin@aaddscontoso.com`），然后输入 VM 的地址（例如“linux-q2gr.aaddscontoso.com”）。 如果使用 Azure Cloud Shell，请使用 VM 的公共 IP 地址，而不使用内部 DNS 名称。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. 成功连接到 VM 后，验证是否已正确初始化主目录：

    ```console
    pwd
    ```

    你应位于“/home”目录中，并具有你自己的与用户帐户相匹配的目录。

3. 现在请检查是否已正确解析组成员身份：

    ```console
    id
    ```

    应会看到托管域中的组成员身份。

4. 如果以“AAD DC 管理员”组成员的身份登录到 VM，请检查是否可以正确使用 `sudo` 命令：

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>后续步骤

如果在将 VM 连接到托管域或使用域帐户登录时遇到问题，请参阅[域加入问题故障排除](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
