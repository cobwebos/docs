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
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472854"
---
# <a name="start-using-privileged-identity-management"></a>开始使用 Privileged Identity Management

使用 Privileged Identity Management (PIM)，可以管理、控制和监视 Azure Active Directory (Azure AD) 组织内的访问。 此范围包括访问 Azure 资源、Azure AD 和其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）。

本文介绍了如何启用并开始使用 Privileged Identity Management。

## <a name="prerequisites"></a>先决条件

若要使用 Privileged Identity Management，则必须具有以下许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5

有关详细信息，请参阅[使用 Privileged Identity Management 的许可要求](subscription-requirements.md)。

## <a name="sign-up-pim-for-azure-ad-roles"></a>为 Azure AD 角色注册 PIM

为目录启用 Privileged Identity Management 以后，需注册 Privileged Identity Management，然后才能管理 Azure AD 角色。

1. 打开**Azure AD 特权标识管理**。

1. 选择“Azure AD 角色”****。

    ![注册针对 Azure AD 角色的 Privileged Identity Management](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. 选择 **"注册**"。

1. 在出现的消息中单击“是”以注册 Privileged Identity Management，以便管理 Azure AD 角色。****

    ![注册针对 Azure AD 角色消息的 Privileged Identity Management](./media/pim-getting-started/sign-up-pim-message.png)

    注册完成后，会启用 Azure AD 选项。 可能需要刷新门户。

    若要了解如何发现并选择 Azure 资源，以便通过 Privileged Identity Management 进行保护，请参阅[在 Privileged Identity Management 中发现要管理的 Azure 资源](pim-resource-roles-discover-resources.md)。

## <a name="navigate-to-your-tasks"></a>导航到任务

设置 Privileged Identity Management 后，即可启动标识管理任务。

![Privileged Identity Management 中的导航窗口，其中显示“任务”和“管理”选项](./media/pim-getting-started/pim-quickstart-tasks.png)

| 任务 + 管理 | 描述 |
| --- | --- |
| **我的角色**  | 显示已向你分配的符合条件的活动角色列表。 可以在此处激活任何符合条件的已分配角色。 |
| **我的请求** | 显示要激活符合条件的角色分配的挂起的请求。 |
| **审批请求** | 按用户显示你的目录中指定由你进行审批的要激活符合条件的角色的请求列表。 |
| **评审访问权限** | 列出指定要由你完成的活动访问审阅（无论你是审阅自己还是审阅其他人的访问权限）。 |
| **Azure AD 角色** | 为特权角色管理员显示用来管理 Azure AD 角色分配的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示正在访问此仪表板的用户的相关信息，而非整个租户的相关信息。 |
| **Azure 资源** | 为特权角色管理员显示用来管理 Azure 资源角色分配的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示正在访问此仪表板的用户的相关信息，而非整个租户的相关信息。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>将 PIM 磁贴添加到仪表板

为了更轻松地打开特权标识管理，请向 Azure 门户仪表板添加特权标识管理磁贴。

1. 登录到 Azure[门户](https://portal.azure.com/)。

1. 选择**所有服务**并查找**Azure AD 特权标识管理**服务。

    ![“所有服务”中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 选择特权身份管理快速入门。

1. 选中“将边栏选项卡固定到仪表板”**** 可将“Privileged Identity Management 快速入门”边栏选项卡固定到仪表板。

    ![用于将 Privileged Identity Management 边栏选项卡固定到仪表板的图钉图标](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    在 Azure 仪表板上，你将看到如下所示的一个磁贴：

    ![仪表板上的 Privileged Identity Management 快速入门磁贴](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中发现要管理的 Azure 资源](pim-resource-roles-discover-resources.md)
