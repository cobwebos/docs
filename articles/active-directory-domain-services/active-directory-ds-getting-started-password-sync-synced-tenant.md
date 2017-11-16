---
title: "Azure AD 域服务：启用密码同步 | Microsoft Docs"
description: "Azure Active Directory 域服务入门"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: b658bc91723c8384b91eb62e8506c82aa5112509
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>允许将密码同步到 Azure Active Directory 域服务
在前述任务中，你为 Azure Active Directory (Azure AD) 租户启用了 Azure Active Directory 域服务。 下一个任务是使 NT LAN Manager (NTLM) 和 Kerberos 身份验证所需的凭据哈希同步到 Azure AD 域服务。 设置凭据同步以后，用户即可使用其公司凭据登录到托管域。

对于仅限云的用户帐户来说，所涉及的步骤不同于使用 Azure AD Connect 从本地目录同步的用户帐户。

<br>
| **用户帐户的类型** | **要执行的步骤** |
| --- | --- |
| **从本地目录同步的用户帐户** |**&#x2713;** [按此文中的说明操作](active-directory-ds-getting-started-password-sync-synced-tenant.md#task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad) | 
| **在 Azure AD 中创建的云用户帐户** |**&#x2713;** [将仅限云的用户帐户的密码同步到托管域](active-directory-ds-getting-started-password-sync.md) |
<br>

> [!TIP]
> **可能需要完成这两组步骤。**
> 如果 Azure AD 租户既有仅限云的用户，又有来自本地 AD 的用户，则需完成两组步骤。
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>任务 5：为使用本地 AD 同步的用户帐户启用到托管域的密码同步
已同步的 Azure AD 租户设置为使用 Azure AD Connect 与组织的本地目录同步。 默认情况下，Azure AD Connect 不会将 NTLM 和 Kerberos 凭据哈希同步到 Azure AD。 要使用 Azure AD 域服务，需要将 Azure AD Connect 配置为同步 NTLM 和 Kerberos 身份验证所需的凭据哈希。 通过以下步骤可将所需的凭据哈希从本地目录同步到 Azure AD 租户。

> [!NOTE]
> **如果组织的用户帐户是从本地目录同步的，则必须启用 NTLM 和 Kerberos 哈希的同步，然后才能使用托管域。** 已同步用户帐户是指在本地目录中创建并使用 Azure AD Connect 同步到 Azure AD 租户中的帐户。
>
>

### <a name="install-or-update-azure-ad-connect"></a>安装或更新 Azure AD Connect
在加入域的计算机上安装 Azure AD Connect 的最新建议的版本。 如果有 Azure AD Connect 安装的现有实例，需要更新该实例以使用最新版本的 Azure AD Connect。 若要避免可能已修复的已知问题/Bug，请始终使用最新版本的 Azure AD Connect。

**[下载 Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

推荐的版本：**1.1.614.0** - 于 2017 年 9 月 5 日发布。

> [!WARNING]
> 必须安装推荐的最新的 Azure AD Connect 版本，才能将旧密码凭据（NTLM 和 Kerberos 身份验证需要的）同步到 Azure AD 租户。 此功能不可用于之前的 Azure AD Connect 版本或旧版的 DirSync 工具。
>
>

有关 Azure AD connect 的安装说明，请参阅以下文章 — [Azure AD Connect 入门](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>启用 Azure AD 的 NTLM 和 Kerberos 凭据哈希的同步
对每个 AD 林执行以下 PowerShell 脚本。 可以通过此脚本将所有本地用户的 NTLM 和 Kerberos 密码哈希同步到 Azure AD 租户。 脚本还在 Azure AD Connect 中启动完全同步。

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Azure AD 的凭据哈希同步需要一些时间，具体取决于目录的大小（用户、组的数量等）。 将凭据哈希同步到 Azure AD 后不久，即可在受 Azure AD 域服务管理的域中使用密码。

<br>

## <a name="related-content"></a>相关内容
* [对仅限云的 Azure AD 目录启用 AAD 域服务的密码同步](active-directory-ds-getting-started-password-sync.md)
* [管理受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-administer-domain.md)
* [将 Windows 虚拟机加入到受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-join-windows-vm.md)
* [将 Red Hat Enterprise Linux 虚拟机加入到受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
