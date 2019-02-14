---
title: 开始使用 PIM - Azure |Microsoft Docs
description: 了解如何启用并开始在 Azure 门户中使用 Azure AD Privileged Identity Management (PIM)。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 11/09/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09914de48df09dabd4069cd33e7acbea328fa89d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193146"
---
# <a name="start-using-pim"></a>开始使用 PIM

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，可以管理、控制和监视组织内的访问。 此范围包括访问 Azure 资源、Azure AD 和其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）。

本文介绍了如何启用并开始使用 PIM。

## <a name="prerequisites"></a>先决条件

若要使用 PIM，必须具有以下许可证之一：

- Azure AD Premium P2
- 企业移动性 + 安全性 (EMS) E5

有关详细信息，请参阅[使用 PIM 所要满足的许可要求](subscription-requirements.md)。

## <a name="first-person-to-use-pim"></a>要使用 PIM 的第一个人

如果你是第一个要在目录中使用 PIM 的人，系统会自动在目录中为你分配[安全管理员](../users-groups-roles/directory-assign-admin-roles.md#security-administrator)和[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色。 只有特权角色管理员才能管理用户的 Azure AD Directory 角色分配。 另外，还可以选择运行[安全向导](pim-security-wizard.md)，该向导会引导你完成初始发现和分配体验。

## <a name="enable-pim"></a>启用 PIM

若要开始在目录中使用 PIM，必须先启用 PIM。

1. 以目录的全局管理员身份登录到 [Azure 门户](https://portal.azure.com/)。

    只有拥有组织帐户（例如 @yourdomain.com）而非 Microsoft 帐户（例如 @outlook.com）的全局管理员才能为目录启用 PIM。

1. 单击“所有服务”，并查找 **Azure AD Privileged Identity Management** 服务。

    ![“所有服务”中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 单击此项可打开“PIM 快速入门”。

1. 在列表中单击“许可 PIM”。

    ![许可 PIM](./media/pim-getting-started/consent-pim.png)

1. 单击“验证我的身份”，以便通过 Azure MFA 来验证身份。 系统会要求你选取一个帐户。

    ![选取帐户](./media/pim-getting-started/pick-account.png)

1. 如果需要更多信息才能进行验证，系统会引导你完成相关过程。 有关详细信息，请参阅[获取有关双重验证的帮助](https://go.microsoft.com/fwlink/p/?LinkId=708614)。

    ![需要更多信息](./media/pim-getting-started/more-information-required.png)

    例如，系统可能会要求你提供电话验证。

    ![其他安全验证](./media/pim-getting-started/additional-security-verification.png)

1. 完成验证过程以后，请单击“许可”按钮。

1. 在出现的消息中单击“是”，对 PIM 服务表示许可。

    ![许可 PIM 消息](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>为 Azure AD 角色注册 PIM

为目录启用 PIM 以后，需注册 PIM，然后才能管理 Azure AD 角色。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 角色”。

    ![为 Azure AD 角色注册 PIM](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. 单击“注册”。

1. 在出现的消息中单击“是”以注册 PIM，以便管理 Azure AD 角色。

    ![“为 Azure AD 角色注册 PIM”消息](./media/pim-getting-started/sign-up-pim-message.png)

    注册完成后，会启用 Azure AD 选项。 可能需要刷新门户。

    若要了解如何发现并选择 Azure 资源，以便通过 PIM 进行保护，请参阅[在 PIM 中发现要管理的 Azure 资源](pim-resource-roles-discover-resources.md)。

## <a name="navigate-to-your-tasks"></a>导航到任务

设置 PIM 后，即可执行标识管理任务。

![PIM 的顶级任务 - 屏幕快照](./media/pim-getting-started/pim-quickstart-tasks.png)

| 任务 + 管理 | 说明 |
| --- | --- |
| **我的角色**  | 显示已向你分配的符合条件的活动角色列表。 可以在此处激活任何符合条件的已分配角色。 |
| **我的请求** | 显示要激活符合条件的角色分配的挂起的请求。 |
| **应用程序访问** | 使你能够减少潜在延迟，并在激活后立即使用角色。 |
| **审批请求** | 按用户显示你的目录中指定由你进行审批的要激活符合条件的角色的请求列表。 |
| **审阅访问权限** | 列出指定要由你完成的活动访问审阅（无论你是审阅自己还是审阅其他人的访问权限）。 |
| **Azure AD 角色** | 为特权角色管理员显示用来管理 Azure AD 目录角色分配的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示正在访问此仪表板的用户的相关信息，而非整个租户的相关信息。 |
| **Azure 资源** | 为特权角色管理员显示用来管理 Azure 资源角色分配的仪表板和设置。 此仪表板对非特权角色管理员禁用。 这些用户可以访问标题为“我的视图”的特殊仪表板。 “我的视图”仪表板仅显示正在访问此仪表板的用户的相关信息，而非整个租户的相关信息。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>将 PIM 磁贴添加到仪表板

为了更加方便地打开 PIM，应当将 PIM 磁贴添加到 Azure 门户仪表板中。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 单击“所有服务”，并查找 **Azure AD Privileged Identity Management** 服务。

    ![“所有服务”中的 Azure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. 单击此项可打开“PIM 快速入门”。

1. 选中“将边栏选项卡固定到仪表板”可将“PIM 快速入门”边栏选项卡固定到仪表板。

    ![将边栏选项卡固定到仪表板](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    在 Azure 仪表板上，你将看到如下所示的一个磁贴：

    ![“PIM 快速入门”磁贴](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中分配 Azure AD 目录角色](pim-how-to-add-role-to-user.md)
- [在 PIM 中发现要管理的 Azure 资源](pim-resource-roles-discover-resources.md)
