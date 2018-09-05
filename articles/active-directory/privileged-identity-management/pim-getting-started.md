---
title: 开始使用 PIM - Azure |Microsoft Docs
description: 了解如何开始在 Azure 门户中使用 Azure AD Privileged Identity Management (PIM)。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5b3bff27821964648713b02589c941c99e3eb03d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190083"
---
# <a name="start-using-pim"></a>开始使用 PIM

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，可以管理、控制和监视组织内的访问。 此范围包括访问 Azure 资源、Azure AD 和其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）。

本文介绍了如何将 Azure AD PIM 应用添加到 Azure 门户仪表板。

## <a name="first-person-to-use-pim"></a>要使用 PIM 的第一个人

如果你是第一个要在目录中使用 PIM 的人，系统会自动在目录中为你分配[安全管理员](../users-groups-roles/directory-assign-admin-roles.md#security-administrator)和[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色。 只有特权角色管理员才能管理用户的 Azure AD Directory 角色分配。 此外，还可以选择运行[安全向导](pim-security-wizard.md)。 系统会引导你完成初始的发现和分配体验。

## <a name="add-pim-tile-to-the-dashboard"></a>将 PIM 磁贴添加到仪表板

为了更加方便地打开 PIM，应当将 PIM 磁贴添加到 Azure 门户仪表板中。

1. 以目录的全局管理员身份登录到 [Azure 门户](https://portal.azure.com/)。

1. 单击“所有服务”，并查找 **Azure AD Privileged Identity Management** 服务。

    ![“所有服务”中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 单击此项可打开“PIM 快速入门”。

1. 选中“将边栏选项卡固定到仪表板”可将“PIM 快速入门”边栏选项卡固定到仪表板。

    ![将边栏选项卡固定到仪表板](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    在 Azure 仪表板上，你将看到如下所示的一个磁贴：

    ![“PIM 快速入门”磁贴](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="navigate-to-your-tasks"></a>导航到任务

在设置 PIM 后，可以使用此边栏选项卡来完成标识管理任务。

![PIM 的顶级任务 - 屏幕快照](./media/pim-getting-started/pim-quickstart-tasks.png)

| 任务 + 管理 | 说明 |
| --- | --- |
| **我的角色**  | 显示已向你分配的符合条件的活动角色列表。 可以在此处激活任何符合条件的已分配角色。 |
| **我的请求** | 显示要激活符合条件的角色分配的挂起的请求。 |
| **应用程序访问** | 使你能够减少潜在延迟，并在激活后立即使用角色。 |
| **审批请求** | 按用户显示你的目录中指定由你进行审批的要激活符合条件的角色的请求列表。 |
| **审阅访问权限** | 列出指定要由你完成的活动访问审阅（无论你是审阅自己还是审阅其他人的访问权限）。 |
| **Azure AD 目录角色** | 为特权角色管理员显示用来管理 Azure AD 目录角色分配的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示正在访问此仪表板的用户的相关信息，而非整个租户的相关信息。 |
| **Azure 资源** | 为特权角色管理员显示用来管理 Azure 资源角色分配的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示正在访问此仪表板的用户的相关信息，而非整个租户的相关信息。 |

## <a name="next-steps"></a>后续步骤

- [在 PIM 中激活 Azure AD 目录角色](pim-how-to-activate-role.md)
- [在 PIM 中激活 Azure 资源角色](pim-resource-roles-activate-your-roles.md)
