---
title: 在 PIM 的 Azure Active Directory 中查看的 Azure AD 角色审核历史记录 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中查看 Azure AD 角色审核历史记录。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8061cff8d39db66cb22a5650c7688657aa8b3554
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053931"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>在 PIM 中的 Azure AD 角色查看审核历史记录

可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 审核历史记录以查看所有特权角色在过去 30 天内的所有角色分配和激活。 如果你想要查看完整的审核历史记录的目录，包括管理员、 最终用户和同步活动中的活动可以使用[Azure Active Directory 的安全和活动报告](../reports-monitoring/overview-reports.md)。

## <a name="view-audit-history"></a>查看审核历史记录

按照以下步骤，若要查看 Azure AD 角色审核历史记录。

1. 使用[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色成员用户的身份登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 

1. 单击“Azure AD 角色”。 

1. 单击**目录角色审核历史记录**。

    具体取决于你审核历史记录中，柱形图显示以及激活总数、 最大激活数每日和每日平均激活。

    ![目录角色审核历史记录](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    在页面的底部，并介绍了可用的审核历史记录中每个操作显示的表。 列具有以下含义：

    | 列 | 描述 |
    | --- | --- |
    | Time | 当发生此操作。 |
    | 请求者 | 请求角色激活或更改的用户。 如果值为**Azure 系统**，检查的详细信息的 Azure 审核历史记录。 |
    | 操作 | 请求者所采取的操作。 操作可以包括分配、 取消分配、 激活、 停用或 AddedOutsidePIM。 |
    | 成员 | 正在激活或分配给角色的用户。 |
    | 角色 | 分配或由用户激活角色。 |
    | 推论 | 在激活期间向原因字段输入的文本。 |
    | 过期时间 | 激活的角色时过期。 仅适用于符合条件的角色分配。 |

1. 若要对审核历史记录进行排序，请单击**时间**，**操作**，并**角色**按钮。

## <a name="filter-audit-history"></a>筛选审核历史记录

1. 在审核历史记录页的顶部，单击**筛选器**按钮。

    **更新图表参数**窗格会显示。

1. 在中**时间范围**，选择时间范围。

1. 在中**角色**，添加你想要查看的角色的复选标记。

    ![更新图表参数窗格](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. 单击**完成**若要查看已筛选的审核历史记录。

## <a name="next-steps"></a>后续步骤

- [在 PIM 中查看 Azure 资源角色的活动和审核历史记录](azure-pim-resource-rbac.md)
