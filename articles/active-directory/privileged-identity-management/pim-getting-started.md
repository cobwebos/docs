---
title: 开始使用 PIM-Azure Active Directory |Microsoft Docs
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
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 733743f4680f3197a1754d87b4672641b2321907
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399333"
---
# <a name="start-using-privileged-identity-management"></a>开始使用 Privileged Identity Management

使用 Privileged Identity Management （PIM），您可以管理、控制和监视 Azure Active Directory （Azure AD）组织内的访问权限。 此作用域包括对 Azure 资源、Azure AD 以及其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）的访问权限。

本文介绍如何启用和开始使用 Privileged Identity Management。

## <a name="prerequisites"></a>必备条件

若要使用 Privileged Identity Management，则必须具有以下许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5

有关详细信息，请参阅[使用 Privileged Identity Management 的许可要求](subscription-requirements.md)。

## <a name="first-person-to-use-pim"></a>要使用 PIM 的第一个人

如果你是在目录中使用 Privileged Identity Management 的第一个用户，则会在目录中自动为你分配 "[安全管理员](../users-groups-roles/directory-assign-admin-roles.md#security-administrator)" 和 "[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)" 角色。 只有特权角色管理员才能管理用户 Azure AD 角色分配。 另外，还可以选择运行[安全向导](pim-security-wizard.md)，该向导会引导你完成初始发现和分配体验。

## <a name="enable-pim"></a>启用 PIM

若要开始在目录中使用 Privileged Identity Management，必须先启用 Privileged Identity Management。

1. 以目录的全局管理员身份登录到 [Azure 门户](https://portal.azure.com/)。

    您必须是具有组织帐户（例如 @yourdomain.com）的全局管理员，而不是 Microsoft 帐户（例如 @outlook.com）才能为目录启用 Privileged Identity Management。

1. 单击“所有服务”，并查找 **Azure AD Privileged Identity Management** 服务。

    ![“所有服务”中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 单击打开 "Privileged Identity Management 快速入门"。

1. 在列表中单击“许可 PIM”。

    ![同意 Privileged Identity Management 启用 Privileged Identity Management](./media/pim-getting-started/consent-pim.png)

1. 单击“验证我的身份”，以便通过 Azure MFA 来验证身份。 系统会要求你选取一个帐户。

    ![选择帐户窗口以验证你的身份](./media/pim-getting-started/pick-account.png)

1. 如果需要更多信息才能进行验证，系统会引导你完成相关过程。 有关详细信息，请参阅[获取有关双重验证的帮助](../user-help/multi-factor-authentication-end-user-troubleshoot.md)。

    ![如果你的组织需要更多信息，则需要更多详细信息窗口](./media/pim-getting-started/more-information-required.png)

    例如，系统可能会要求你提供电话验证。

    ![询问如何与你联系的其他安全验证页面](./media/pim-getting-started/additional-security-verification.png)

1. 完成验证过程以后，请单击“许可”按钮。

1. 在出现的消息中，单击 **"是"** 以同意 Privileged Identity Management 服务。

    ![同意 Privileged Identity Management 邮件完成许可过程](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>为 Azure AD 角色注册 PIM

为目录启用 Privileged Identity Management 后，你将需要注册 Privileged Identity Management 来管理 Azure AD 角色。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 角色”。

    ![为 Azure AD 角色注册 Privileged Identity Management](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. 单击“注册”。

1. 在出现的消息中，单击 **"是"** 注册 Privileged Identity Management 管理 Azure AD 角色。

    ![注册 Azure AD 角色的 Privileged Identity Management 消息](./media/pim-getting-started/sign-up-pim-message.png)

    注册完成后，会启用 Azure AD 选项。 可能需要刷新门户。

    有关如何发现并选择要保护 Privileged Identity Management 的 Azure 资源的信息，请参阅[在 Privileged Identity Management 中发现要管理的 azure 资源](pim-resource-roles-discover-resources.md)。

## <a name="navigate-to-your-tasks"></a>导航到任务

设置 Privileged Identity Management 后，你可以开始身份管理任务。

![导航窗口中 Privileged Identity Management 显示任务和管理选项](./media/pim-getting-started/pim-quickstart-tasks.png)

| 任务 + 管理 | 说明 |
| --- | --- |
| **我的角色**  | 显示已向你分配的符合条件的活动角色列表。 可以在此处激活任何符合条件的已分配角色。 |
| **我的请求** | 显示要激活符合条件的角色分配的挂起的请求。 |
| **审批请求** | 按用户显示你的目录中指定由你进行审批的要激活符合条件的角色的请求列表。 |
| **审阅访问权限** | 列出指定要由你完成的活动访问审阅（无论你是审阅自己还是审阅其他人的访问权限）。 |
| **Azure AD 角色** | 显示用于管理 Azure AD 角色分配的特权角色管理员的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示正在访问此仪表板的用户的相关信息，而非整个租户的相关信息。 |
| **Azure 资源** | 为特权角色管理员显示用来管理 Azure 资源角色分配的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示正在访问此仪表板的用户的相关信息，而非整个租户的相关信息。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>将 PIM 磁贴添加到仪表板

为了更轻松地打开 Privileged Identity Management，应将 Privileged Identity Management 磁贴添加到 Azure 门户仪表板。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 单击“所有服务”，并查找 **Azure AD Privileged Identity Management** 服务。

    ![“所有服务”中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 单击打开 "Privileged Identity Management 快速入门"。

1. 选中 "**将边栏选项卡固定到仪表板**"，将 Privileged Identity Management 快速入门边栏选项卡固定到仪表板

    ![用于将 Privileged Identity Management 边栏选项卡固定到仪表板的图钉图标](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    在 Azure 仪表板上，你将看到如下所示的一个磁贴：

    ![在仪表板上 Privileged Identity Management 快速启动磁贴](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure AD 角色](pim-how-to-add-role-to-user.md)
- [在 Privileged Identity Management 中发现要管理的 Azure 资源](pim-resource-roles-discover-resources.md)
