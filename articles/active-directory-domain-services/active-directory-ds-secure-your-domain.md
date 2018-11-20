---
title: 保护 Azure Active Directory 域服务托管域 | Microsoft Docs
description: 保护托管域
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: 1dea3def00423ecf092562aa47c76258316194a9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50159054"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>保护 Azure AD 域服务托管域
本文可帮助你保护托管域。 你可以禁用弱密码套件和 NTLM 凭据哈希同步。

## <a name="install-the-required-powershell-modules"></a>安装所需的 PowerShell 模块

### <a name="install-and-configure-azure-ad-powershell"></a>安装和配置 Azure AD PowerShell
请按照本文中的说明[安装 Azure AD PowerShell 模块并连接到 Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。

### <a name="install-and-configure-azure-powershell"></a>安装和配置 Azure PowerShell
请按照文章中的说明[安装 Azure PowerShell 模块并连接到 Azure 订阅](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>禁用弱密码套件和 NTLM 凭据哈希同步
使用以下 PowerShell 脚本可以：
1. 禁用托管域上的 NTLM v1 支持。
2. 禁止从本地 AD 进行 NTLM 密码哈希同步。
3. 禁用托管域上的 TLS v1。

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>后续步骤
* [了解 Azure AD 域服务中的同步](active-directory-ds-synchronization.md)
