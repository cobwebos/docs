---
title: "Azure AD Privileged Identity Management 安全向导"
description: "首次使用 Azure Active Directory Privileged Identity Management 扩展时，会看到一个安全向导。 本文介绍如何使用该向导。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: a53a3719-8cc7-4fc7-8164-aafca192871b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 03adc1873e8187a9104f42ba9b50df2ac0d015b5


---
# <a name="the-azure-ad-privileged-identity-management-security-wizard"></a>Azure AD Privileged Identity Management 安全向导
如果你是第一个在组织中运行 Azure Privileged Identity Management (PIM) 的人，就会看到一个向导。 该向导可帮助你了解特权标识的安全风险，以及如何使用 PIM 来降低这些风险。 如果希望以后再对现有的角色分配进行任何更改，则暂时不需要在向导中进行这些更改。

## <a name="what-to-expect"></a>期望
在组织开始使用 PIM 之前，所有角色分配都是永久性的：即使用户当前无需其权限，但他们一直保留这些角色。  该向导的第一个步骤显示高特权角色的列表，以及当前有多少用户充当这些角色。 如果不熟悉其中的一个或多个用户，可以深入到某个特定角色来详细了解相应的用户。

该向导的第二个步骤用于更改管理员的角色分配。  

> [!WARNING]
> 必须至少有一个全局管理员，并且要有多个具有组织帐户（而不是 Microsoft 帐户）的特权角色管理员。 如果只有一个特权角色管理员，一旦该帐户遭到删除，组织就无法管理 PIM。
> 此外，如果用户具有 Microsoft 帐户（用于登录 Skype 和 Outlook.com 等 Microsoft 服务），请将角色分配设置为永久。 如果打算要求激活该角色之前执行 MFA，该用户将被锁定。
> 
> 

进行更改之后，将不再次显示该向导。 下次当你或其他特权角色管理员使用 PIM 时，将看到 PIM 仪表板。  

* 如果想要在角色中添加或删除用户，或将角色分配从永久更改为符合资格，请参阅[如何添加或删除用户的角色](active-directory-privileged-identity-management-how-to-add-role-to-user.md)了解详细信息。
* 如果想要向更多用户授予 PIM 管理访问权限，请参阅[如何授予 PIM 的管理访问权限](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]




<!--HONumber=Nov16_HO3-->


