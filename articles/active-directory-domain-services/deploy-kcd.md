---
title: Azure AD 域服务的 Kerberos 受限委派 |微软文档
description: 了解如何在 Azure 活动目录域服务托管域中启用基于资源的 Kerberos 约束委派 （KCD）。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 0d7c9319097d81f4db2e2c5ecfc692269d27a4db
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476094"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>在 Azure 活动目录域服务中配置 Kerberos 约束委派 （KCD）

运行应用程序时，可能需要这些应用程序在其他用户的上下文中访问资源。 活动目录域服务 （AD DS） 支持一种称为*Kerberos 委派*的机制，该机制启用此用例。 然后，Kerberos*约束*委派 （KCD） 在此基础上构建，以定义可在用户上下文中访问的特定资源。 Azure 活动目录域服务 （Azure AD DS） 托管域比传统的本地 AD DS 环境更安全地锁定，因此使用更安全的*基于资源的*KCD。

本文介绍如何在 Azure AD DS 托管域中配置基于资源的 Kerberos 约束委派。

## <a name="prerequisites"></a>先决条件

要完成本文，您需要以下资源：

* 一个有效的 Azure 订阅。
    * 如果没有 Azure 订阅，[请创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。
* 加入到 Azure AD DS 托管域的 Windows 服务器管理 VM。
    * 如果需要，请完成[创建 Windows 服务器 VM 的教程并将其加入托管域，][create-join-windows-vm]然后[安装 AD DS 管理工具][tutorial-create-management-vm]。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。**

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos 约束委派概述

Kerberos 委派允许一个帐户模拟另一个帐户以访问资源。 例如，访问后端 Web 组件的 Web 应用程序在建立后端连接时可以将自己模拟为不同的用户帐户。 Kerberos 委派不安全，因为它不限制模拟帐户可以访问的资源。

Kerberos 约束委派 （KCD） 限制指定服务器或应用程序在模拟其他标识时可以连接的服务或资源。 传统的 KCD 需要域管理员权限来配置服务的域帐户，并且它将帐户限制在单个域上运行。

传统的 KCD 也有一些问题。 例如，在早期的操作系统中，服务管理员没有有用的方法来知道委托给其拥有的资源服务的前端服务。 任何可以委派给资源服务的前端服务都可能是攻击点。 如果托管配置为委派为资源服务的前端服务的服务器受到威胁，则资源服务也可能受到威胁。

在 Azure AD DS 托管域中，您没有域管理员权限。 因此，无法在 Azure AD DS 托管域中配置基于帐户的传统 KCD。 可改为使用基于资源的 KCD，这也更安全。

### <a name="resource-based-kcd"></a>基于资源的 KCD

Windows Server 2012 及更高版本使服务管理员能够为其服务配置受约束的委派。 此模型称为“基于资源的 KCD”。 使用此方法，后端服务管理员可以允许或拒绝使用 KCD 的特定前端服务。

可使用 PowerShell 配置基于资源的 KCD。 您可以使用[Set-ADComputer][Set-ADComputer]或[Set-ADUser][Set-ADUser] cmdlet，具体取决于模拟帐户是计算机帐户还是用户帐户/服务帐户。

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>为计算机帐户配置基于资源的 KCD

在这种情况下，假设您有一个在名为*contoso-webapp.aaddscontoso.com*的计算机上运行的 Web 应用。 Web 应用需要访问在域用户上下文中名为*contoso-api.aaddscontoso.com*的计算机上运行的 Web API。 完成以下步骤以配置此方案：

1. [创建自定义 OU](create-ou.md)。 您可以将管理此自定义 OU 的权限委派给 Azure AD DS 托管域中的用户。
1. [域将虚拟机][create-join-windows-vm]（运行 Web 应用的虚拟机）和运行 Web API 的虚拟机连接到 Azure AD DS 托管域。 从上一步在自定义 OU 中创建这些计算机帐户。

    > [!NOTE]
    > Web 应用的计算机帐户和 Web API 必须位于自定义 OU 中，您可以在其中配置基于资源的 KCD 的权限。 无法为内置*AAD DC 计算机*容器中的计算机帐户配置基于资源的 KCD。

1. 最后，使用[Set-AD 计算机][Set-ADComputer]电源 Shell cmdlet 配置基于资源的 KCD。 从加入域的管理 VM 中作为*Azure AD DC 管理员*组的成员登录的用户帐户，运行以下 cmdlet。 根据需要提供您自己的计算机名称：
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>为用户帐户配置基于资源的 KCD

在这种情况下，假设您有一个 Web 应用，该应用作为名为*appvc*的服务帐户运行。 Web 应用需要访问在域用户的上下文中作为名为*backendsvc*的服务帐户运行的 Web API。 完成以下步骤以配置此方案：

1. [创建自定义 OU](create-ou.md)。 您可以将管理此自定义 OU 的权限委派给 Azure AD DS 托管域中的用户。
1. [域将][create-join-windows-vm]运行后端 Web API/资源的虚拟机加入 Azure AD DS 托管域。 在自定义 OU 中创建计算机帐户。
1. 在自定义 OU 中创建用于运行 Web 应用的服务帐户（例如，“appsvc”）。

    > [!NOTE]
    > 同样，Web API VM 的计算机帐户和 Web 应用的服务帐户必须位于自定义 OU 中，您有权配置基于资源的 KCD。 不能为内置*AAD DC 计算机*或*AAD DC 用户*容器中的帐户配置基于资源的 KCD。 这也意味着不能使用从 Azure AD 同步的用户帐户来设置基于资源的 KCD。 您必须创建和使用在 Azure AD DS 中专门创建的服务帐户。

1. 最后，使用[Set-ADUser][Set-ADUser] PowerShell cmdlet 配置基于资源的 KCD。 从加入域的管理 VM 中作为*Azure AD DC 管理员*组的成员登录的用户帐户，运行以下 cmdlet。 根据需要提供您自己的服务名称：

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>后续步骤

要了解有关委派在活动目录域服务中的工作方式的详细信息，请参阅[Kerberos 约束委派概述][kcd-technet]。

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
