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
ms.openlocfilehash: e548e99cc60d67b477fd087b993764bf7f223592
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541180"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中查看和分配管理员角色

现在，可在 Azure Active Directory 门户中查看和管理管理员角色的所有成员。 如果你经常需要管理角色分配，也许更乐意使用此体验。 如果曾经有过“这些角色究竟有什么用？”这样的疑问，可查看每个 Azure AD 管理员角色的详细权限列表。

## <a name="view-all-roles"></a>查看所有角色

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“Azure Active Directory”。

1. 选择“角色和管理员”以查看所有可用角色列表  。

1. 选择每行右侧的省略号可查看角色的权限。 选择某个角色可查看分配给该角色的用户。 如果你看到与下图不同的内容，请阅读[查看特权角色的分配](#view-assignments-for-privileged-roles)中的“说明”，以验证你是否在 Privileged Identity Management (PIM) 中。

    ![Azure AD 门户中的角色列表](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>查看我的角色

也可轻松查看自己的权限。 在“角色和管理员”**** 页上选择“你的角色”****，可查看当前分配给你的角色。

## <a name="view-assignments-for-privileged-roles"></a>查看特权角色的分配

可选择“在 PIM 中管理”以查看其他管理功能****。 特权角色管理员可将“永久”（始终充当该角色）分配更改为“符合条件”（仅当提升了权限时才充当该角色）。 如果没有 Privileged Identity Management，仍可以选择“在 PIM 中管理”来注册试用版。 Privileged Identity Management 需要 [Azure AD Premium P2 许可证计划](../privileged-identity-management/subscription-requirements.md)。

![管理员角色的成员列表](./media/directory-manage-roles-portal/member-list.png)

全局管理员或特权角色管理员可以轻松添加或删除成员、筛选列表，或选择某个成员以查看为其分配的有效角色。

> [!Note]
> 如果你有 Azure AD Premium P2 许可证，并且已使用 Privileged Identity Management，则所有角色管理任务都会在 Privilege Identity Management 中执行，而不会在 Azure AD 中执行。
>
> ![在 PIM 中为已使用 PIM 且具有 Premium P2 许可证的用户管理的 Azure AD 角色](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>查看用户的角色权限

查看某个角色的成员时，选择“说明”可以查看角色分配授予的权限的完整列表****。 该页包含相关文档的链接，引导你对目录角色进行管理。

![显示 "全局管理员描述" 页面的屏幕截图。](./media/directory-manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>下载角色分配

若要下载特定角色的所有分配，请在“角色和管理员”页上选择一个角色，然后选择“下载角色分配”。 将会下载一个 CSV 文件，其中列出了该角色的所有范围内的分配。

![下载角色的所有分配](./media/directory-manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>分配角色

1. 以全局管理员或特权角色管理员权限登录到 [Azure 门户](https://portal.azure.com)，然后选择“Azure Active Directory”。

1. 选择“角色和管理员”以查看所有可用角色列表****。

1. 选择一个角色，查看其分配情况。

    ![显示 "用户管理员-分配" 页面并选择 "在 PIM 中管理" 操作的屏幕截图。](./media/directory-manage-roles-portal/member-list.png)

1. 选择“添加分配”，然后选择要分配的角色。 可选择“在 PIM 中管理”以查看其他管理功能****。 如果你看到与下图不同的内容，请阅读[查看特权角色的分配](#view-assignments-for-privileged-roles)中的“说明”，以验证你是否在 PIM 中。

    ![管理员角色的权限列表](./media/directory-manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>后续步骤

* 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
* 有关角色以及管理员角色分配的详细信息，请参阅[分配管理员角色](directory-assign-admin-roles.md)。
* 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)。
