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
ms.openlocfilehash: 0403fe3cf0bf8cfaf9c722edadbecd2fee61cb46
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056279"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>无法在 Privileged Identity Management 中管理的角色

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 可以管理所有 [Azure AD 角色](../users-groups-roles/directory-assign-admin-roles.md)和所有 [Azure 角色](../../role-based-access-control/built-in-roles.md)。 Azure 角色还可以包括附加到管理组、订阅、资源组和资源的自定义角色。 然而，有极少角色无法管理。 本文介绍无法在 Privileged Identity Management 中管理的角色。

## <a name="classic-subscription-administrator-roles"></a>经典订阅管理员角色

在 Privileged Identity Management 中无法管理以下经典订阅管理员角色：

- 帐户管理员
- 服务管理员
- 共同管理员

有关经典订阅管理员角色的详细信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="what-about-microsoft-365-admin-roles"></a>对于Microsoft 365 管理员角色，会怎样？

我们支持 Azure AD 角色和管理员门户体验中的所有 Microsoft 365 角色（如 Exchange 管理员和 SharePoint 管理员），但不支持 Exchange RBAC 或 SharePoint RBAC 中的特定角色。 有关这些 Microsoft 365 服务的详细信息，请参阅 [Microsoft 365 管理员角色](/office365/admin/add-users/about-admin-roles)。

> [!NOTE]
> 符合 SharePoint 管理员角色、设备管理员角色以及尝试访问 Microsoft 安全与合规中心的任何角色条件的用户，激活其角色后可能会遇到最多几小时的延迟。 我们正与这些团队合作解决问题。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)