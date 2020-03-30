---
title: 完成 PIM 中 Azure 资源角色的访问审查 - Azure AD |微软文档
description: 了解如何在 Azure 活动目录中完成对 Azure 资源角色特权标识管理的访问审查。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021984"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>完成特权标识管理中 Azure 资源角色的访问审核

特权角色管理员可以在[开始访问审查](pim-resource-roles-start-access-review.md)后查看特权访问权限。 Azure 活动目录 （Azure AD） 中的特权标识管理 （PIM） 会自动发送电子邮件，提示用户查看其访问权限。 如果用户未收到电子邮件，可以向他们发送[如何执行访问评审](pim-resource-roles-perform-access-review.md)的相关说明。

访问评审期限结束，或者所有用户已完成其自评审后，请按照本文中的步骤管理评审并查看结果。

## <a name="manage-access-reviews"></a>管理访问评审

1. 转到[Azure 门户](https://portal.azure.com/)。 在仪表板上，选择**Azure 资源**服务。

2. 选择你的资源。

3. 选择仪表板的“访问审阅”**** 部分。

    ![Azure 资源 - 访问评审列表，其中显示角色、所有者、开始日期、结束日期和状态](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. 选择要管理的访问审阅。

在访问审核的详细信息页上，有许多选项可用于管理该审核。 选项如下：

![用于管理评审的选项 -“停止”、“重置”、“应用”、“删除”](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>停止

所有访问审核都有结束日期。 选择 **"停止"** 以提前完成它。 任何此时尚未完成审核的用户在停止审核后将无法完成审核。 停止后，无法重新开始审阅。

### <a name="reset"></a>重置

可重置访问评审来删除对其所做的所有决策。 重置访问审核后，所有用户将标记为不再审核。

### <a name="apply"></a>应用

访问审核完成后，选择 **"应用"** 以实施审核结果。 如果在评审中拒绝了用户的访问，则此步骤会删除其角色分配。  

### <a name="delete"></a>删除

如果不再想了解审阅，可将其删除。 选择 **"删除**"从特权身份管理服务中删除审核。

## <a name="results"></a>结果

在“结果”页上查看和下载评审结果列表****。

![“结果”页，其中列出用户、结果、原因、评审者、应用者和应用结果](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>审阅者

查看现有访问审阅的审阅者以及为其添加审阅者。 提醒审阅者完成其审阅。

![“评审者”页，其中列出名称和用户主体名称](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>后续步骤

- [在特权标识管理中启动 Azure 资源角色的访问审查](pim-resource-roles-start-access-review.md)
- [在特权标识管理中对 Azure 资源角色执行访问审查](pim-resource-roles-perform-access-review.md)
