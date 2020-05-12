---
title: 无法在 Privileged Identity Management 中管理的角色 - Azure Active Directory | Microsoft Docs
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
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d1d7ad27c43a0f8b4bbb763a1eb933a63cec58e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115636"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>无法在 Privileged Identity Management 中管理的角色

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 可以管理所有 [Azure AD 角色](../users-groups-roles/directory-assign-admin-roles.md)和所有 [Azure 角色](../../role-based-access-control/built-in-roles.md)。 Azure 角色还可以包括附加到管理组、订阅、资源组和资源的自定义角色。 然而，有极少角色无法管理。 本文介绍无法在 Privileged Identity Management 中管理的角色。

## <a name="classic-subscription-administrator-roles"></a>经典订阅管理员角色

在 Privileged Identity Management 中无法管理以下经典订阅管理员角色：

- 帐户管理员
- 服务管理员
- 共同管理员

有关经典订阅管理员角色的详细信息，请参阅[经典订阅管理员角色、Azure RBAC 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="what-about-office-365-admin-roles"></a>那么 Office 365 管理员角色呢？

我们支持 Azure AD 角色和管理员门户体验中的所有 Office365 角色（如 Exchange 管理员和 SharePoint 管理员），但不支持 Exchange RBAC 或 SharePoint RBAC 中的特定角色。 关于这些 Office 365 服务的详细信息，请参阅 [Office 365 管理员角色](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)。

> [!NOTE]
> 启用 SharePoint 管理员角色、设备管理员角色和尝试访问 Microsoft 安全和合规性中心的任何角色的有资格用户在激活其角色后，可能会遇到长达数小时的延迟。 我们正与这些团队合作解决问题。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)
