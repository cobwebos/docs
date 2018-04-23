---
title: Azure AD Privileged Identity Management 中的角色 | Microsoft 文档
description: 了解可以在 Azure Privileged Identity Management 扩展中针对特权标识使用哪些角色。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ac812ccc-cf4e-4ac2-b981-69598056c9ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/04/2018
ms.author: billmath
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 94a066c43d1b04161a46dac15e5b373d97cc063d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="different-administrative-role-in-azure-active-directory-pim"></a>Azure Active Directory PIM 中的不同管理角色
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

可以将组织中的用户分配到 Azure AD 中的不同管理角色。 这些角色分配控制用户可以针对 Azure AD、Office 365 和其他 Microsoft Online Services 与连接的应用程序执行哪些任务，例如添加或删除用户或更改服务设置。  

> [!IMPORTANT]
> Microsoft 推荐使用 Azure 门户的 [Azure AD 管理中心](https://aad.portal.azure.com)管理 Azure AD。

全局管理员可以使用 PowerShell cmdlet（例如 `Add-MsolRoleMember` 和 `Remove-MsolRoleMember`）或者根据[在 Azure Active Directory 中分配管理员角色](active-directory-assign-admin-roles-azure-portal.md)中所述，通过门户更新要将哪些用户“永久”分配到 Azure AD 中的角色。

Azure AD Privileged Identity Management (PIM) 可以管理针对 Azure AD 中具有特权的用户的策略。 PIM 将用户分配到 Azure AD 中的一个或多个角色，可以分配某位用户永久充当该角色，或者使其符合该角色的资格。 将用户永久分配到某个角色或者激活合格角色分配时，他们可以使用分配到角色的权限来管理 Azure Active Directory、Office 365 和其他应用程序。

用户无论具有永久角色分配还是合格角色分配，获得的访问权限并无差异。 唯一的差异在于，有些用户并不是一直需要该访问权限。 他们符合该角色的资格，随时可以根据需要启用或禁用该角色。

## <a name="roles-managed-in-pim"></a>在 PIM 中管理的角色
使用 Privileged Identity Management 可将用户分配到常见的管理员角色，包括：

* **全局管理员**（也称为公司管理员）：有权访问所有管理功能。 组织中可以有多个全局管理员。 注册购买 Office 365 的人员自动成为全局管理员。
* **特权角色管理员**：可以管理 Azure AD PIM，以及更新其他用户的角色分配。  
* **计费管理员**：进行采购、管理订阅、管理支持票证和监视服务运行状况。
* **密码管理员**：重置密码、管理服务请求和监视服务运行状况。 密码管理员只能重置用户的密码。
* **服务管理员**：管理服务请求并监视服务运行状况。
  
  > [!NOTE]
  > 如果使用 Office 365，请在向用户分配服务管理员角色之前，先将服务（例如 Exchange Online）的管理权限分配到该用户。
  > 
  > 
* **用户管理管理员**：重置密码、监视服务运行状况，以及管理用户帐户、用户组和服务请求。 用户管理管理员无法删除全局管理员、创建其他管理员角色，或重置计费管理员、全局管理员和服务管理员的密码。
* **Exchange 管理员**：具有通过 Exchange 管理中心 (EAC) 访问 Exchange Online 的管理权限，可在 Exchange Online 中执行几乎所有的任务。
* **SharePoint 管理员（预览）**：具有通过 SharePoint Online 管理中心访问 SharePoint Online 的管理权限，可在 SharePoint Online 中执行几乎所有的任务。 此角色目前处于预览状态。 在 PIM 中激活后，符合条件的用户在 SharePoint 中使用此角色可能会发生延迟。
* **Skype for Business 管理员**：具有通过 Skype for Business 管理中心访问 Skype for Business 的管理权限，可在 Skype for Business Online 中执行几乎所有的任务。

有关详细信息，请参阅文章[在 Azure AD 中分配管理员角色](active-directory-assign-admin-roles-azure-portal.md)以及[在 Office 365 中分配管理员角色](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504)。

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


可以通过 PIM [将这些角色分配到用户](active-directory-privileged-identity-management-how-to-add-role-to-user.md)，使用户能够[根据需要激活角色](active-directory-privileged-identity-management-how-to-activate-role.md)。

如果要将访问权限授予另一位用户，使其可以在 PIM 本身中进行管理，请参阅 [how to give access to PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)（如何授予对 PIM 的访问权限），其中进一步说明了 PIM 要求该用户必须拥有哪些角色。

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>不在 PIM 中管理的角色
Exchange Online 或 SharePoint Online 中的角色（前面所述的角色除外）并不会出现在 Azure AD 中，因此也不会显示在 PIM 中。 有关在这些 Office 365 服务中更改精细角色分配的详细信息，请参阅 [Permissions in Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d)（Office 365 中的权限）。

Azure 订阅和资源组也不会出现在 Azure AD 中。 若要管理 Azure 订阅，请参阅 [如何添加或更改 Azure 管理员角色](../billing/billing-add-change-azure-subscription-administrator.md)；有关 Azure RBAC 的详细信息，请参阅 [Azure 基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)。

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>用户角色和登录
对于某些 Microsoft 服务和应用程序而言，将用户分配到角色可能还不足以让该用户成为管理员。

若要访问 Azure 门户，要求用户必须是 Azure 订阅的服务管理员或共同管理员，即使该用户无需管理 Azure 订阅，也是如此。  例如，若要管理 Azure AD 的配置设置，用户必须身兼 Azure AD 中的全局管理员和 Azure 订阅中的订阅共同管理员。  要了解如何将用户添加到 Azure 订阅，请参阅[如何添加或更改 Azure 管理员角色](../billing/billing-add-change-azure-subscription-administrator.md)。

此外，在访问 Microsoft Online Services 时，用户可能需要获得许可证才能打开服务的门户或执行管理任务。

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>将许可证分配到 Azure AD 中的用户
1. 使用全局管理员帐户或共同管理员帐户登录到 [Azure 门户](http://portal.azure.com)。
3. 选择要使用的、包含关联许可证的 Azure AD 和目录。
4. 选择左侧的“许可证”。 此时会显示可用的许可证列表。
5. 选择包含要分发的许可证的许可证计划。
6. 选择“分配用户”。
7. 选择要将许可证分配到的用户。
8. 单击“分配”按钮。  现在，该用户可登录到 Azure。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

