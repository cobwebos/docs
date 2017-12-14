---
title: "Azure Active Directory 域服务：将 CoreOS Linux VM 加入托管域 | Microsoft Docs"
description: "将 CoreOS Linux 虚拟机加入 Azure AD 域服务"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: 790ad85df0dbf68674e2b9c6254858100ddfd0fd
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>将 CoreOS Linux 虚拟机加入托管域
本文介绍如何将 Azure 中的 CoreOS Linux 虚拟机加入 Azure AD 域服务托管域。

## <a name="before-you-begin"></a>开始之前
若要执行本文中所列的任务，需要：
1. 一个有效的 **Azure 订阅**。
2. 一个 **Azure AD 目录** - 已与本地目录或仅限云的目录同步。
3. 必须为 Azure AD 目录启用 **Azure AD 域服务**。 如果未启用，请遵循[入门指南](active-directory-ds-getting-started.md)中所述的所有任务。
4. 请确保已将托管域的 IP 地址配置为虚拟网络的 DNS 服务器。 有关详细信息，请参阅[如何更新 Azure 虚拟网络的 DNS 设置](active-directory-ds-getting-started-dns.md)
5. 完成[将密码同步到 Azure AD 域服务托管域](active-directory-ds-getting-started-password-sync.md)所要执行的步骤。


## <a name="provision-a-coreos-linux-virtual-machine"></a>预配 CoreOS Linux 虚拟机
使用以下任一方法在 Azure 中预配 CoreOS虚拟机：
* [Azure 门户](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

本文将使用 Azure 中的 CoreOS Linux（稳定版）虚拟机映像。

> [!IMPORTANT]
> * 将虚拟机部署到**已在其中启用了 Azure AD 域服务的同一个虚拟网络**。
> * 选取与已启用 Azure AD 域服务的子网**不同的子网**。
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>远程连接到新预配的 Linux 虚拟机
CoreOS 虚拟机已在 Azure 中预配。 下一个任务是使用在预配 VM 时创建的本地管理员帐户远程连接到虚拟机。

请遵循[如何登录到运行 Linux 的虚拟机](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文中的说明。


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>配置 Linux 虚拟机上的 hosts 文件
在 SSH 终端中编辑 /etc/hosts 文件，并更新计算机的 IP 地址和主机名。

```
sudo vi /etc/hosts
```

在 hosts 文件中输入以下值：

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
此处，“contoso100.com”是托管域的 DNS 域名。 “contoso-coreos”是要加入托管域的 CoreOS 虚拟机的主机名。


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>在 Linux 虚拟机上配置 SSSD 服务
接下来，更新 (“/etc/sssd/sssd.conf”) 中的 SSSD 配置文件以匹配以下示例：

 ```
 [sssd]
 config_file_version = 2
 services = nss, pam
 domains = CONTOSO100.COM

 [domain/CONTOSO100.COM]
 id_provider = ad
 auth_provider = ad
 chpass_provider = ad

 ldap_uri = ldap://contoso100.com
 ldap_search_base = dc=contoso100,dc=com
 ldap_schema = rfc2307bis
 ldap_sasl_mech = GSSAPI
 ldap_user_object_class = user
 ldap_group_object_class = group
 ldap_user_home_directory = unixHomeDirectory
 ldap_user_principal = userPrincipalName
 ldap_account_expire_policy = ad
 ldap_force_upper_case_realm = true
 fallback_homedir = /home/%d/%u

 krb5_server = contoso100.com
 krb5_realm = CONTOSO100.COM
 ```
将“CONTOSO100.COM”替换为托管域的 DNS 域名。 确保在配置文件中指定大写域名。


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>将 Linux 虚拟机加入托管域
在 Linux 虚拟机上安装所需的包后，下一个任务是将虚拟机加入托管域。

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> 故障排除：如果“adcli”找不到托管域：
  * 确保域可从虚拟机（请尝试 ping）进行访问。
  * 检查虚拟机是否已确实部署到提供托管域的同一个虚拟网络。
  * 检查是否已将虚拟网络的 DNS 服务器设置更新为指向托管域的域控制器。
>

启动 SSSD 服务。 在 SSH 终端中键入以下命令：
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>验证域加入
验证计算机是否已成功加入托管域。 使用不同的 SSH 连接来连接到已加入域的 CoreOS VM。 使用域用户帐户连接，并检查该用户帐户是否正确解析。

1. 在 SSH 终端中键入以下命令，使用 SSH 连接到已加入域的 CoreOS 虚拟机。 使用属于托管域的域帐户（例如，在本例中为“bob@CONTOSO100.COM”）。
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
    ```

2. 在 SSH 终端中键入以下命令，查看是否已正确初始化主目录。
    ```
    pwd
    ```

3. 在 SSH 终端中键入以下命令，查看组成员身份是否正确解析。
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>排查域加入问题
请参阅[排查域加入问题](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshooting-domain-join)一文。

## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [将 Windows Server 虚拟机加入 Azure AD 域服务托管域](active-directory-ds-admin-guide-join-windows-vm.md)
* [如何登录到运行 Linux 的虚拟机](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
