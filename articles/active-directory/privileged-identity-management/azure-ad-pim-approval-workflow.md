---
title: "Azure Privileged Identity Management 审批工作流 | Microsoft Docs"
description: "了解 Privileged Identity Management (PIM) 审批工作流"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: cf6a9213fa0a1cba8725aabb42abe51b805ece7a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="approvals-preview"></a>审批（预览版）

## <a name="overview"></a>概述

利用 Privileged Identity Management 的审批，可以将角色配置为需要审批才可激活，还可选择一个或多个用户或组作为委派的审批者。 继续阅读，了解如何配置角色和选择审批者。

>[!NOTE]
请注意，此功能仍处于开发阶段，可能会遇到 bug。 此功能（包括文本和命名约定）可能有所更改，不应将其视为最终版本。


## <a name="key-terminology"></a>关键术语

有资格的角色用户 – 有资格的角色用户是指组织内已分配到 Azure AD 角色的有资格用户（角色需要激活）。

委派的审批者 – 委派的审批者是 Azure AD 内负责审批角色激活请求的一个或多个用户或组。

## <a name="scenarios"></a>方案

个人预览版支持以下方案：

特权角色管理员 (PRA) 可以：

-   [启用对特定角色的审批](#enable-approval-for-specific-roles)

-   [指定要审批请求的审批者用户和/或组](#specify-approver-users-and/or-groups-to-approve-requests)

-   [查看所有特权角色的请求和审批历史记录](#view-request-and-approval-history-for-all-privileged-roles)

指定的审批者可以：

-   [查看挂起的审批（请求）](#view-pending-approvals-requests)

-   [批准或拒绝角色提升请求（单个和/或批量）](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [提供批准/拒绝的理由](#provide-justification-for-my-approval/rejection) 

有资格的用户角色可以：

-   [请求激活需要审批的角色](#request-activation-of-a-role-that-requires-approval)

-   [查看要激活的请求的状态](#view-the-status-of-your-request-to-activate)

-   [批准激活后，在 Azure AD 中完成任务](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>导航

我们已更新导航以支持审批

![](media/azure-ad-pim-approval-workflow/image001.png)

利用默认登录页，可以方便地访问有关 PIM 和新审批者文档的信息。

![](media/azure-ad-pim-approval-workflow/image002.png)

此外，我们还为 PIM 的所有用户添加了一个新的部分“我的审核历史记录”。 可在此处找到与用户标识相关的所有信息。 在一个便利的位置提供所有挂起的和已完成的请求、对处理的请求所做的任何决策，以及以往的所有角色激活。

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>启用对特定角色的审批

若要启用对特定角色的审批，首先从左侧导航区域中选择目录角色。

![](media/azure-ad-pim-approval-workflow/image004.png)

在目录角色左侧导航区域中查找和选择设置

![](media/azure-ad-pim-approval-workflow/image006.png)

选择特权角色：

![](media/azure-ad-pim-approval-workflow/image009.png)

在“要求审批”部分中选择“启用”：

![](media/azure-ad-pim-approval-workflow/image011.png)

启用后，边栏选项卡将展开，显示以下详细信息：

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
如果未指定任何审批者，PRA 将成为默认审批者。 审批此角色的所有激活请求时，需要 PRA。

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>指定要审批请求的审批者用户和/或组

若要委托审批，请单击“选择审批者”选项：

![](media/azure-ad-pim-approval-workflow/image015.png)

当加载时选择的审批者边栏选项卡时，你可以搜索特定用户或组使用的搜索栏顶部，或从预填充列表中，选择，然后单击"选择"完成后：

![](media/azure-ad-pim-approval-workflow/image017.png)

注意：可以一次性选择多个用户或组。

选择的内容会显示在所选审批者列表中，如下所示：

![](media/azure-ad-pim-approval-workflow/image019.png)

若要删除审批者，只需单击其姓名旁边的“删除”按钮。

若要添加其他审批者，请重复执行该过程。

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>查看所有特权角色的请求和审批历史记录

若要查看所有特权角色的请求和审批历史记录，请从仪表板中选择“审核历史记录”：

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
可以按操作对数据排序，并查找“已批准的激活”

### <a name="view-pending-approvals-requests"></a>查看挂起的审批（请求）

有请求等待审批时，委派的审批者将收到电子邮件通知。 若要在 PIM 门户中查看这些请求，请从左侧导航栏的仪表板（在新导航窗格中）中选择“挂起的审批请求”选项卡。

![](media/azure-ad-pim-approval-workflow/image023.png)

将在这里看到待审批的请求列表：

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>批准或拒绝角色提升请求（单个和/或批量）

选择想要批准或拒绝的请求，并在“操作”栏中单击与所做决策对应的按钮：

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>提供批准/拒绝的理由

这会打开新边栏选项卡，以便一次性批准或拒绝多个请求。 输入决策理由，并在底部或边栏选项卡上单击“批准”或“拒绝”：

![](media/azure-ad-pim-approval-workflow/image029.png)

完成请求过程后，状态符号将反映所做的决策（在此示例中，决策是批准）：

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>请求激活需要审批的角色

从旧版 PIM 导航或新版导航中均可发起要求审批的角色激活请求，因为角色激活的过程保持不变。 只需从要激活的角色列表中选择一个角色：

![](media/azure-ad-pim-approval-workflow/image033.png)

如果特权角色要求进行多重身份验证，系统会提示首先完成该任务：

![](media/azure-ad-pim-approval-workflow/image035.png)

完成后，单击“激活”并提供理由（如果需要）：

![](media/azure-ad-pim-approval-workflow/image037.png)

请求者将看到请求正在等待审批的通知：

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>查看要激活的请求的状态

只有使用新版导航才可查看要激活的挂起请求的状态。 从左侧导航栏中，选择“我的请求”选项卡：

![](media/azure-ad-pim-approval-workflow/image041.png)

请求状态默认为“挂起”，但可以进行切换以查看所有请求或已拒绝的请求。

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>批准激活后，在 Azure AD 中完成任务

请求一经审批，角色就会处于活动状态，用户就可以继续进行需要此角色的任何工作。

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>后续步骤

反馈对我们非常重要。 请随意在此处与我们分享评论或反馈！
