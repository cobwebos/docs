---
title: 将 CentOS VM 加入 Azure AD 域服务 |Microsoft Docs
description: 了解如何配置 CentOS Linux 虚拟机并将其加入到 Azure AD 域服务托管域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 0c49624a28950cd3625ee5f6bb565fe8a1e7fd7a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712639"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>将 CentOS Linux 虚拟机加入 Azure AD 域服务托管域

若要让用户使用一组凭据登录到 Azure 中的虚拟机（Vm），可以将 Vm 加入到 Azure Active Directory 域服务（AD DS）托管域。 将 VM 加入到 Azure AD DS 托管域时，可以使用域中的用户帐户和凭据来登录和管理服务器。 还将应用来自 Azure AD DS 托管域的组成员身份，以便控制对 VM 上的文件或服务的访问。

本文介绍了如何将 CentOS Linux VM 加入到 Azure AD DS 托管域。

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="create-and-connect-to-a-centos-linux-vm"></a>创建并连接到 CentOS Linux VM

如果 Azure 中有现有的 CentOS Linux VM，请使用 SSH 连接到该 VM，然后继续执行下一步，[开始配置 VM](#configure-the-hosts-file)。

如果需要创建 CentOS Linux VM，或者想要创建用于本文的测试 VM，可以使用以下方法之一：

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

创建 VM 时，请注意虚拟网络设置，以确保 VM 能够与 Azure AD DS 托管域通信：

* 将 VM 部署到相同的或对等互连的虚拟网络，在该网络中已启用 Azure AD 域服务。
* 将 VM 部署到不同于 Azure AD 域服务实例的子网中。

部署 VM 后，请遵循使用 SSH 连接到 VM 的步骤。

## <a name="configure-the-hosts-file"></a>hosts 파일 구성

若要确保为托管域正确配置了 VM 主机名，请编辑 */etc/hosts*文件，并设置主机名：

```console
sudo vi /etc/hosts
```

在*hosts*文件中，更新*localhost*地址。 다음 예제에서,

* *aadds.contoso.com*是 Azure AD DS 托管域的 DNS 域名。
* *centos*是你要加入到托管域的 centos VM 的主机名。

请用自己的值更新这些名称：

```console
127.0.0.1 centos.aadds.contoso.com centos
```

完成后，使用编辑器的 `:wq` 命令保存并退出*hosts*文件。

## <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

VM 需要其他一些包才能将 VM 加入到 Azure AD DS 托管域。 若要安装和配置这些包，请使用 `yum`更新和安装域加入工具：

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>将 VM 加入托管域

现在，所需的包已安装到 VM 上，请将 VM 加入到 Azure AD DS 托管域。

1. 使用 `realm discover` 命令发现 Azure AD DS 托管域。 以下示例发现领域*AADDS。CONTOSO.COM*。 以全部大写的形式指定你自己 Azure AD DS 托管域名：

    ```console
    sudo realm discover AADDS.CONTOSO.COM
    ```

   如果 `realm discover` 命令找不到你的 Azure AD DS 托管域，请查看以下故障排除步骤：

    * 请确保可从 VM 访问域。 尝试 `ping aadds.contoso.com` 以查看是否返回了肯定回复。
    * 检查是否已将 VM 部署到相同的或对等互连的虚拟网络，Azure AD DS 托管域在该网络中可用。
    * 确认已将虚拟网络的 DNS 服务器设置更新为指向 Azure AD DS 托管域的域控制器。

1. 现在使用 `kinit` 命令初始化 Kerberos。 指定属于*AAD DC 管理员*组的用户。 如果需要，请[将用户帐户添加到 Azure AD 中的组](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    同样，必须以全部大写的形式输入 Azure AD DS 托管域名。 在下面的示例中，名为 `contosoadmin@aadds.contoso.com` 的帐户用于初始化 Kerberos。 输入您自己的用户帐户，该帐户是*AAD DC Administrators*组的成员：

    ```console
    kinit contosoadmin@AADDS.CONTOSO.COM
    ```

1. 最后，使用 `realm join` 命令将计算机加入到 Azure AD DS 托管域。 使用同一个用户帐户，该帐户是在上一个 `kinit` 命令中指定的*AAD DC Administrators*组的成员，如 `contosoadmin@AADDS.CONTOSO.COM`：

    ```console
    sudo realm join --verbose AADDS.CONTOSO.COM -U 'contosoadmin@AADDS.CONTOSO.COM'
    ```

将 VM 加入到 Azure AD DS 托管域需要一段时间。 以下示例输出显示 VM 已成功加入到 Azure AD DS 托管域：

```output
Successfully enrolled machine in realm
```

如果 VM 无法成功完成域加入过程，请确保 VM 的网络安全组允许 TCP + UDP 端口464上的出站 Kerberos 流量到 Azure AD DS 托管域的虚拟网络子网。

## <a name="allow-password-authentication-for-ssh"></a>允许对 SSH 进行密码身份验证

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
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>'AAD DC Administrators' 그룹 sudo 권한 부여

若要授予*AAD DC Administrators*组成员对 CentOS VM 的管理权限，请向 */etc/sudoers*添加一个条目。 添加后， *AAD DC 管理员*组的成员可以在 CentOS VM 上使用 `sudo` 命令。

1. 打开*sudoers*文件进行编辑：

    ```console
    sudo visudo
    ```

1. 将以下条目添加到 */etc/sudoers*文件的末尾。 *AAD DC 管理员*组的名称中包含空格，因此请在组名称中包含反斜杠转义符。 添加你自己的域名，例如*aadds.contoso.com*：

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aadds.contoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    完成后，使用编辑器的 `:wq` 命令保存并退出编辑器。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用域帐户登录到 VM

若要验证 VM 是否已成功加入到 Azure AD DS 托管域，请使用域用户帐户启动新的 SSH 连接。 确认已创建主目录，并且已应用域的组成员身份。

1. 从控制台创建新的 SSH 连接。 使用 "`ssh -l`" 命令（如 `contosoadmin@contoso.com`）使用属于托管域的域帐户，然后输入 VM 的地址，例如*centos.aadds.contoso.com*。 如果使用 Azure Cloud Shell，请使用 VM 的公共 IP 地址，而不使用内部 DNS 名称。

    ```console
    ssh -l contosoadmin@AADDS.CONTOSO.com centos.aadds.contoso.com
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
    sudo yum update
    ```

## <a name="next-steps"></a>다음 단계

如果在将 VM 连接到 Azure AD DS 托管域或使用域帐户登录时遇到问题，请参阅[排查域加入问题](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
