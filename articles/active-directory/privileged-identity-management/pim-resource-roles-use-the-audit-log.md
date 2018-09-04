---
title: 在 PIM 中查看 Azure 资源角色的审核历史记录 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中查看 Azure 资源角色的审核历史记录。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c0536423e9640f78149b612ec66b0a07cdcf24bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189305"
---
# <a name="view-audit-history-for-azure-resource-roles-in-pim"></a>在 PIM 中查看 Azure 资源角色的审核历史记录

资源审核提供资源的所有角色活动的视图。 可以使用预定义日期或自定义范围筛选信息。
![筛选信息](media/azure-pim-resource-rbac/rbac-resource-audit.png)

资源审核还提供对用户活动详细信息的快速访问。 选择“审核类型”下的“激活”。 选择“(活动)”查看用户在 Azure 资源中的操作。
![活动详细信息](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![更多活动详细信息](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="my-audit"></a>我的审核

“我的审核”提供用户的个人角色活动的视图。 可以使用预定义日期或自定义范围筛选信息。
![个人角色活动](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>查看激活和 Azure 资源活动

若要查看特定用户在各种资源中执行的操作，可以查看与给定激活时段关联的 Azure 资源活动。 首先，从“成员”视图或特定角色成员列表中选择用户。 这会生成按日期显示用户在 Azure 资源中操作的图形视图。 其中还显示了同一时间段内的最近角色激活。

![用户详细信息](media/azure-pim-resource-rbac/rbac-user-details.png)

选择特定的角色激活会显示角色激活详细信息，以及在用户处于活动状态期间发生的相应 Azure 资源活动。

![选择角色激活](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中查看 Azure AD 目录角色的审核历史记录](pim-how-to-use-audit-log.md)
