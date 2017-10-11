---
title: "Azure Active Directory 域服务：启用 kerberos 约束委派 | Microsoft 文档"
description: "在 Azure Active Directory 域服务托管域上启用 kerberos 约束委派"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: f36f16a7bb00ace9fd5164eb38ba77f015f22f5c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>在托管域上配置 Kerberos 约束委派 (KCD)
许多应用程序需要在用户的上下文中访问资源。 Active Directory 支持实现此用例的称为“Kerberos 委派”的机制。 而且，可以限制委派，以便只能在用户的上下文中访问特定资源。 Azure AD 域服务托管域不同于传统的 Active Directory 域，因为它们更安全地锁定。

本文说明如何在 Azure AD 域服务托管域上配置 kerberos 约束委派。

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos 约束委派 (KCD)
Kerberos 委派使一个帐户可以模拟另一个安全主体（例如用户）访问资源。 请考虑在用户的上下文中访问后端 Web API 的 Web 应用程序。 在此示例中，Web 应用程序（在服务帐户或计算机/虚拟机帐户的上下文中运行）在访问资源（后端 Web API）时会模拟用户。 Kerberos 委派是不安全的，因为它不限制模拟帐户在用户的上下文中可以访问的资源。

Kerberos 约束委派 (KCD) 限制指定的服务器可代表用户操作的服务/资源。 传统的 KCD 需要域管理员权限，才能配置服务的域帐户，并将该帐户限制到单个域。

传统的 KCD 也有与之关联的一些问题。 在域管理员配置了服务的早期操作系统中，服务管理员没有有效途径来了解哪些前端服务委派给了其拥有的资源服务。 并且可委派给资源服务的任何前端服务都代表了一个潜在的攻击点。 如果托管前端服务的服务器受到安全威胁，并且它已配置为委派给资源服务，则资源服务也会受到安全威胁。

> [!NOTE]
> 在 Azure AD 域服务托管域上，没有域管理员权限。 因此，**无法在托管域上配置传统 KCD**。 请使用基于资源的 KCD，如本文中所述。 该机制也更安全。
>
>

## <a name="resource-based-kerberos-constrained-delegation"></a>基于资源的 kerberos 约束委派
在 Windows Server 2012 R2 和 Windows Server 2012 中，配置服务的约束委派的能力已从域管理员转移到服务管理员。 这样，后端服务管理员可以允许或拒绝前端服务。 此模型称为**基于资源的 kerberos 约束委派**。

可使用 PowerShell 配置基于资源的 KCD。 可以使用 Set-ADComputer 或 Set-ADUser cmdlet，具体取决于所模拟的帐户是计算机帐户还是用户帐户/服务帐户。

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>在托管域上为计算机帐户配置基于资源的 KCD
假定你已在计算机上运行的 web 应用 contoso100-webapp.contoso100.com。 它需要在域用户的上下文中访问资源（在“contoso100-api.contoso100.com”上运行的 Web API）。 下面是为此方案设置基于资源的 KCD 的方法。

```
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>在托管域上为用户帐户配置基于资源的 KCD
假定你已作为服务帐户 appsvc 运行的 web 应用，它需要访问资源 (web API 作为服务帐户的 backendsvc 运行) 的域用户的上下文中。 下面是为此方案设置基于资源的 KCD 的方法。

```
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [Kerberos 约束委派概述](https://technet.microsoft.com/library/jj553400.aspx)
