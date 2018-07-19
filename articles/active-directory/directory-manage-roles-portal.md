---
title: 在 Azure Active Directory 中查看管理员角色的成员 | Microsoft Docs
description: 现在，你可以在门户中查看和管理 Azure AD 管理员角色的成员。 本文面向经常需要管理角色分配的人员。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/02/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 39f7d69482845af48b1dce8c2e51f4acf77bf4fb
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448054"
---
# <a name="view-all-members-of-an-administrator-role-in-azure-active-directory"></a>在 Azure Active Directory 中查看管理员角色的所有成员

现在，你可以在 Azure Active Directory 门户中查看和管理管理员角色的所有成员。 如果你经常需要管理角色分配，也许更乐意使用此体验。 如果你曾经有过“这些角色究竟有什么用？”这样的疑问，可以查看每个 Azure AD 管理员角色的详细权限列表。

你也可以轻松查看自己的权限。 单击“你的角色”可以快速访问你的用户页，其中列出了为你分配的所有有效角色。 单击每行右侧的省略号会打开角色的详细说明。

![Azure AD 门户中的角色列表](./media/directory-manage-roles-portal/role-list.png)

选择整行可查看已分配的成员的列表。 可以选择“在 PIM 中管理”以查看其他管理功能。 特权角色管理员可将“永久”（始终充当该角色）分配更改为“符合条件”（仅当提升了权限时才充当该角色）。 如果没有 PIM，仍可以选择“在 PIM 中管理”来注册试用版。 Privileged Identity Management 需要 [Azure AD Premium P2 许可证计划](./privileged-identity-management/subscription-requirements.md)。

![管理员角色的成员列表](./media/directory-manage-roles-portal/member-list.png)

全局管理员或特权角色管理员可以轻松添加或删除成员、筛选列表，或选择某个成员，以转到用户页来查看为其分配的有效角色。 

## <a name="role-details-in-the-portal"></a>门户中的角色详细信息

查看某个角色的成员时，选择“说明”可以查看角色分配授予的权限的完整列表。 该页包含相关文档的链接，引导你对目录角色进行管理。

![管理员角色的权限列表](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>后续步骤

* 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
* 有关角色以及管理员角色分配的详细信息，请参阅[分配管理员角色](users-groups-roles/directory-assign-admin-roles.md)。
* 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](./fundamentals/users-default-permissions.md)。
