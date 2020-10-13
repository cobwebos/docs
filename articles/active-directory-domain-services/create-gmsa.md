---
title: Azure AD 域服务的组托管服务帐户 | Microsoft Docs
description: 了解如何创建要用于 Azure Active Directory 域服务托管域的组托管服务帐户 (gMSA)
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 723b8cf7a71e5af672c8665040645d55645fe9f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87281848"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-active-directory-domain-services"></a>在 Azure Active Directory 域服务中创建组托管服务帐户 (gMSA)

应用程序和服务通常需要标识来向其他资源进行身份验证。 例如，Web 服务可能需要向数据库服务进行身份验证。 如果一个应用程序或服务有多个实例（如 web 服务器场），则手动创建和配置这些资源的标识将非常耗时。

在这种情况下，可以在 Azure Active Directory 域服务 (Azure AD DS) 托管域中创建组托管服务帐户 (gMSA)。 Windows OS 自动管理 gMSA 的凭据，这简化了对大型资源组的管理。

本文介绍了如何使用 Azure PowerShell 在托管域中创建 gMSA。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]的教程。
* 已加入 Azure AD DS 托管域的 Windows Server 管理 VM。
    * 如果需要，请完成[创建管理 VM][tutorial-create-management-vm] 的教程。

## <a name="managed-service-accounts-overview"></a>托管服务帐户概述

独立的托管服务帐户 (sMSA) 是一个域帐户，其密码是自动管理的。 此方法简化了服务主体名称 (SPN) 管理，并且可以委托其他管理员进行管理。 无需手动创建和轮换帐户的凭据。

组托管服务帐户 (gMSA) 提供同样的管理简化效果，但适用于域中的多个服务器。 gMSA 允许服务器场上承载的同一服务的所有实例都使用同一服务主体，相互身份验证协议才能工作。 当 gMSA 用作服务主体时，将再次由 Windows 操作系统管理帐户的密码，而不是依赖于管理员。

有关详细信息，请参阅[组托管服务帐户 (gMSA) 概述][gmsa-overview]。

## <a name="using-service-accounts-in-azure-ad-ds"></a>使用 Azure AD DS 中的服务帐户

由于托管域由 Microsoft 锁定和管理，因此在使用服务帐户时有一些注意事项：

* 在托管域上的自定义组织单位 (OU) 中创建服务帐户。
    * 无法在内置的“AADDC 用户”或“AADDC 计算机”OU 中创建服务帐户 。
    * 在这种情况下，请在托管域上[创建自定义 OU][create-custom-ou]，然后在该自定义 OU 中创建服务帐户。
* 密钥分发服务 (KDS) 根密钥已预先创建。
    * KDS 根密钥用于生成和检索 gMSA 的密码。 在 Azure AD DS 中，服务会为你创建 KDS 根。
    * 你没有相应权限可创建另一个 KDS 根密钥或查看默认的 KDS 根密钥。

## <a name="create-a-gmsa"></a>创建 gMSA

首先，使用 [New-ADOrganizationalUnit][New-AdOrganizationalUnit] cmdlet 创建一个自定义 OU。 有关创建和管理自定义 OU 的详细信息，请参阅 [Azure AD DS 中的自定义 OU][create-custom-ou]。

> [!TIP]
> 若要完成这些步骤来创建 gMSA，请[使用管理 VM][tutorial-create-management-vm]。 此管理 VM 应已具有所需的 AD PowerShell cmdlet 和与托管域的连接。

以下示例在名为 aaddscontoso.com 的托管域中创建名为 myNewOU 的自定义 OU 。 使用你自己的 OU 和托管域名：

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

现在，使用 [New-ADServiceAccount][New-ADServiceAccount] cmdlet 创建 gMSA。 定义了以下示例参数：

* “-Name”设置为 WebFarmSvc
* “-Path”参数为上一步中创建的 gMSA 指定自定义 OU。
* 为 WebFarmSvc.aaddscontoso.com 设置 DNS 条目和服务主体名称
* AADDSCONTOSO-SERVER$ 中的主体可以检索密码并使用标识。

指定你自己的名称和域名。

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

现在可以根据需要将应用程序和服务配置为使用 gMSA。

## <a name="next-steps"></a>后续步骤

有关 gMSA 的详细信息，请参阅[组托管服务帐户入门][gmsa-start]。

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
