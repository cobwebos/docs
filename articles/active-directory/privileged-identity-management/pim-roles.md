---
title: 在 Privileged Identity Management Azure Active Directory 中无法管理的角色 |Microsoft Docs
description: 介绍 Azure AD Privileged Identity Management (PIM) 中无法管理的角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895201"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>在 Privileged Identity Management 中无法管理的角色

Azure Active Directory （Azure AD） Privileged Identity Management （PIM）可以管理所有[Azure AD 角色](../users-groups-roles/directory-assign-admin-roles.md)和所有[Azure 资源角色](../../role-based-access-control/built-in-roles.md)。 这些角色还包括附加到管理组、订阅、资源组和资源的自定义角色。 然而，有极少角色无法管理。 本文介绍了在 Privileged Identity Management 中无法管理的角色。

## <a name="classic-subscription-administrator-roles"></a>经典订阅管理员角色

你无法在 Privileged Identity Management 中管理以下经典订阅管理员角色：

- 帐户管理员
- 服务管理员
- 共同管理员

有关经典订阅管理员角色的详细信息，请参阅[经典订阅管理员角色、Azure RBAC 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="what-about-office-365-admin-roles"></a>那么 Office 365 管理员角色呢？

Exchange Online 或 SharePoint Online 中的角色（Exchange 管理员和 SharePoint 管理员除外）未在 Azure AD 中表示，因此无法在 Privileged Identity Management 中进行管理。 关于这些 Office 365 服务的详细信息，请参阅 [Office 365 管理员角色](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)。

> [!NOTE]
> SharePoint 管理员具有通过 SharePoint Online 管理中心访问 SharePoint Online 的管理权限，可在 SharePoint Online 中执行几乎所有的任务。 在 Privileged Identity Management 中激活后，符合条件的用户可能会在 SharePoint 中使用此角色时遇到延迟。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)
