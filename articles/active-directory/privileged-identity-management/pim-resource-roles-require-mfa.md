---
title: Azure 资源的 Privileged Identity Management - MFA | Microsoft Docs
description: 本文档介绍了如何为 PIM 资源启用多重身份验证。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management - 资源角色 - MFA

Azure 资源的 PIM 中的角色使资源管理员和标识管理员能够通过有时限的成员身份和实时访问来保护重要的 Azure 基础结构。 此外，PIM 针对两种不同的方案提供了可选的 Azure 多重身份验证 (MFA) 执行。

## <a name="require-mfa-to-activate"></a>要求通过 MFA 进行激活

资源管理员可以要求角色的合格成员成功完成 Azure MFA 才能激活。 此过程能够以合理的确定性确保请求激活的用户是其声称的人员。 强制设施此选项可以在用户帐户可能已遭受危害的情况下保护重要资源。 

若要强制实施此要求，请从托管资源列表中选择一个资源。 从[概述仪表板](pim-resource-roles-overview-dashboards.md)中，从屏幕右下角的角色列表中选择一个角色。

此外，还可以从左侧导航菜单中的“角色”或“角色设置”选项卡来访问角色设置。

>[!Note]
>如果左侧导航菜单中的选项灰显，并且页面顶部的横幅指出“你有可以激活的合格角色”，则表明你不是活动管理员，并且必须[激活](pim-resource-roles-activate-your-roles.md)才能继续。

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

如果要查看角色的成员身份，请从屏幕顶部的栏中选择“角色设置”以打开“角色设置详细信息”。

单击顶部的“编辑”按钮可修改角色设置。

在“激活”下的部分中，单击“要求通过多重身份验证进行激活”复选框，然后单击“保存”。

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>要求在分配时进行 MFA

在某些情况下，资源管理员可能希望将成员短期分配到角色（例如一天），并且不需要所分配的成员来请求激活。 在这种情况下，PIM 无法在成员使用其角色分配时强制执行 MFA，因为成员从分配之时起已在角色中处于活动状态。

为确保履行分配的资源管理员是其声称的人员，可以在分配时强制执行 MFA。

从同一“角色设置详细信息”屏幕上，选中“要求在分配时进行多重身份验证”复选框。

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>后续步骤

[需要批准才能激活](pim-resource-roles-approval-workflow.md)

[使用审核日志](pim-resource-roles-use-the-audit-log.md)



