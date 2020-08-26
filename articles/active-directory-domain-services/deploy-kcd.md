---
title: Azure AD 域服务的 Kerberos 约束委派 | Microsoft Docs
description: 了解如何在 Azure Active Directory 域服务托管域中启用基于资源的 Kerberos 约束委派 (KCD)。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 7f1640f74c3fca27e1d992f3e2cc538f6e2c7fef
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722987"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>配置 Azure Active Directory 域服务中的 Kerberos 约束委派 (KCD)

当你运行应用程序时，这些应用程序可能需要在不同用户的上下文中访问资源。 Active Directory 域服务 (AD DS) 支持可实现此用例的称为“Kerberos 委派”的机制。 然后，Kerberos 约束委派 (KCD) 将基于此机制进行构建，以定义可以在此用户的上下文中访问的特定资源。

与传统的本地 AD DS 环境相比，Azure Active Directory 域服务 (Azure AD DS) 托管域可以更安全地进行锁定，因此，请使用更安全的基于资源的 KCD。

本文介绍了如何在 Azure AD DS 托管域上配置基于资源的 Kerberos 约束委派。

## <a name="prerequisites"></a>先决条件

若要完成本文，需准备好以下资源：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]。
* 已加入 Azure AD DS 托管域的 Windows Server 管理 VM。
    * 如果需要，请完成[创建 Windows Server VM 并将其加入到托管域][create-join-windows-vm]的教程，然后[安装 AD DS 管理工具][tutorial-create-management-vm]。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos 约束委派概述

Kerberos 委派允许一个帐户模拟另一个帐户来访问资源。 例如，访问后端 Web 组件的 Web 应用程序可以在建立后端连接时将其自身模拟为其他用户帐户。 Kerberos 委派不安全，因为它不会对模拟帐户可以访问哪些资源进行限制。

Kerberos 约束委派 (KCD) 限制指定的服务器或应用程序在模拟其他标识时可以连接的服务或资源。 传统 KCD 需要域管理员权限才能配置服务的域帐户，并且会将该帐户限制为在单个域上运行。

传统 KCD 也有一些问题。 例如，在早期操作系统中，服务管理员没有有效途径来了解哪些前端服务委派给了其拥有的资源服务。 可委派给资源服务的任何前端服务都是一个潜在的攻击点。 如果托管着前端服务的服务器受到安全威胁，并且前端服务配置为委派给资源服务，则资源服务也会受到安全威胁。

在托管域中，你没有域管理员权限。 因此，无法在托管域中配置传统的基于帐户的 KCD。 可以改为使用基于资源的 KCD，这也更安全。

### <a name="resource-based-kcd"></a>基于资源的 KCD

在 Windows Server 2012 及更高版本中，服务管理员能够为其服务配置约束委派。 此模型称为基于资源的 KCD。 使用此方法，后端服务管理员可以允许或拒绝特定前端服务使用 KCD。

可使用 PowerShell 配置基于资源的 KCD。 可以使用 [Set-ADComputer][Set-ADComputer] 或 [Set-ADUser][Set-ADUser] cmdlet，具体取决于模拟帐户是计算机帐户还是用户帐户/服务帐户。

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>为计算机帐户配置基于资源的 KCD

在此方案中，假设你有一个在名为 contoso-webapp.aaddscontoso.com 的计算机上运行的 Web 应用。

此 Web 应用需要在域用户的上下文中访问在名为 contoso-api.aaddscontoso.com 的计算机上运行的 Web API。

请完成以下步骤来配置此方案：

1. [创建自定义 OU](create-ou.md)。 可以将管理此自定义 OU 的权限委托给托管域中的用户。
1. 将运行 Web 应用的虚拟机和运行 Web API 的虚拟机[加入到托管域][create-join-windows-vm]。 在上一步的自定义 OU 中创建这些计算机帐户。

    > [!NOTE]
    > Web 应用和 Web API 的计算机帐户必须位于你有权配置基于资源的 KCD 的自定义 OU 中。 无法为内置“AAD DC 计算机”容器中的计算机帐户配置基于资源的 KCD。

1. 最后，使用 [Set-ADComputer][Set-ADComputer] PowerShell cmdlet 配置基于资源的 KCD。

    在已加入域的管理 VM 中，使用属于“Azure AD DC 管理员”组成员的用户帐户登录，运行以下 cmdlet。 根据需要提供你自己的计算机名称：
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>为用户帐户配置基于资源的 KCD

在此方案中，假设你有一个作为名为 appsvc 的服务帐户运行的 Web 应用。 此 Web 应用需要访问一个 Web API，后者在域用户的上下文中作为名为 backendsvc 的服务帐户运行。 请完成以下步骤来配置此方案：

1. [创建自定义 OU](create-ou.md)。 可以将管理此自定义 OU 的权限委托给托管域中的用户。
1. 将运行后端 Web API/资源的虚拟机[加入到托管域][create-join-windows-vm]。 在自定义 OU 中创建计算机帐户。
1. 在自定义 OU 中创建用于运行 Web 应用的服务帐户（如“appsvc”）。

    > [!NOTE]
    > 同样，Web API VM 的计算机帐户和 Web 应用的服务帐户都必须位于你有权配置基于资源的 KCD 的自定义 OU 中。 你无法为内置“AAD DC 计算机”容器或“AAD DC 用户”容器中的帐户配置基于资源的 KCD。  这也意味着你无法使用从 Azure AD 同步的用户帐户来设置基于资源的 KCD。 你必须创建并使用专门在 Azure AD DS 中创建的服务帐户。

1. 最后，使用 [Set-ADUser][Set-ADUser] PowerShell cmdlet 配置基于资源的 KCD。

    在已加入域的管理 VM 中，使用属于“Azure AD DC 管理员”组成员的用户帐户登录，运行以下 cmdlet。 根据需要提供你自己的服务名称：

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>后续步骤

若要详细了解委派在 Active Directory 域服务中的工作原理，请参阅 [Kerberos 约束委派概述][kcd-technet]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)