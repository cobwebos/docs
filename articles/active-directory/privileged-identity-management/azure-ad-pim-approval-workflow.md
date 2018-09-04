---
title: 在 PIM 中批准或拒绝 Azure AD 目录角色的请求 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中批准或拒绝 Azure AD 目录角色的请求。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189152"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>在 PIM 中批准或拒绝 Azure AD 目录角色的请求

利用 Privileged Identity Management，可以将角色配置为需要审批才可激活，还可选择一个或多个用户或组作为委派的审批者。

## <a name="view-pending-approvals-requests"></a>查看挂起的审批（请求）

有请求等待审批时，委派的审批者将收到电子邮件通知。 若要在 PIM 门户中查看这些请求，请从左侧导航栏的仪表板（在新导航窗格中）中选择“挂起的审批请求”选项卡。

![](media/azure-ad-pim-approval-workflow/image023.png)

将在这里看到待审批的请求列表：

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>批准或拒绝角色提升请求（单个和/或批量）

选择想要批准或拒绝的请求，并在“操作”栏中单击与所做决策对应的按钮：

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>提供批准/拒绝的理由

这会打开新边栏选项卡，以便一次性批准或拒绝多个请求。 输入决策理由，并在底部或边栏选项卡上单击“批准”或“拒绝”：

![](media/azure-ad-pim-approval-workflow/image029.png)

完成请求过程后，状态符号将反映所做的决策（在此示例中，决策是批准）：

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
- [PIM 中的电子邮件通知](pim-email-notifications.md)
