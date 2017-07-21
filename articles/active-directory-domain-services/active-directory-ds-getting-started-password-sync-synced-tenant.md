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
ms.date: 06/30/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 947ea3c9d789ecf5a754001aafcda6f8bcd41047
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---
# 允许将密码同步到 Azure Active Directory 域服务
<a id="enable-password-synchronization-to-azure-active-directory-domain-services" class="xliff"></a>
在前述任务中，你为 Azure Active Directory (Azure AD) 租户启用了 Azure Active Directory 域服务。 下一个任务是使 NT LAN Manager (NTLM) 和 Kerberos 身份验证所需的凭据哈希同步到 Azure AD 域服务。 设置凭据同步以后，用户即可使用其公司凭据登录到托管域。

对于仅限云的用户帐户来说，所涉及的步骤不同于使用 Azure AD Connect 从本地目录同步的用户帐户。 如果 Azure AD 租户既有仅限云的用户，又有来自本地 AD 的用户，你需要执行两个步骤。

<br>

> [!div class="op_single_selector"]
> * 仅限云的用户帐户：[将仅限云的用户帐户的密码同步到托管域](active-directory-ds-getting-started-password-sync.md)
> * 本地用户帐户：[将从本地 AD 同步的用户帐户的密码同步到托管域](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## 任务 5：为使用本地 AD 同步的用户帐户启用到托管域的密码同步
<a id="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad" class="xliff"></a>
已同步的 Azure AD 租户设置为使用 Azure AD Connect 与组织的本地目录同步。 默认情况下，Azure AD Connect 不会将 NTLM 和 Kerberos 凭据哈希同步到 Azure AD。 若要使用 Azure AD 域服务，需要将 Azure AD Connect 配置为同步 NTLM 和 Kerberos 身份验证所需的凭据哈希。 通过以下步骤可将所需的凭据哈希从本地目录同步到 Azure AD 租户。

> [!NOTE]
> 如果组织的用户帐户是从本地目录同步的，则必须启用 NTLM 和 Kerberos 哈希的同步，然后才能使用托管域。 已同步用户帐户是指在本地目录中创建并使用 Azure AD Connect 同步到 Azure AD 租户中的帐户。
>
>

### 安装或更新 Azure AD Connect
<a id="install-or-update-azure-ad-connect" class="xliff"></a>
在加入域的计算机上安装 Azure AD Connect 的最新建议的版本。 如果有 Azure AD Connect 安装的现有实例，需要更新该实例以使用最新版本的 Azure AD Connect。 若要避免可能已修复的已知的问题/bug，请确保始终使用最新版本的 Azure AD Connect。

**[下载 Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

推荐的版本：1.1.553.0 - 于 2017 年 6 月 27 日发布。

> [!WARNING]
> 必须安装推荐的最新的 Azure AD Connect 版本，才能将旧密码凭据（NTLM 和 Kerberos 身份验证需要的）同步到 Azure AD 租户。 此功能不可用于之前的 Azure AD Connect 版本或旧版的 DirSync 工具。
>
>

有关 Azure AD connect 的安装说明，请参阅以下文章 — [Azure AD Connect 入门](../active-directory/active-directory-aadconnect.md)

### 启用 Azure AD 的 NTLM 和 Kerberos 凭据哈希的同步
<a id="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad" class="xliff"></a>
针对每个AD 林执行以下 PowerShell 脚本，以强制执行完整的密码同步，并将所有本地用户的凭据哈希同步到 Azure AD 租户。 通过此脚本可将 NTLM/Kerberos 身份验证所需的凭据哈希同步到 Azure AD 租户。

```
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

## 相关内容
<a id="related-content" class="xliff"></a>
* [对仅限云的 Azure AD 目录启用 AAD 域服务的密码同步](active-directory-ds-getting-started-password-sync.md)
* [管理受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-administer-domain.md)
* [将 Windows 虚拟机加入到受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-join-windows-vm.md)
* [将 Red Hat Enterprise Linux 虚拟机加入到受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

