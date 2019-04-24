---
title: 无法管理 PIM-Azure Active Directory 中的角色 |Microsoft Docs
description: 介绍 Azure AD Privileged Identity Management (PIM) 中无法管理的角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa5fb632ee5fd9c18bde7443e81fe2ef6e5335e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437267"
---
# <a name="roles-you-cannot-manage-in-pim"></a>PIM 中无法管理的角色

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 使你能够管理所有[Azure AD 角色](../users-groups-roles/directory-assign-admin-roles.md)和全部[Azure 资源角色](../../role-based-access-control/built-in-roles.md)。 这些角色还包括附加到管理组、订阅、资源组和资源的自定义角色。 然而，有极少角色无法管理。 本文介绍 PIM 中无法管理的角色。

## <a name="classic-subscription-administrator-roles"></a>经典订阅管理员角色

在 PIM 中无法管理以下经典订阅管理员角色：

- 帐户管理员
- 服务管理员
- 共同管理员

有关经典订阅管理员角色的详细信息，请参阅[经典订阅管理员角色、Azure RBAC 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

## <a name="what-about-office-365-admin-roles"></a>那么 Office 365 管理员角色呢？

Exchange Online 或 SharePoint Online 中的角色（Exchange 管理员和 SharePoint 管理员除外）在 Azure AD 中不表示，因此无法在 PIM 中进行管理。 关于这些 Office 365 服务的详细信息，请参阅 [Office 365 管理员角色](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles)。

> [!NOTE]
> SharePoint 管理员具有通过 SharePoint Online 管理中心访问 SharePoint Online 的管理权限，可在 SharePoint Online 中执行几乎所有的任务。 在 PIM 中激活后，符合条件的用户在 SharePoint 中使用此角色可能会发生延迟。

## <a name="next-steps"></a>后续步骤

- [将 Azure AD PIM 中的角色分配](pim-how-to-add-role-to-user.md)
- [在 PIM 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)
