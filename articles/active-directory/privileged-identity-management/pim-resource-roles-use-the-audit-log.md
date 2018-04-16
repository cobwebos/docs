---
title: Azure 资源的 Privileged Identity Management - 资源审核 | Microsoft Docs
description: 说明如何获取给定资源的所有角色活动的视图。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management - 资源角色 - 审核

资源审核提供资源的所有角色活动的视图。 可以使用预定义日期或自定义范围筛选信息。
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

资源审核还提供快速访问方式来查看用户的活动详细信息。 在“审核类型”下，选择“激活”。 单击“(活动)”可查看该用户对 Azure 资源的操作。
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>我的审核

“我的审核”提供用户的个人角色活动的视图。 可以使用预定义日期或自定义范围筛选信息。
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>查看激活和 Azure 资源活动

如果需要查看特定的用户针对各种资源执行了哪些操作，可以查看与给定激活时段关联的 Azure 资源活动（适用于有资格的用户）。 首先，从“成员”视图中或者从充当特定角色的成员列表中选择一个用户。 结果会按日期显示该用户对 Azure 资源执行的操作的图形视图，以及最近在同一时间段内执行的角色激活操作。

![](media/azure-pim-resource-rbac/rbac-user-details.png)

选择特定的角色激活操作会显示角色激活详细信息，以及在该用户处于活动状态时发生的相应 Azure 资源活动。

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

