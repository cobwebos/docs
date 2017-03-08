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
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9c43a831768684c6458d5f62557c8a06b2c5ca6b
ms.lasthandoff: 12/08/2016


---
# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>启用 Azure AD 域服务的密码同步
在前面的任务中，为 Azure AD 租户启用了 Azure AD 域服务。 下一个任务是启用 Azure AD 域服务的密码同步。 一旦设置凭据同步，用户可以使用其公司凭据登录到托管域。

所涉及的步骤是不同的，具体要取决于组织是具有仅限云的 Azure AD 租户，还是被设置为与使用 Azure AD Connect 的本地目录同步。

<br>

> [!div class="op_single_selector"]
> * [仅限云的 Azure AD 租户](active-directory-ds-getting-started-password-sync.md)
> * [已同步的 Azure AD 租户](active-directory-ds-getting-started-password-sync-synced-tenant.md)
>
>

<br>

## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-synced-azure-ad-tenant"></a>任务 5：对已同步的 Azure AD 租户启用 AAD 域服务的密码同步
已同步的 Azure AD 租户设置为使用 Azure AD Connect 与组织的本地目录同步。 默认情况下，Azure AD Connect 不会将 NTLM 和 Kerberos 凭据哈希同步到 Azure AD。 若要使用 Azure AD 域服务，需要将 Azure AD Connect 配置为同步 NTLM 和 Kerberos 身份验证所需的凭据哈希。 通过以下步骤可将所需的凭据哈希同步到 Azure AD 租户。

### <a name="install-or-update-azure-ad-connect"></a>安装或更新 Azure AD Connect
在加入域的计算机上安装 Azure AD Connect 的最新建议的版本。 如果有 Azure AD Connect 安装的现有实例，需要更新该实例以使用最新版本的 Azure AD Connect。 若要避免可能已修复的已知的问题/bug，请确保始终使用最新版本的 Azure AD Connect。

**[下载 Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

推荐的版本： **1.1.281.0** — 于 2016 年 9 月 7 日发布

> [!WARNING]
> 必须安装推荐的最新的 Azure AD Connect 版本，才能将旧密码凭据（NTLM 和 Kerberos 身份验证需要的）同步到 Azure AD 租户。 此功能不可用于之前的 Azure AD Connect 版本或旧版的 DirSync 工具。
>
>

有关 Azure AD connect 的安装说明，请参阅以下文章 — [Azure AD Connect 入门](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>启用 Azure AD 的 NTLM 和 Kerberos 凭据哈希的同步
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

## <a name="related-content"></a>相关内容
* [对仅限云的 Azure AD 目录启用 AAD 域服务的密码同步](active-directory-ds-getting-started-password-sync.md)
* [管理受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-administer-domain.md)
* [将 Windows 虚拟机加入到受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-join-windows-vm.md)
* [将 Red Hat Enterprise Linux 虚拟机加入到受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

