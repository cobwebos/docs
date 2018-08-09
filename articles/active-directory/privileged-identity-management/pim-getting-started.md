---
title: Azure AD Privileged Identity Management 入门 | Microsoft Docs
description: 了解如何在 Azure 门户中使用 Azure Active Directory Privileged Identity Management 应用程序管理特权标识。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 4eeb58e6e7377c2c0ec7db850a84bf1e296500d2
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623045"
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>开始使用 Azure AD Privileged Identity Management

使用 Azure Active Directory (AD) Privileged Identity Management，可以管理、控制和监视组织内的访问。 此范围包括访问 Azure 资源、Azure AD 和其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）。

本文介绍了如何将 Azure AD PIM 应用添加到 Azure 门户仪表板。

## <a name="add-the-privileged-identity-management-application"></a>添加 Privileged Identity Management 应用程序

在使用 Azure AD Privileged Identity Management 之前，需要将应用程序添加到 Azure 门户仪表板。

1. 以目录的全局管理员身份登录 [Azure 门户](https://portal.azure.com/) 。
2. 如果组织有多个目录，请在 Azure 门户的右上角选择用户名。 选择要在其中使用 PIM 的目录。
3. 选择“所有服务”，并使用“筛选器”文本框搜索“Azure AD Privileged Identity Management”。
4. 选中“固定到仪表板”，并单击“创建”。 Privileged Identity Management 应用程序打开。

如果你是第一个在目录中使用 Azure AD Privileged Identity Management 的人，系统会自动在目录中为你分配“安全管理员”和“特权角色管理员”角色。 只有特权角色管理员才能管理用户的 Azure AD Directory 角色分配。 此外，还可以选择运行[安全向导](pim-security-wizard.md)。 系统会引导你完成初始的发现和分配体验。

## <a name="navigate-to-your-tasks"></a>导航到任务

设置 Azure AD Privileged Identity Management 后，每次打开应用程序时均会看到导航边栏选项卡。 使用此边栏选项卡可完成标识管理任务。

![PIM 的顶级任务 - 屏幕快照](./media/pim-getting-started/PIM_Tasks_New.png)

- “我的角色”显示已向你分配的符合条件的活动角色列表。 可以在此处激活任何符合条件的已分配角色。
- “审批请求(预览)”显示你的目录中的用户发出的用于激活符合条件的 Azure Active Directory 角色的请求列表，并且已指定由你来审批这些请求。 [了解详细信息。](./azure-ad-pim-approval-workflow.md)
- “挂起的请求(预览)”显示用于激活符合条件的角色分配的所有挂起的请求。
- “审阅访问权限”列出已指定要由你完成的活动访问审阅（无论你是审阅自己还是审阅其他人的访问权限）。
- “Azure Active Directory 角色”（位于左侧导航菜单的“管理”部分下）显示特权角色管理员用于管理角色分配、更改角色激活设置、开始访问审阅等的仪表板。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示正在访问此仪表板的用户的相关信息，而非整个租户的相关信息。
- “Azure 资源角色(预览)”（位于左侧导航菜单的“管理”部分下）显示可让角色分配选择的订阅资源列表 

## <a name="next-steps"></a>后续步骤
[Azure AD Privileged Identity Management 概述](pim-configure.md) 包括有关如何管理组织中管理访问权限的更多详细信息。

[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
