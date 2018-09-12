---
title: 在 PIM 中激活 Azure AD 目录角色 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中激活 Azure AD 目录角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 59dab4651366c3ad6579e0da660baee0c653d1a3
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665996"
---
# <a name="activate-my-azure-ad-directory-roles-in-pim"></a>在 PIM 中激活 Azure AD 目录角色

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 简化了企业管理以特权身份访问 Azure AD 中的资源和其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）的方式。  

如果符合管理角色的资格，即表示可以在需要执行特权操作时激活该角色。 例如，如果偶尔管理 Office 365 功能，则组织的特权角色管理员可能不会让你成为永久全局管理员，因为该角色也影响其他服务。 他们会让你符合 Azure AD 角色（例如“Exchange Online 管理员”）的资格。 可以在需要权限时，请求暂时分配该角色，并将在预定的时段内拥有管理员控制权。

本文面向需要在 PIM 中激活其 Azure AD 目录角色的管理员。

## <a name="activate-a-role"></a>激活角色

需要充当某个 Azure AD 目录角色时，可在 PIM 中使用“我的角色”导航选项来请求激活。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 有关如何将 PIM 磁贴添加到仪表板的信息，请参阅[开始使用 PIM](pim-getting-started.md)。

1. 单击“Azure AD 目录角色”。

1. 单击“我的角色”，查看你有资格获取的 Azure AD 目录角色列表。

    ![Azure AD 目录角色 - 我的角色](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. 找到想要激活的角色。

    ![Azure AD 目录角色 -“我的角色”列表](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. 单击“激活”打开“角色激活详细信息”窗格。

1. 如果角色需要多重身份验证 (MFA)，请单击“验证你的身份，然后继续”。 只需在每个会话中执行身份验证一次。

    ![在激活角色之前先执行 MFA 身份验证](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. 单击“验证我的身份”，并遵照说明提供其他安全验证。

    ![其他安全验证](./media/pim-how-to-activate-role/additional-security-verification.png)

1. 单击“激活”打开“激活”窗格。

    ![“激活”窗格](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. 根据需要指定自定义的激活开始时间。

1. 指定激活持续时间。

1. 在“激活原因”框中，输入该激活请求的原因。 某些角色要求提供问题票证编号。

    ![完成操作后的“激活”窗格](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. 单击“激活”。

    如果角色不需要审批，而且已经激活，则此角色将出现在活动角色列表中。 如果[角色需要审批](./azure-ad-pim-approval-workflow.md)才能激活，则浏览器右上角会显示一条通知，告知你请求正在等待审批。

    ![请求等待审批通知](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>查看请求的状态

可以查看等待激活的请求的状态。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“Azure AD 目录角色”。

1. 单击“我的请求”查看请求列表。

    ![Azure AD 目录角色 - 我的请求](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="use-a-role-immediately-after-activation"></a>激活后立即使用角色

由于缓存的原因，在未刷新的情况下，激活过程不会在 Azure 门户中立即发生。 如果需要在激活角色后减少延迟的可能性，可以使用门户中的“应用程序访问”页。 从此页访问的应用程序会立即检查新的角色分配。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“应用程序访问”页。

    ![PIM 应用程序访问](./media/pim-how-to-activate-role/pim-application-access.png)

1. 单击“Azure Active Directory”，在“所有用户”页上重新打开门户。

    单击此链接时，会强制刷新，并检查是否有新的 Azure AD 角色分配。

## <a name="deactivate-a-role"></a>停用角色

角色激活后，在达到时间限制（符合条件的时间段）时会自动停用。

如果提前完成了管理员任务，也可以在 Azure AD Privileged Identity Management 中手动停用角色。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“Azure AD 目录角色”。

1. 单击“我的角色”。

1. 单击“活动角色”，查看活动角色的列表。

1. 找到已用完的角色，然后单击“停用”。

## <a name="cancel-a-pending-request"></a>取消挂起的请求

如果不需要激活需要审批的角色，随时可以取消等待中的请求。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“Azure AD 目录角色”。

1. 单击“我的请求”。

1. 针对想要取消的角色，单击“取消”按钮。

    单击“取消”会取消该请求。 若要再次激活该角色，必须提交新的激活请求。

   ![取消等待中的请求](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中激活 Azure 资源角色](pim-resource-roles-activate-your-roles.md)
