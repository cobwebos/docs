---
title: 在 PIM 中完成对 Azure 资源角色的访问评审 - Azure AD | Microsoft Docs
description: 了解如何在 Azure Active Directory 的 Privileged Identity Management 中完成 Azure 资源角色的访问评审。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1601c9fc750df1f8e18bdaa072ede1b5ee164329
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743807"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中完成对 Azure 资源角色的访问评审

[启动访问评审](pim-resource-roles-start-access-review.md)后，特权角色管理员可以评审特权访问。 Azure Active Directory (Azure AD) 中的 Privileged Identity Management (PIM) 会自动发送一封提示用户评审其访问权限的电子邮件。 如果用户未收到电子邮件，可以向他们发送[如何执行访问评审](pim-resource-roles-perform-access-review.md)的相关说明。

访问评审期限结束，或者所有用户已完成其自评审后，请按照本文中的步骤管理评审并查看结果。

## <a name="manage-access-reviews"></a>管理访问评审

1. 转到 [Azure 门户](https://portal.azure.com/)。 在仪表板中，选择“Azure 资源”服务  。

2. 选择你的资源。

3. 选择仪表板的“访问审阅”  部分。

    ![Azure 资源 - 访问评审列表，其中显示角色、所有者、开始日期、结束日期和状态](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. 选择要管理的访问审阅。

在访问评审的详细信息页上，有大量用于管理该评审的选项。 选项如下：

![用于管理评审的选项 -“停止”、“重置”、“应用”、“删除”](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>停止

所有访问评审都有一个结束日期。 选择“停止”  即可提前完成。 若用户此时尚未完成审阅，则在停止审阅后，用户无法完成审阅。 停止后，无法重新开始审阅。

### <a name="reset"></a>重置

可重置访问评审来删除对其所做的所有决策。 重置访问评审后，所有用户都将被重新标记为未审阅。

### <a name="apply"></a>应用

完成访问评审后，选择“应用”来实现评审结果  。 如果在评审中拒绝了用户的访问，则此步骤会删除其角色分配。  

### <a name="delete"></a>删除

如果不再想了解审阅，可将其删除。 选择“删除”  即可从 Privileged Identity Management 服务中删除评审。

## <a name="results"></a>结果

在“结果”页上查看和下载评审结果列表  。

![“结果”页，其中列出用户、结果、原因、评审者、应用者和应用结果](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>审阅者

查看现有访问审阅的审阅者以及为其添加审阅者。 提醒审阅者完成其审阅。

![“评审者”页，其中列出名称和用户主体名称](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中开始对 Azure 资源角色的访问评审](pim-resource-roles-start-access-review.md)
- [在 Privileged Identity Management 中执行对我的 Azure 资源角色的访问评审](pim-resource-roles-perform-access-review.md)
