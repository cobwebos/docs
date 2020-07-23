---
title: 查看和分配管理员角色权限 - Azure AD | Microsoft Docs
description: 现在，你可以在门户中查看和管理 Azure AD 管理员角色的成员。 本文面向经常需要管理角色分配的人员。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 06/15/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e067e8d56f8a928f952648fc76cd5d6b7a1afe7
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86221237"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中查看和分配管理员角色

现在，可在 Azure Active Directory 门户中查看和管理管理员角色的所有成员。 如果你经常需要管理角色分配，也许更乐意使用此体验。 如果曾经有过“这些角色究竟有什么用？”这样的疑问，可查看每个 Azure AD 管理员角色的详细权限列表。

## <a name="view-all-roles"></a>查看所有角色

1. 登录到[Azure 门户](https://portal.azure.com)，然后选择 " **Azure Active Directory**"。

1. 选择“角色和管理员”以查看所有可用角色列表  。

1. 选择每行右侧的省略号，查看角色的权限。 选择一个角色来查看分配给该角色的用户。 如果你看到与下图不同的内容，请阅读[查看特权角色的分配](#view-assignments-for-privileged-roles)中的说明，以验证你是否处于 PRIVILEGED IDENTITY MANAGEMENT (PIM) 。

    ![Azure AD 门户中的角色列表](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>查看我的角色

也可轻松查看自己的权限。 在“角色和管理员”**** 页上选择“你的角色”****，可查看当前分配给你的角色。

## <a name="view-assignments-for-privileged-roles"></a>查看特权角色的分配

可选择“在 PIM 中管理”以查看其他管理功能****。 特权角色管理员可将“永久”（始终充当该角色）分配更改为“符合条件”（仅当提升了权限时才充当该角色）。 如果没有 Privileged Identity Management，仍然可以选择 "**在 PIM 中管理"** 以注册试用版。 Privileged Identity Management 需要 [Azure AD Premium P2 许可证计划](../privileged-identity-management/subscription-requirements.md)。

![管理员角色的成员列表](./media/directory-manage-roles-portal/member-list.png)

全局管理员或特权角色管理员可以轻松添加或删除成员、筛选列表，或选择某个成员以查看为其分配的有效角色。

> [!Note]
> 如果有 Azure AD 的高级 P2 许可证，并且已使用 Privileged Identity Management，则所有角色管理任务都将在 "特权标识管理" 中执行，而不是在 Azure AD 中执行。
>
> ![为已使用 PIM 并具有高级 P2 许可证的用户在 PIM 中管理 Azure AD 角色](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>查看用户的角色权限

查看某个角色的成员时，选择“说明”可以查看角色分配授予的权限的完整列表****。 该页包含相关文档的链接，引导你对目录角色进行管理。

![管理员角色的权限列表](./media/directory-manage-roles-portal/role-description.png)

## <a name="assign-a-role"></a>分配角色

1. 以 "全局管理员" 或 "特权角色管理员" 权限登录到[Azure 门户](https://portal.azure.com)，然后选择 " **Azure Active Directory**"。

1. 选择“角色和管理员”以查看所有可用角色列表****。

1. 选择要查看其分配的角色。

    ![管理员角色的权限列表](./media/directory-manage-roles-portal/member-list.png)

1. 选择 "**添加分配**" 并选择要分配的角色。 可选择“在 PIM 中管理”以查看其他管理功能****。 如果你看到与下图中的内容不同的内容，请阅读 "[查看特权角色的分配](#view-assignments-for-privileged-roles)" 中的说明，以验证你是否在 PIM 中。

    ![管理员角色的权限列表](./media/directory-manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>后续步骤

* 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
* 有关角色以及管理员角色分配的详细信息，请参阅[分配管理员角色](directory-assign-admin-roles.md)。
* 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)。
