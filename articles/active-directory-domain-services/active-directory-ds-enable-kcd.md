---
title: Azure Active Directory 域服务：启用 kerberos 约束委派 | Microsoft 文档
description: 在 Azure Active Directory 域服务托管域上启用 kerberos 约束委派
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 4c6e25972b47edf67dac8557e1925bb44463f4d6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331023"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>在托管域上配置 Kerberos 约束委派 (KCD)
许多应用程序需要在用户的上下文中访问资源。 Active Directory 支持实现此用例的称为“Kerberos 委派”的机制。 而且，可以限制委派，以便只能在用户的上下文中访问特定资源。 Azure AD 域服务托管域不同于传统的 Active Directory 域，因为它们更安全地锁定。

本文说明如何在 Azure AD 域服务托管域上配置 kerberos 约束委派。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos 约束委派 (KCD)
Kerberos 委派使一个帐户可以模拟另一个安全主体（例如用户）访问资源。 请考虑在用户的上下文中访问后端 Web API 的 Web 应用程序。 在此示例中，Web 应用程序（在服务帐户或计算机/虚拟机帐户的上下文中运行）在访问资源（后端 Web API）时会模拟用户。 Kerberos 委派是不安全的，因为它不限制模拟帐户在用户的上下文中可以访问的资源。

Kerberos 约束委派 (KCD) 限制指定的服务器可代表用户操作的服务/资源。 传统的 KCD 需要域管理员权限，才能配置服务的域帐户，并将该帐户限制到单个域。

传统的 KCD 也有与之关联的一些问题。 在早期操作系统中，如果域管理员为服务配置了基于帐户的 KCD，服务管理员没有有效途径来了解哪些前端服务委派给了其拥有的资源服务。 并且可委派给资源服务的任何前端服务都代表了一个潜在的攻击点。 如果托管前端服务的服务器受到安全威胁，并且它已配置为委派给资源服务，则资源服务也会受到安全威胁。

> [!NOTE]
> 在 Azure AD 域服务托管域上，没有域管理员权限。 因此，无法在托管域上配置传统基于帐户的 KCD。 请使用基于资源的 KCD，如本文中所述。 该机制也更安全。
>
>

## <a name="resource-based-kcd"></a>基于资源的 KCD
从 Windows Server 2012 开始，服务管理员获得了为其服务配置约束委派的能力。 在此模型中，后端服务管理员可以允许或拒绝特定前端服务使用 KCD。 此模型称为“基于资源的 KCD”。

可使用 PowerShell 配置基于资源的 KCD。 可以使用 `Set-ADComputer` 或 `Set-ADUser` cmdlet，具体取决于所模拟的帐户是计算机帐户还是用户帐户/服务帐户。

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>在托管域上为计算机帐户配置基于资源的 KCD
假定有一个在计算机“contoso100-webapp.contoso100.com”上运行的 Web 应用。 它需要在域用户的上下文中访问资源（在“contoso100-api.contoso100.com”上运行的 Web API）。 下面是为此方案设置基于资源的 KCD 的方法：

1. [创建自定义 OU](active-directory-ds-admin-guide-create-ou.md)。 可以将管理此自定义 OU 的权限委托给托管域中的用户。
2. 将两台虚拟机（即运行 Web 应用的虚拟机和运行 Web API 的虚拟机）都加入到托管域。 在自定义 OU 中创建这些计算机帐户。
3. 现在，使用以下 PowerShell 命令创建基于资源的 KCD：

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Web 应用和 Web API 的计算机帐户需要在有权配置基于资源的 KCD 的自定义 OU 中。 无法在内置 AAD DC 计算机的容器中为计算机帐户配置基于资源的 KCD。
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>在托管域上为用户帐户配置基于资源的 KCD
假定有一个以服务帐户“appsvc”的形式运行的 Web 应用，它需要在域用户的上下文中访问资源（以服务帐户“backendsvc”的形式运行的 Web API）。 下面是为此方案设置基于资源的 KCD 的方法。

1. [创建自定义 OU](active-directory-ds-admin-guide-create-ou.md)。 可以将管理此自定义 OU 的权限委托给托管域中的用户。
2. 将运行后端 Web API/资源的虚拟机加入到托管域。 在自定义 OU 中创建计算机帐户。
3. 在自定义 OU 中创建用于运行 Web 应用的服务帐户（例如，“appsvc”）。
4. 现在，使用以下 PowerShell 命令创建基于资源的 KCD：

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> 后端 Web API 和服务帐户的计算机帐户都需要在有权配置基于资源的 KCD 的自定义 OU 中。 无法在内置 AAD DC 计算机的容器或在内置 AAD DC 用户的容器中，为计算机帐户配置基于资源的 KCD。 因此，无法使用从 Azure AD 同步的用户帐户来设置基于资源的 KCD。
>

## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [Kerberos 约束委派概述](https://technet.microsoft.com/library/jj553400.aspx)
