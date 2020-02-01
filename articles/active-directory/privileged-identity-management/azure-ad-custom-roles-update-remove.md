---
title: 在 Privileged Identity Management （PIM）中更新或删除 Azure AD 自定义角色分配 |Microsoft Docs
description: 如何更新或删除 Azure AD 自定义角色分配 Privileged Identity Management （PIM）
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace189db569941371026b76c4438515f4c53e77b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896506"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>在 Privileged Identity Management 中更新或删除分配的 Azure AD 自定义角色

本文介绍如何使用 Privileged Identity Management （PIM）来更新或删除为 Azure Active Directory （Azure AD）管理体验中的应用程序管理创建的自定义角色。 

- 有关创建自定义角色以在 Azure AD 中委派应用程序管理的详细信息，请参阅[Azure Active Directory 中的自定义管理员角色（预览版）](../users-groups-roles/roles-custom-overview.md)。 
- 如果尚未使用 Privileged Identity Management，请在[开始使用 Privileged Identity Management](pim-getting-started.md)获取详细信息。

> [!NOTE]
> 预览期间，Azure AD 自定义角色不与内置目录角色集成。 功能正式发布后，会在内置角色中进行角色管理角色管理。

## <a name="update-or-remove-an-assignment"></a>更新或删除分配

按照以下步骤更新或删除现有的自定义角色分配。

1. 使用分配给特权角色管理员角色的用户帐户登录到 Azure 门户中的[Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) 。
1. 选择 " **Azure AD 自定义角色（预览版）** "。

    ![选择 Azure AD 自定义角色预览以查看符合条件的角色分配](./media/azure-ad-custom-roles-assign/view-custom.png)

1. 选择 "**角色**" 以查看 Azure AD 应用程序的自定义角色的**分配**列表。

    ![选择角色查看符合条件的角色分配列表](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. 选择要更新或删除的角色。
1. 在**符合条件的角色**或**活动角色**选项卡上找到角色分配。
1. 选择 "**更新**" 或 "**删除**" 以更新或删除角色分配。

    ![在符合条件的角色分配中选择 "删除" 或 "更新"](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>后续步骤

- [激活 Azure AD 自定义角色](azure-ad-custom-roles-assign.md)
- [分配 Azure AD 自定义角色](azure-ad-custom-roles-assign.md)
- [配置 Azure AD 自定义角色分配](azure-ad-custom-roles-configure.md)
