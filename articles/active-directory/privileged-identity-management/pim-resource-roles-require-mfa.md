---
title: 使用 Privileged Identity Management 在 Azure 资源中强制执行 Azure 多重身份验证 | Microsoft Docs
description: 本文档介绍了如何为 PIM 资源启用多重身份验证。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 42c93a107c7ed58d6f7412b61627dccaf9076bb7
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617864"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>使用 Privileged Identity Management 在 Azure 资源中强制执行 Azure 多重身份验证

借助面向 Azure 资源角色的 Privileged Identity Management (PIM)，资源管理员和标识管理员可以通过有时限的成员身份和实时访问权限来保护关键 Azure 基础结构。 此外，PIM 还提供了两种不同的可选 Azure 多重身份验证强制执行方案。

## <a name="require-multi-factor-authentication-to-activate"></a>要求必须使用多重身份验证才能激活

资源管理员可以要求角色的符合条件成员，必须运行 Azure 多重身份验证才能激活。 此过程可确保请求激活的用户一定就是其本人。 强制执行此选项可以在用户帐户可能已遭入侵的情况下保护关键资源。 

若要强制实施此要求，请从托管资源列表中选择一个资源。 在[“概述”仪表板](pim-resource-roles-overview-dashboards.md)中，从屏幕右下角的角色列表中选择角色。

此外，还可以从左侧窗格的“角色”或“角色设置”选项卡来访问角色设置。

>[!Note]
>如果左侧窗格中的选项灰显，并且页面顶部的横幅指出“有符合条件的角色可供激活”，表明你作为管理员不处于活动状态。 也就是说，必须[激活](pim-resource-roles-activate-your-roles.md)才能继续。

![“角色”和“角色设置”选项卡 ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

若要查看角色的成员身份，请选择屏幕顶部栏中的“角色设置”，以打开“角色设置详细信息”。

若要修改角色设置，请选择顶部的“编辑”按钮。

在“激活”下面的部分中，选中“要求在激活时进行多重身份验证”复选框。 再选择“保存”。

![要求在激活时进行多重身份验证](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>要求在分配时进行多重身份验证

在某些情况下，资源管理员可能希望为成员分配短期（例如，一天）角色。 在这种情况下，分配的一个或多个成员无需请求激活。 这样一来，PIM 无法在成员使用角色分配时强制执行多重身份验证，因为成员从分配到角色之时起就已处于活动状态。

为确保负责分配角色的资源管理员是其本人，可以在分配时强制执行多重身份验证。

在相同的“角色设置详细信息”屏幕上，选中“要求在直接分配时进行多重身份验证”复选框。

![要求在直接分配时进行多重身份验证](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>后续步骤

[需要批准才能激活](pim-resource-roles-approval-workflow.md)

[使用审核日志](pim-resource-roles-use-the-audit-log.md)



