---
title: Azure Active Directory 域服务：创建组托管服务帐户 | Microsoft Docs
description: 管理 Azure Active Directory 域服务托管域
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: maheshu
ms.openlocfilehash: 9bfd38b2eba3cab5012e5715ad283b9cb7b84a9b
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505886"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>在 Azure AD 域服务托管域上创建组托管服务帐户 (gMSA)
本文展示了如何在 Azure AD 域服务托管域上创建托管服务帐户。

## <a name="managed-service-accounts"></a>托管服务帐户
独立的托管服务帐户 (sMSA) 是一个托管域帐户，其密码是自动管理的。 它简化了服务主体名称 (SPN) 管理，并且可以委托其他管理员进行管理。 此类型的托管服务帐户 (MSA) 是在 Windows Server 2008 R2 和 Windows 7 中引入的。

组托管服务帐户 (gMSA) 为域中的许多服务器提供了相同的优势。 服务器场上承载的同一服务的所有实例都需要使用同一服务主体，相互身份验证协议才能工作。 当 gMSA 用作服务主体时，将由 Windows 操作系统管理帐户的密码，而不是依赖于管理员。

**详细信息：**
- [组托管服务帐户概述](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [组托管服务帐户入门](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>使用 Azure AD 域服务中的服务帐户
Azure AD 域服务托管域处于锁定状态，由 Microsoft 进行管理。 将服务帐户与 Azure AD 域服务一起使用时，有几个关键事项需要注意。

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>在托管域上的自定义组织单位 (OU) 中创建服务帐户
无法在内置的“AADDC 用户”或“AADDC 计算机”组织单位中创建服务帐户。 请在托管域上[创建自定义 OU](active-directory-ds-admin-guide-create-ou.md)，然后在该自定义 OU 中创建服务帐户。

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>密钥分发服务 (KDS) 根密钥已经预先创建
Azure AD 域服务托管域上已预先创建了密钥分发服务 (KDS) 根密钥。 你不需要创建 KDS 根密钥，也无权执行此操作。 你也无法查看托管域上的 KDS 根密钥。

## <a name="sample---create-a-gmsa-using-powershell"></a>示例 - 使用 PowerShell 创建 gMSA
下面的示例展示了如何使用 PowerShell 创建自定义 OU。 然后，你可以通过使用 ```-Path``` 参数指定 OU 在该 OU 中创建 gMSA。

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=CONTOSO100,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso100.com  `
-Path "OU=MYNEWOU,DC=CONTOSO100,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso100.com/contoso100.com, `
http/WebFarmSvc.contoso100.com/contoso100,  `
http/WebFarmSvc/contoso100.com, http/WebFarmSvc/contoso100  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**PowerShell cmdlet 文档：**
- [New-ADOrganizationalUnit cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [New-ADServiceAccount cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>后续步骤
- [在托管域上创建自定义 OU](active-directory-ds-admin-guide-create-ou.md)
- [组托管服务帐户概述](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [组托管服务帐户入门](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
