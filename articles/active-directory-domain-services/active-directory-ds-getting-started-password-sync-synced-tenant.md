---
title: Azure AD 域服务：启用密码同步 | Microsoft Docs
description: Azure Active Directory 域服务入门
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 0b38bdba0d88da9296106411737c280dcf6d5df1
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69613170"
---
# <a name="enable-password-hash-synchronization-to-azure-active-directory-domain-services"></a>允许将密码哈希同步到 Azure Active Directory 域服务

已同步的 Azure AD 租户设置为使用 Azure AD Connect 与组织的本地目录同步。 默认情况下，Azure AD Connect 不会将 NTLM 和 Kerberos 凭据哈希同步到 Azure AD。 要使用 Azure AD 域服务，需要将 Azure AD Connect 配置为同步 NTLM 和 Kerberos 身份验证所需的凭据哈希。 通过以下步骤可将所需的凭据哈希从本地目录同步到 Azure AD 租户。

> [!NOTE]
> **如果组织的用户帐户是从本地目录同步的，则必须启用 NTLM 和 Kerberos 哈希的同步，然后才能使用托管域。** 已同步用户帐户是指在本地目录中创建并使用 Azure AD Connect 同步到 Azure AD 租户中的帐户。 密码哈希同步不同于用户/对象同步过程。你需要禁用/启用密码哈希同步才能获取完整密码同步, 然后你将在应用程序事件日志中看到该批次密码哈希更新。

在本文中, 将启用 NT LAN Manager (NTLM) 和 Kerberos 身份验证所需的凭据哈希同步, 以便 Azure AD 域服务。 设置凭据同步以后，用户即可使用其公司凭据登录到托管域。

对于仅限云的用户帐户来说，所涉及的步骤不同于使用 Azure AD Connect 从本地目录同步的用户帐户。

| **用户帐户的类型** | **要执行的步骤** |
| --- | --- |
| **从本地目录同步的用户帐户** |**&#x2713;** [按此文中的说明操作](active-directory-ds-getting-started-password-sync-synced-tenant.md#install-or-update-azure-ad-connect) |
| **在 Azure AD 中创建的云用户帐户** |**&#x2713;** [将仅限云的用户帐户的密码同步到托管域](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) |

> [!TIP]
> **可能需要完成这两组步骤。**
> 如果 Azure AD 租户既有仅限云的用户，又有来自本地 AD 的用户，则需完成两组步骤。

## <a name="install-or-update-azure-ad-connect"></a>安装或更新 Azure AD Connect

在加入域的计算机上安装 Azure AD Connect 的最新建议的版本。 如果有 Azure AD Connect 安装的现有实例，需要更新该实例以使用最新版本的 Azure AD Connect。 若要避免可能已修复的已知问题/Bug，请始终使用最新版本的 Azure AD Connect。

**[下载 Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)**

建议的版本：**1.1.614.0** - 已在 2017 年 9 月 5 日发布。

> [!WARNING]
> 必须安装推荐的最新的 Azure AD Connect 版本，才能将旧密码凭据（NTLM 和 Kerberos 身份验证需要的）同步到 Azure AD 租户。 此功能不可用于之前的 Azure AD Connect 版本或旧版的 DirSync 工具。

有关 Azure AD connect 的安装说明，请参阅以下文章 — [Azure AD Connect 入门](../active-directory/hybrid/whatis-hybrid-identity.md)

## <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>启用 Azure AD 的 NTLM 和 Kerberos 凭据哈希的同步

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

## <a name="next-steps"></a>后续步骤

> [!NOTE]
> **密码哈希同步**不同于用户/对象同步过程。你需要禁用/启用密码哈希同步才能获取完整密码同步, 然后你将在应用程序事件日志中看到该批次密码哈希更新。

* [对仅限云的 Azure AD 目录启用 AAD 域服务的密码同步](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)
* [管理 Azure AD 域服务域](tutorial-create-management-vm.md)
* [将 Windows 虚拟机加入到受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-join-windows-vm.md)
* [将 Red Hat Enterprise Linux 虚拟机加入到受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
