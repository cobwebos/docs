---
title: 如何激活或停用角色 | Microsoft 文档
description: 了解如何使用 Azure Privileged Identity Management 应用程序激活特权标识的角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 02/14/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 33c41becb8ed741b7db5e3f89d193ca2ae7b6390
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617126"
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中激活或停用角色
Azure Active Directory (AD) Privileged Identity Management 简化了企业管理以特权身份访问 Azure AD 中的资源和其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）的方式。  

如果符合管理角色的资格，即表示可以在需要执行特权操作时激活该角色。 例如，如果偶尔管理 Office 365 功能，则组织的特权角色管理员可能不会让你成为永久全局管理员，因为该角色也影响其他服务。 他们会让你符合 Azure AD 角色（例如“Exchange Online 管理员”）的资格。 可以在需要权限时，请求暂时分配该角色，并将在预定的时段内拥有管理控制权。

本文面向需要在 Azure AD Privileged Identity Management (PIM) 中激活其角色的管理员。 其中逐步讲解了如何在需要权限时激活角色，在完成任务后停用该角色。 此外，特权角色管理员可能需要获得批准才能激活角色（预览）。 在此处了解有关 [PIM 批准流](./azure-ad-pim-approval-workflow.md)的详细信息。

## <a name="add-the-privileged-identity-management-application"></a>添加 Privileged Identity Management 应用程序
在 [Azure 门户中](https://portal.azure.com/)使用 Azure AD Privileged Identity Management 应用程序请求角色激活，即使要在另一个门户中或通过 PowerShell 来操作，也是如此。 如果 Azure 门户中没有 Azure AD Privileged Identity Management 应用程序，请遵循以下步骤开始操作。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 Azure 门户右上角的用户名，并选择要操作的目录。
3. 选择“所有服务”，并使用“筛选器”文本框搜索“Azure AD Privileged Identity Management”。
4. 选中“固定到仪表板”，并单击“创建”。 Privileged Identity Management 应用程序打开。

## <a name="activate-a-role"></a>激活角色
需要充当某个角色时，可在 Azure AD Privileged Identity Management 应用程序左侧导航栏中选择“我的角色”导航选项来请求激活。

1. 登录到 [Azure 门户](https://portal.azure.com/)，选择“Azure AD Privileged Identity Management”磁贴。
2. 选择“我的角色”。 该页顶部的“分组”中将显示分配给你的合格角色列表。
3. 选择要激活的角色。
4. 选择“激活”。 此时会显示“请求激活角色”边栏选项卡。
5. 有些角色要求事先进行多重身份验证 (MFA) 才能激活。 只需在每个会话中执行身份验证一次。
   
    ![在激活角色之前先执行 MFA 身份验证 - 屏幕截图](./media/pim-how-to-activate-role/PIM_activation_MFA.png)
6. 在文本字段中输入请求激活的原因。  某些角色要求提供问题票证编号。
7. 选择“确定”。  如果角色不需要审批，而且已经激活，此角色将出现在激活角色列表中（在合格的角色分配列表下方）。 如果[角色需要审批](./azure-ad-pim-approval-workflow.md)才能激活，则将在浏览器右上角显示一条简短的 Toast 通知，告知你请求尚待审批。

    ![请求挂起通知 - 屏幕截图](./media/pim-how-to-activate-role/PIM_Request_Pending_Toast2.png)

## <a name="deactivate-a-role"></a>停用角色
角色激活后，在达到时间限制（符合条件的时间段）时会自动停用。

如果提前完成了管理任务，也可在 Azure AD Privileged Identity Management 应用程序中手动停用角色。  选择“我的角色”，然后从“活动角色分配”分组中选择已完成的角色并选择“停用”。  

## <a name="cancel-a-pending-request"></a>取消挂起的请求
如果不需要激活需要批准的角色，可随时取消挂起的请求。 仅需在 Azure AD Privileged Identity Management 应用程序左侧导航栏中选择“我的角色”导航选项即可。

1. 登录到 [Azure 门户](https://portal.azure.com/)，选择“Azure AD Privileged Identity Management”磁贴。
2. 选择“我的角色”。 该页顶部的“分组”中将显示分配给你的合格角色列表。
3. 选择角色。
4. 在“角色激活详细信息”边栏选项卡上，选择“激活尚待审批”横幅。
5. 在“挂起的审批”边栏选项卡顶部选择“取消”。

   ![取消挂起的请求屏幕截图](./media/pim-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png)

## <a name="next-steps"></a>后续步骤
如果有兴趣进一步了解 Azure AD Privileged Identity Management，以下链接提供了详细信息。

[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
