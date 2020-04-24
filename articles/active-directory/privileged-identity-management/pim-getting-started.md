---
title: 开始使用 PIM - Azure Active Directory | Microsoft Docs
description: 了解如何启用并开始在 Azure 门户中使用 Azure AD Privileged Identity Management (PIM)。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46f6c77916c7be62247c69b12dff1982e5781aff
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112212"
---
# <a name="start-using-privileged-identity-management"></a>开始使用 Privileged Identity Management

本文介绍如何启用 Privileged Identity Management （PIM）并开始使用它。

使用 Privileged Identity Management （PIM）可以管理、控制和监视 Azure Active Directory （Azure AD）组织中的访问权限。 使用 PIM，你可以提供对 Azure 资源、Azure AD 资源以及其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）的按需和实时访问权限。

## <a name="prerequisites"></a>必备条件

若要使用 Privileged Identity Management，则必须具有以下许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5

有关详细信息，请参阅[使用 Privileged Identity Management 的许可要求](subscription-requirements.md)。

## <a name="prepare-pim-for-azure-ad-roles"></a>为 Azure AD 角色准备 PIM

为目录启用 Privileged Identity Management 后，可以准备 Privileged Identity Management 来管理 Azure AD 角色。

下面是我们建议为按顺序准备 Azure AD 角色的任务：

1. [配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)。
1. [为符合条件分配](pim-how-to-add-role-to-user.md)。
1. [允许符合条件的用户实时激活其 Azure AD 角色](pim-how-to-activate-role.md)。

## <a name="prepare-pim-for-azure-roles"></a>为 Azure 角色准备 PIM

为目录启用 Privileged Identity Management 后，可以准备 Privileged Identity Management，以便在订阅上管理 azure 资源访问权限的 Azure 角色。

下面是我们建议为 Azure 角色准备的任务，按顺序：

1. [发现 Azure 资源](pim-resource-roles-discover-resources.md)
1. [配置 Azure 角色设置](pim-resource-roles-configure-role-settings.md)。
1. [为符合条件分配](pim-resource-roles-assign-roles.md)。
1. [允许符合条件的用户实时激活其 Azure 角色](pim-resource-roles-activate-your-roles.md)。

## <a name="navigate-to-your-tasks"></a>导航到任务

设置 Privileged Identity Management 后，你可以了解你的方法。

![Privileged Identity Management 中的导航窗口，其中显示“任务”和“管理”选项](./media/pim-getting-started/pim-quickstart-tasks.png)

| 任务 + 管理 | 说明 |
| --- | --- |
| **我的角色**  | 显示已向你分配的符合条件的活动角色列表。 可以在此处激活任何符合条件的已分配角色。 |
| **我的请求** | 显示要激活符合条件的角色分配的挂起的请求。 |
| **审批请求** | 按用户显示你的目录中指定由你进行审批的要激活符合条件的角色的请求列表。 |
| **评审访问权限** | 列出指定要由你完成的活动访问审阅（无论你是审阅自己还是审阅其他人的访问权限）。 |
| **Azure AD 角色** | 显示用于管理 Azure AD 角色分配的特权角色管理员的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示正在访问此仪表板的用户的相关信息，而非整个租户的相关信息。 |
| **Azure 资源** | 显示用于管理 Azure 资源角色分配的特权角色管理员的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示正在访问此仪表板的用户的相关信息，而非整个租户的相关信息。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>将 PIM 磁贴添加到仪表板

为了更轻松地打开 Privileged Identity Management，请将 PIM 磁贴添加到 Azure 门户仪表板。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 选择 "**所有服务**" 并查找**Azure AD Privileged Identity Management**服务。

    ![“所有服务”中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 选择 "Privileged Identity Management 快速入门"。

1. 选中“将边栏选项卡固定到仪表板”**** 可将“Privileged Identity Management 快速入门”边栏选项卡固定到仪表板。

    ![用于将 Privileged Identity Management 边栏选项卡固定到仪表板的图钉图标](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    在 Azure 仪表板上，你将看到如下所示的一个磁贴：

    ![仪表板上的 Privileged Identity Management 快速入门磁贴](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中管理 Azure 资源访问](pim-resource-roles-discover-resources.md)
