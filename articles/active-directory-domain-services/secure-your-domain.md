---
title: 安全 Azure AD 域服务 |微软文档
description: 了解如何禁用 Azure 活动目录域服务托管域的弱密码、旧协议和 NTLM 密码哈希同步。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 8eee516beaaf26ed25bd20f9689d26fdb1eb9b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74546223"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>禁用弱密码和密码哈希同步，以保护 Azure AD 域服务托管域

默认情况下，Azure 活动目录域服务 （Azure AD DS） 允许使用密码，如 NTLM v1 和 TLS v1。 某些旧应用程序可能需要这些密码，但被视为弱，如果您不需要，则可以禁用这些密码。 如果使用 Azure AD Connect 进行本地混合连接，还可以禁用 NTLM 密码哈希的同步。

本文介绍如何禁用 NTLM v1 和 TLS v1 密码并禁用 NTLM 密码哈希同步。

## <a name="prerequisites"></a>先决条件

要完成本文，您需要以下资源：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。
* 安装和配置 Azure PowerShell。
    * 如果需要，请按照说明[安装 Azure PowerShell 模块并连接到 Azure 订阅](/powershell/azure/install-az-ps)。
    * 请确保使用[连接-AzAccount][Connect-AzAccount] cmdlet 登录到 Azure 订阅。
* 安装和配置 Azure AD 电源外壳。
    * 如果需要，请按照说明[安装 Azure AD PowerShell 模块并连接到 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 请确保使用[连接 AzureAD][Connect-AzureAD] cmdlet 登录到 Azure AD 租户。

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>禁用弱密码和 NTLM 密码哈希同步

要禁用弱密码套件和 NTLM 凭据哈希同步，请登录到 Azure 帐户，然后使用[Get-AzResource][Get-AzResource] cmdlet 获取 Azure AD DS 资源：

> [!TIP]
> 如果使用*Microsoft.AAD/Domain 服务*资源不存在[的 Get-AzResource][Get-AzResource]命令收到错误，[请提升您管理所有 Azure 订阅和管理组的访问][global-admin]。

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

接下来，定义*域安全设置*以配置以下安全选项：

1. 禁用 NTLM v1 支持。
2. 禁止从本地 AD 进行 NTLM 密码哈希同步。
3. 禁用 TLS v1。

> [!IMPORTANT]
> 如果在 Azure AD DS 托管域中禁用 NTLM 密码哈希同步，则用户和服务帐户无法执行 LDAP 简单绑定。 如果需要执行 LDAP 简单绑定，请不要在以下命令中设置 *"同步密码"="已禁用";* 安全配置选项。

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

最后，使用[Set-AzResource][Set-AzResource] cmdlet 将定义的安全设置应用于 Azure AD DS 托管域。 从第一步指定 Azure AD DS 资源，以及上一步的安全设置。

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

将安全设置应用于 Azure AD DS 托管域需要一些时间。

## <a name="next-steps"></a>后续步骤

要了解有关同步过程的更多详细信息，请参阅[如何在 Azure AD DS 托管域中同步对象和凭据][synchronization]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD