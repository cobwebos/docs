---
title: Azure AD 域服务的组托管服务帐户 |Microsoft Docs
description: 了解如何创建与 Azure Active Directory 域服务托管域一起使用的组托管服务帐户（gMSA）
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: a943d2a8453cb727e9d01e35b12ca90d939ee5e8
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546309"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>在 Azure AD 域服务中创建组托管服务帐户（gMSA）

应用程序和服务通常需要使用标识对其他资源进行身份验证。 例如，web 服务可能需要使用数据库服务进行身份验证。 如果某个应用程序或服务有多个实例（如 web 服务器场），则手动创建和配置这些资源的标识会耗费时间。

相反，可以在 Azure Active Directory 域服务（Azure AD DS）托管域中创建组托管服务帐户（gMSA）。 Windows OS 自动管理 gMSA 的凭据，这简化了大量资源组的管理。

本文说明如何使用 Azure PowerShell 在 Azure AD DS 托管域中创建 gMSA。

## <a name="before-you-begin"></a>开始之前

若要完成本文，需要具备以下资源和权限：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建和配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]的教程。
* 已加入到 Azure AD DS 托管域的 Windows Server 管理 VM。
    * 如果需要，请完成[创建管理 VM][tutorial-create-management-vm]教程。

## <a name="managed-service-accounts-overview"></a>托管服务帐户概述

独立托管服务帐户（sMSA）是一个自动管理其密码的域帐户。 此方法简化了服务主体名称（SPN）管理，并为其他管理员启用委托管理。 无需手动创建和轮换帐户的凭据。

组托管服务帐户（gMSA）提供相同的管理简化，但适用于域中的多个服务器。 GMSA 允许服务器场中托管的服务的所有实例使用相同的服务主体来处理相互身份验证协议。 将 gMSA 用作服务主体时，Windows 操作系统将再次管理帐户的密码，而不是依赖于管理员。

有关详细信息，请参阅[组托管服务帐户（gMSA）概述][gmsa-overview]。

## <a name="using-service-accounts-in-azure-ad-ds"></a>使用 Azure AD DS 中的服务帐户

由于 Azure AD DS 托管域被 Microsoft 锁定并由 Microsoft 管理，因此，在使用服务帐户时，需要注意以下事项：

* 在托管域上的自定义组织单位（OU）中创建服务帐户。
    * 无法在内置*AADDC Users*或*AADDC 计算机*ou 中创建服务帐户。
    * 请改为在 Azure AD DS 托管域中[创建自定义 ou][create-custom-ou] ，然后在该自定义 OU 中创建服务帐户。
* 已预先创建密钥分发服务（KDS）根密钥。
    * KDS 根密钥用于生成和检索 Gmsa 的密码。 在 Azure AD DS 中，将为你创建 KDS 根。
    * 你没有权限创建另一个，或查看默认的 KDS 根密钥。

## <a name="create-a-gmsa"></a>创建 gMSA

首先，使用[ADOrganizationalUnit][New-AdOrganizationalUnit] cmdlet 创建自定义 OU。 有关创建和管理自定义 Ou 的详细信息，请参阅[AZURE AD DS 中的自定义 ou][create-custom-ou]。

> [!TIP]
> 若要完成这些步骤以创建 gMSA，请[使用管理 VM][tutorial-create-management-vm]。 此管理 VM 应已具有所需的 AD PowerShell cmdlet 并连接到托管域。

以下示例在名为*contoso.com*的 Azure AD DS 托管域中创建名为*myNewOU*的自定义 OU。 使用你自己的 OU 和托管域名：

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=contoso,DC=COM"
```

现在使用[uninstall-adserviceaccount][New-ADServiceAccount] Cmdlet 创建 gMSA。 定义以下示例参数：

* **-Name**设置为*WebFarmSvc*
* **-Path**参数指定上一步中创建的 gMSA 的自定义 OU。
* 为*WebFarmSvc.contoso.com*设置 DNS 条目和服务主体名称
* *CONTOSO-SERVER $* 中的主体允许检索使用标识的密码。

指定您自己的名称和域名。

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.contoso.com `
    -Path "OU=MYNEWOU,DC=contoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.contoso.com/contoso.com, `
        http/WebFarmSvc.contoso.com/contoso, `
        http/WebFarmSvc/contoso.com, `
        http/WebFarmSvc/contoso `
    -PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

现在，可以根据需要将应用程序和服务配置为使用 gMSA。

## <a name="next-steps"></a>后续步骤

有关 Gmsa 的详细信息，请参阅[组托管服务帐户][gmsa-start]入门。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
