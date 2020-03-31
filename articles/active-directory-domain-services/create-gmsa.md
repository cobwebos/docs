---
title: Azure AD 域服务的托管服务帐户组 |微软文档
description: 了解如何创建组托管服务帐户 （gMSA） 以与 Azure 活动目录域服务托管域一起使用
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
ms.openlocfilehash: 58749e4518f6fa73c8641ce38483c101576047aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614074"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>在 Azure AD 域服务中创建组托管服务帐户 （gMSA）

应用程序和服务通常需要一个标识来与其他资源进行身份验证。 例如，Web 服务可能需要使用数据库服务进行身份验证。 如果应用程序或服务有多个实例（如 Web 服务器场），则手动创建和配置这些资源的标识会非常耗时。

相反，可以在 Azure 活动目录域服务 （Azure AD DS） 托管域中创建组托管服务帐户 （gMSA）。 Windows 操作系统自动管理 gMSA 的凭据，从而简化了大型资源组的管理。

本文介绍如何使用 Azure PowerShell 在 Azure AD DS 托管域中创建 gMSA。

## <a name="before-you-begin"></a>开始之前

要完成本文，您需要以下资源和特权：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成创建[和配置 Azure 活动目录域服务实例][create-azure-ad-ds-instance]的教程。
* 加入到 Azure AD DS 托管域的 Windows 服务器管理 VM。
    * 如果需要，请完成教程以创建[管理 VM][tutorial-create-management-vm]。

## <a name="managed-service-accounts-overview"></a>托管服务帐户概述

独立托管服务帐户 （sMSA） 是自动管理其密码的域帐户。 此方法简化了服务主体名称 （SPN） 管理，并启用委派管理给其他管理员。 您无需手动创建和轮换帐户的凭据。

组托管服务帐户 （gMSA） 提供相同的管理简化，但为域中的多个服务器提供相同的管理简化。 gMSA 允许托管在服务器场上的服务的所有实例使用相同的服务主体，以便相互身份验证协议正常工作。 当 gMSA 用作服务主体时，Windows 操作系统将再次管理帐户的密码，而不是依赖管理员。

有关详细信息，请参阅[组托管服务帐户 （gMSA） 概述][gmsa-overview]。

## <a name="using-service-accounts-in-azure-ad-ds"></a>在 Azure AD DS 中使用服务帐户

由于 Azure AD DS 托管域由 Microsoft 锁定和管理，因此在使用服务帐户时需要考虑：

* 在托管域上的自定义组织单位 （OU） 中创建服务帐户。
    * 不能在内置*的 AADDC 用户*或*AADDC 计算机*O 中创建服务帐户。
    * 而是在 Azure AD DS 托管域中[创建自定义 OU，][create-custom-ou]然后在该自定义 OU 中创建服务帐户。
* 密钥分发服务 （KDS） 根键是预先创建的。
    * KDS 根键用于生成和检索 gMSA 的密码。 在 Azure AD DS 中，将为您创建 KDS 根。
    * 您没有创建另一个或查看默认 KDS 根键的权限。

## <a name="create-a-gmsa"></a>创建 gMSA

首先，使用[新 AD 组织单位][New-AdOrganizationalUnit]cmdlet 创建自定义 OU。 有关创建和管理自定义 O 的详细信息，请参阅[Azure AD DS 中的自定义 O。][create-custom-ou]

> [!TIP]
> 要完成这些步骤以创建 gMSA，[请使用管理 VM][tutorial-create-management-vm]。 此管理 VM 应该已经具有所需的 AD PowerShell cmdlet 和与托管域的连接。

下面的示例在名为*aaddscontoso.com*的 Azure AD DS 托管域中创建名为*myNewOU*的自定义 OU。 使用您自己的 OU 和托管域名：

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

现在使用[新 ADService 帐户][New-ADServiceAccount]cmdlet 创建 gMSA。 定义了以下示例参数：

* **-名称**设置为*WebFarmSvc*
* **-路径**参数为上一步中创建的 gMSA 指定自定义 OU。
* DNS 条目和服务主体名称设置为*WebFarmSvc.aaddscontoso.com*
* 允许*AADDSCONTOSO-SERVER$* 中的主体使用标识检索密码。

指定您自己的名称和域名。

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

应用程序和服务现在可以配置为根据需要使用 gMSA。

## <a name="next-steps"></a>后续步骤

有关 gMSA 的详细信息，请参阅[开始使用组托管服务帐户][gmsa-start]。

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
