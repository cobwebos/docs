---
title: 在 PIM 中查看 Azure AD 角色的审核报告-Azure AD |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中查看 Azure AD 角色的审核历史记录。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4a2eccc02d13bf5a2dfc8bf3ceb7887e4962489
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498493"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>在 PIM 中查看 Azure AD 角色的审核历史记录

你可以使用 Privileged Identity Management （PIM）审核历史记录查看过去30天内所有特权角色的所有角色分配和激活。 若要查看 Azure Active Directory （Azure AD）组织中活动的完整审核历史记录（包括管理员、最终用户和同步活动），可以使用[Azure Active Directory 安全和活动报告](../reports-monitoring/overview-reports.md)。

## <a name="determine-your-version-of-pim"></a>确定 PIM 版本

从2019年11月开始，Privileged Identity Management 的 Azure AD 角色部分更新为与 Azure 资源角色体验相匹配的新版本。 这将创建其他功能以及对[现有 API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本时，本文中所遵循的过程取决于当前所用 Privileged Identity Management 的版本。 按照本部分中的步骤确定 Privileged Identity Management 的版本。 了解 Privileged Identity Management 版本后，可以在本文中选择与该版本匹配的过程。

1. 使用 "[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)" 角色的用户登录到[Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。 如果在 "概述" 页顶部有一个横幅，请按照本文的 "**新版本**" 选项卡中的说明进行操作。 否则，请按照 "以前的**版本**" 选项卡中的说明进行操作。

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新版本](#tab/new)

按照以下步骤查看 Azure AD 角色的审核历史记录。

## <a name="view-resource-audit-history"></a>查看资源审核历史记录

资源审核可让你查看与 Azure AD 角色关联的所有活动。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择**Azure AD 角色**。

1. 选择 "**资源审核**"。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    ![包含筛选器的资源审核列表](media/pim-how-to-use-audit-log/resource-audit.png)

## <a name="view-my-audit"></a>查看我的审核

使用“我的审核”，可以查看你的个人角色活动。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择**Azure AD 角色**。

1. 选择要查看其审核历史记录的资源。

1. 选择**我的审核**。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    ![当前用户的审核列表](media/pim-how-to-use-audit-log/audit-time-span.png)

# <a name="previous-version"></a>[先前版本](#tab/previous)

## <a name="view-audit-history"></a>查看审核历史记录

按照以下步骤查看 Azure AD 角色的审核历史记录。

1. 使用[特权角色管理员](https://portal.azure.com/)角色成员用户的身份登录到 [Azure 门户](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择**Azure AD 角色**。

1. 选择 "**目录角色" "审核历史记录**"。

    根据您的审核历史记录，将显示一个柱形图以及每日激活总数、每日最大激活次数以及每天激活的平均次数。

    ![目录角色审核历史记录](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    在页面的底部，会显示一个表，其中包含有关可用审核历史记录中每个操作的信息。 列具有以下含义：

    | 列 | 说明 |
    | --- | --- |
    | 时间 | 操作发生时。 |
    | 者 | 请求角色激活或更改的用户。 如果值为**Azure 系统**，请查看 azure 审核历史记录以了解详细信息。 |
    | 操作 | 请求程序执行的操作。 操作可包括分配、取消分配、激活、停用或 AddedOutsidePIM。 |
    | 成员 | 激活或分配给角色的用户。 |
    | 角色 | 用户分配或激活的角色。 |
    | 背后 | 激活过程中在 "原因" 字段中输入的文本。 |
    | 过期时间 | 当激活的角色过期时。 仅适用于符合条件的角色分配。 |

1. 若要对审核历史记录进行排序，请单击 "**时间**"、"**操作**" 和 "**角色**" 按钮。

## <a name="filter-audit-history"></a>筛选审核历史记录

1. 在 "审核历史记录" 页的顶部，单击 "**筛选器**" 按钮。

    此时将显示 "**更新图表参数**" 窗格。

1. 在 "**时间范围**" 中，选择一个时间范围。

1. 在 "**角色**" 中，选中相应的复选框以指示要查看的角色。

    ![更新图表参数窗格](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. 选择 "**完成**" 以查看筛选的审核历史记录。

---

## <a name="next-steps"></a>后续步骤

- [查看 Privileged Identity Management 中的 Azure 资源角色的活动和审核历史记录](azure-pim-resource-rbac.md)
