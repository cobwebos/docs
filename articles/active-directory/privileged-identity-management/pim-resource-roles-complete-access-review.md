---
title: 在 Privileged Identity Management-Azure Active Directory | 中完成对 Azure 资源角色的访问评审Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中完成对 Azure 资源角色的访问评审。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6761d1d6aea796e0cca708676f69c47328642cc2
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895816"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中完成 Azure 资源角色的访问评审

特权角色管理员在[开始访问评审](pim-resource-roles-start-access-review.md)后，可以查看特权访问权限。 Azure Active Directory （Azure AD）中的 Privileged Identity Management （PIM）会自动发送一封提示用户查看其访问权限的电子邮件。 如果用户未收到电子邮件，可以向他们发送[如何执行访问评审](pim-resource-roles-perform-access-review.md)的相关说明。

访问评审期限结束，或者所有用户已完成其自评审后，请按照本文中的步骤管理评审并查看结果。

## <a name="manage-access-reviews"></a>管理访问评审

1. 转到 [Azure 门户](https://portal.azure.com/)。 在仪表板上，选择 " **Azure 资源**" 服务。

2. 选择你的资源。

3. 选择仪表板的“访问审阅”部分。

    ![Azure 资源-访问评审列表，显示角色、所有者、开始日期、结束日期和状态](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. 选择要管理的访问审阅。

在访问评审的详细信息页上，有许多选项可用于管理该评审。 选项如下：

![用于管理评审的选项-停止、重置、应用、删除](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>停止

所有访问评审都有一个结束日期。 选择 "**停止**" 以提前完成此操作。 尚未完成评审的任何用户将无法在你停止审阅之后完成此操作。 停止后，无法重新开始审阅。

### <a name="reset"></a>重置

可重置访问评审来删除对其所做的所有决策。 重置访问评审后，所有用户都将被重新标记为未审阅。

### <a name="apply"></a>应用

访问评审完成后，选择 "**应用**" 以实现评审的结果。 如果在评审中拒绝了用户的访问，则此步骤会删除其角色分配。  

### <a name="delete"></a>删除

如果不再想了解审阅，可将其删除。 选择 "**删除**"，从 Privileged Identity Management 服务中删除评审。

## <a name="results"></a>结果

在 "**结果**" 页上，查看并下载查看结果的列表。

!["结果" 页，其中列出了用户、结果、原因、审核者、应用结果](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>审阅者

查看现有访问审阅的审阅者以及为其添加审阅者。 提醒审阅者完成其审阅。

!["审阅者" 页列出名称和用户主体名称](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中启动 Azure 资源角色的访问评审](pim-resource-roles-start-access-review.md)
- [在 Privileged Identity Management 中执行 Azure 资源角色的访问评审](pim-resource-roles-perform-access-review.md)
