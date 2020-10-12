---
title: 安全 Azure AD 域服务 | Microsoft Docs
description: 了解如何为 Azure Active Directory 域服务托管域禁用弱密码、旧协议和 NTLM 密码哈希同步。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 50cf58f83115cfb8c84fe7b2a37b6664c2d9c567
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88116676"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>禁用弱密码和密码哈希同步以保护 Azure Active Directory 域服务托管域

默认情况下，Azure Active Directory 域服务 (Azure AD DS) 允许使用 NTLM v1 和 TLS v1 等密码。 某些旧版应用程序可能需要这些密码，但这些密码视为弱密码，如果不需要，可以将其禁用。 如果使用 Azure AD Connect 进行本地混合连接，则还可以禁用 NTLM 密码哈希的同步。

本文介绍如何禁用 NTLM v1 和 TLS v1 密码以及禁用 NTLM 密码哈希同步。

## <a name="prerequisites"></a>先决条件

若要完成本文，需准备好以下资源：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]。
* 安装和配置 Azure PowerShell。
    * 如果需要，请按照说明[安装 Azure PowerShell 模块并连接到 Azure 订阅](/powershell/azure/install-az-ps)。
    * 确保使用 [Connect-AzAccount][Connect-AzAccount] cmdlet 登录到 Azure 订阅。
* 安装并配置 Azure AD PowerShell。
    * 如果需要，请按照说明[安装 Azure AD PowerShell 模块并连接到 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 确保使用 [Connect-AzureAD][Connect-AzureAD] cmdlet 登录到 Azure AD 租户。

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>禁用弱密码和 NTLM 密码哈希同步

若要禁用弱密码套件和 NTLM 凭据哈希同步，请登录 Azure 帐户，然后使用 [Get-AzResource][Get-AzResource] cmdlet 获取 Azure AD DS 资源：

> [!TIP]
> 如果使用 [Get-AzResource][Get-AzResource] 命令时收到错误，指示 Microsoft.AAD/DomainServices 资源不存在，请[提升你的访问权限以便能够管理所有 Azure 订阅和管理组][global-admin]。

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

接下来，定义 DomainSecuritySettings 以配置以下安全性选项：

1. 禁用 NTLM v1 支持。
2. 禁止从本地 AD 进行 NTLM 密码哈希同步。
3. 禁用 TLS v1。

> [!IMPORTANT]
> 如果在 Azure AD DS 托管域中禁用了 NTLM 密码哈希同步，则用户和服务帐户将无法执行 LDAP 简单绑定。 如果需要执行 LDAP 简单绑定，请不要在下面的命令中设置 "SyncNtlmPasswords"="Disabled"; 安全配置选项。

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

最后，使用 [Set-AzResource][Set-AzResource] cmdlet 将定义的安全设置应用于托管域。 指定第一步中的 Azure AD DS 资源和上一步中的安全设置。

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

将安全设置应用到托管域需要一些时间。

> [!IMPORTANT]
> 禁用 NTLM 之后，在 Azure AD Connect 中执行完整的密码哈希同步，以删除托管域中的所有密码哈希。 如果禁用 NTLM 但不强制执行密码哈希同步，则仅在下次更改密码时才会删除用户帐户的 NTLM 密码哈希。 如果用户在使用 NTLM 作为身份验证方法的系统上缓存了凭据，则此行为可以允许用户继续登录。
>
> NTLM 密码哈希与 Kerberos 密码哈希不同后，将无法回退到 NTLM。 如果 VM 已连接到托管域控制器，则缓存凭据也不再有效。  

## <a name="next-steps"></a>后续步骤

若要了解有关同步过程的详细信息，请参阅[如何在托管域中同步对象和凭据][synchronization]。

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
