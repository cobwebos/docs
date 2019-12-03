---
title: Azure AD 域服务的 Kerberos 约束委派 |Microsoft Docs
description: 了解如何在 Azure Active Directory 域服务托管域中启用基于资源的 Kerberos 约束委派（KCD）。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 8860f2bea2877e7775db20be79181352d8cd55c8
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705282"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>在 Azure Active Directory 域服务中配置 Kerberos 约束委派（KCD）

当你运行应用程序时，这些应用程序可能需要在不同用户的上下文中访问资源。 Active Directory 域服务（AD DS）支持一种称为*Kerberos 委派*的机制，该机制可实现此用例。 然后，Kerberos*约束*委派（KCD）建立在此机制上，用于定义可在用户的上下文中访问的特定资源。 Azure Active Directory 域服务（Azure AD DS）托管域更安全地锁定了传统本地 AD DS 环境，因此请使用更安全的*基于资源*的 KCD。

本文介绍如何在 Azure AD DS 托管域中配置基于资源的 Kerberos 约束委派。

## <a name="prerequisites"></a>必备组件

若要完成本文，需要以下资源：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。
* 已加入到 Azure AD DS 托管域的 Windows Server 管理 VM。
    * 如果需要，请完成[创建 Windows SERVER VM 并将其加入托管域][create-join-windows-vm]的教程，然后[安装 AD DS 管理工具][tutorial-create-management-vm]。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos 约束委派概述

Kerberos 委派允许一个帐户模拟另一个帐户来访问资源。 例如，访问后端 web 组件的 web 应用程序可以在进行后端连接时将其自身模拟为其他用户帐户。 Kerberos 委托不安全，因为它不会限制模拟帐户可以访问的资源。

Kerberos 约束委派（KCD）限制指定的服务器或应用程序在模拟其他标识时可以连接的服务或资源。 传统的 KCD 要求域管理员权限来为服务配置域帐户，并将该帐户限制为在单个域上运行。

传统 KCD 也有一些问题。 例如，在早期版本的操作系统中，服务管理员没有有效的方法来了解哪些前端服务委派给了其拥有的资源服务。 可以委派给资源服务的任何前端服务都是潜在的攻击点。 如果承载配置为委派到资源服务的前端服务的服务器已泄露，则资源服务也会受到损害。

在 Azure AD DS 托管域中，你没有域管理员权限。 因此，不能在 Azure AD DS 中将基于帐户的传统的 KCD 配置为托管域。 可以改为使用基于资源的 KCD，这也更安全。

### <a name="resource-based-kcd"></a>基于资源的 KCD

Windows Server 2012 和更高版本使服务管理员能够为其服务配置约束委派。 此模型称为 "基于资源的 KCD"。 使用此方法，后端服务管理员可以允许或拒绝特定的前端服务使用 KCD。

可使用 PowerShell 配置基于资源的 KCD。 使用[get-adcomputer][Set-ADComputer]或[new-aduser][Set-ADUser] cmdlet，具体取决于模拟帐户是计算机帐户还是用户帐户/服务帐户。

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>为计算机帐户配置基于资源的 KCD

在此方案中，假设你有一个在名为*contoso-webapp.aadds.contoso.com*的计算机上运行的 web 应用。 Web 应用需要访问在域用户的上下文中名为*contoso-api.aadds.contoso.com*的计算机上运行的 web API。 完成以下步骤以配置此方案：

1. [创建自定义 OU](create-ou.md)。 你可以将管理此自定义 OU 的权限委派给 Azure AD DS 托管域中的用户。
1. 域-将运行 web 应用的虚拟机以及运行 web API 的[虚拟机加入][create-join-windows-vm]到 Azure AD DS 托管域。 在上一步的自定义 OU 中创建这些计算机帐户。

    > [!NOTE]
    > Web 应用和 web API 的计算机帐户必须位于您有权配置基于资源的 KCD 的自定义 OU 中。 无法在内置*AAD DC 计算机*容器中为计算机帐户配置基于资源的 KCD。

1. 最后，使用[Get-adcomputer][Set-ADComputer] PowerShell cmdlet 配置基于资源的 KCD。 在已加入域的管理 VM，并以作为*AZURE AD DC administrators*组成员的用户帐户登录，运行以下 cmdlet。 根据需要提供自己的计算机名称：
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aadds.contoso.com
    Set-ADComputer contoso-api.aadds.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>为用户帐户配置基于资源的 KCD

在此方案中，假设你有一个作为名为*appsvc*的服务帐户运行的 web 应用。 Web 应用需要访问在域用户的上下文中作为名为*形式*的服务帐户运行的 web API。 完成以下步骤以配置此方案：

1. [创建自定义 OU](create-ou.md)。 你可以将管理此自定义 OU 的权限委派给 Azure AD DS 托管域中的用户。
1. 域-将运行后端 web API/资源的[虚拟机加入][create-join-windows-vm]到 Azure AD DS 托管域。 在自定义 OU 中创建计算机帐户。
1. 在自定义 OU 中创建用于运行 Web 应用的服务帐户（例如，“appsvc”）。

    > [!NOTE]
    > 同样，web API VM 的计算机帐户和 web 应用的服务帐户必须位于您有权配置基于资源的 KCD 的自定义 OU 中。 无法在内置*AAD Dc 计算机*或*AAD dc 用户*容器中为帐户配置基于资源的 KCD。 这也意味着，不能使用从 Azure AD 同步的用户帐户来设置基于资源的 KCD。 必须创建并使用专门在 Azure AD DS 中创建的服务帐户。

1. 最后，使用[New-aduser][Set-ADUser] PowerShell cmdlet 配置基于资源的 KCD。 在已加入域的管理 VM，并以作为*AZURE AD DC administrators*组成员的用户帐户登录，运行以下 cmdlet。 根据需要提供自己的服务名称：

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>后续步骤

若要详细了解委托在 Active Directory 域服务中的工作原理，请参阅[Kerberos 约束委派概述][kcd-technet]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
