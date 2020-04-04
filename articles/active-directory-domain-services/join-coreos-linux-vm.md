---
title: 将 CoreOS VM 加入 Azure AD 域服务 |微软文档
description: 了解如何配置 CoreOS 虚拟机并将其加入 Azure AD 域服务托管域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 63dfe39b986125abc9cacf6c1a6556876bbd3a99
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655196"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>将 CoreOS 虚拟机加入 Azure AD 域服务托管域

要允许用户使用一组凭据登录到 Azure 中的虚拟机 （VM），可以将 VM 加入 Azure 活动目录域服务 （AD DS） 托管域。 将 VM 加入 Azure AD DS 托管域时，可以使用域中的用户帐户和凭据登录和管理服务器。 Azure AD DS 托管域中的组成员身份也应用于允许您控制对 VM 上文件或服务的访问。

本文介绍如何将 CoreOS VM 加入 Azure AD DS 托管域。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果没有 Azure 订阅，[请创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请参考第一篇教程[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。
* 属于 Azure AD DS 托管域的用户帐户。

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>创建并连接到 CoreOS Linux VM

如果在 Azure 中具有现有的 CoreOS Linux VM，请使用 SSH 连接到它，然后继续执行下一步[以开始配置 VM](#configure-the-hosts-file)。

如果需要创建 CoreOS Linux VM，或者想要创建用于本文的测试 VM，可以使用以下方法之一：

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
* *coreos*是要加入托管域的 CoreOS VM 的主机名。

使用您自己的值更新这些名称：

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

完成后，使用编辑器`:wq`的命令保存并退出*主机*文件。

## <a name="configure-the-sssd-service"></a>配置 SSSD 服务

更新 */etc/ssd/ssd.conf* SSSD 配置。

```console
sudo vi /etc/sssd/sssd.conf
```

为以下参数指定您自己的 Azure AD DS 托管域名：

* 所有上部案例中的*域*
* *[域/AADDS]* 其中 AADDS 位于所有上部案例中
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *所有*上部案例中的krb5_realm

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>将 VM 加入托管域

更新 SSSD 配置文件后，现在将虚拟机加入托管域。

1. 首先，使用`adcli info`命令验证可以看到有关 Azure AD DS 托管域的信息。 下面的示例获取域*AADDSCONTOSO.COM*的信息。 在"所有上写"中指定您自己的 Azure AD DS 托管域名：

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   如果`adcli info`命令找不到 Azure AD DS 托管域，请查看以下故障排除步骤：

    * 确保可以从 VM 联系到域。 尝试`ping aaddscontoso.com`查看是否返回了肯定的答复。
    * 检查 VM 是否部署到 Azure AD DS 托管域可用的同一虚拟网络或对等虚拟网络。
    * 确认虚拟网络的 DNS 服务器设置已更新以指向 Azure AD DS 托管域的域控制器。

1. 现在使用`adcli join`命令将 VM 加入 Azure AD DS 托管域。 指定属于 Azure AD DS 托管域的用户。 如果需要[，将用户帐户添加到 Azure AD 中的组](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    同样，必须在所有上部操作中输入 Azure AD DS 托管域名。 在下面的示例中，命名的`contosoadmin@aaddscontoso.com`帐户用于初始化 Kerberos。 输入属于 Azure AD DS 托管域的用户帐户。

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    当`adcli join`VM 成功加入 Azure AD DS 托管域时，该命令不会返回任何信息。

1. 要应用域联接配置，请启动 SSSD 服务：
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>使用域帐户登录到 VM

要验证 VM 已成功加入 Azure AD DS 托管域，请使用域用户帐户启动新的 SSH 连接。 确认已创建主目录，并应用域中的组成员身份。

1. 从控制台创建新的 SSH 连接。 使用`ssh -l`命令使用属于托管域的域帐户，例如`contosoadmin@aaddscontoso.com`，然后输入 VM 的地址，如*coreos.aaddscontoso.com*。 如果使用 Azure 云外壳，请使用 VM 的公共 IP 地址，而不是内部 DNS 名称。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. 现在检查组成员身份是否得到正确解决：

    ```console
    id
    ```

    您应该从 Azure AD DS 托管域中看到组成员身份。

## <a name="next-steps"></a>后续步骤

如果在将 VM 连接到 Azure AD DS 托管域或使用域帐户登录时遇到问题，请参阅[排除域联接问题](join-windows-vm.md#troubleshoot-domain-join-issues)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
