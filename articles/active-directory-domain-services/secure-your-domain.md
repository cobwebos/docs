---
title: 保护 Azure Active Directory 域服务托管域 | Microsoft Docs
description: 保护托管域
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 923ecae9dc649b8f5cdcfd447b78fdec0805927a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879152"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>保护 Azure AD 域服务托管域
本文可帮助你保护托管域。 你可以禁用弱密码套件和 NTLM 凭据哈希同步。

## <a name="install-the-required-powershell-modules"></a>安装所需的 PowerShell 模块

### <a name="install-and-configure-azure-ad-powershell"></a>安装和配置 Azure AD PowerShell
请按照本文中的说明[安装 Azure AD PowerShell 模块并连接到 Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。

### <a name="install-and-configure-azure-powershell"></a>安装和配置 Azure PowerShell
请按照文章中的说明[安装 Azure PowerShell 模块并连接到 Azure 订阅](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>禁用弱密码套件和 NTLM 凭据哈希同步
使用以下 PowerShell 脚本可以：

1. 禁用托管域上的 NTLM v1 支持。
2. 禁止从本地 AD 进行 NTLM 密码哈希同步。
3. 禁用托管域上的 TLS v1。

如果收到一条错误消息`Get-AzResource` , 指出该*DomainServices*资源不存在, 请[提升你的访问权限, 以管理所有 Azure 订阅和管理组](../role-based-access-control/elevate-access-global-admin.md)。

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

> [!IMPORTANT]
> 如果在 Azure AD 域服务实例上禁用了 NTLM 密码哈希同步, 则用户 (和服务帐户) 无法执行 LDAP 简单绑定。  有关禁用 NTLM 密码哈希同步的详细信息, 请阅读[保护 Azure AD 域服务托管域](secure-your-domain.md)。
>
>

## <a name="next-steps"></a>后续步骤
* [了解 Azure AD 域服务中的同步](synchronization.md)
