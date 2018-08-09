---
title: 使用 Privileged Identity Management 对 Azure 资源审核资源角色 | Microsoft Docs
description: 说明如何获取给定资源的所有角色活动的视图。
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
ms.openlocfilehash: 8f328a609d696886ed452589d3cdfb5f45aec62a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621277"
---
# <a name="audit-resource-roles-for-azure-resources-by-using-privileged-identity-management"></a>使用 Privileged Identity Management 对 Azure 资源审核资源角色 

资源审核提供资源的所有角色活动的视图。 可以使用预定义日期或自定义范围筛选信息。
![筛选信息](media/azure-pim-resource-rbac/rbac-resource-audit.png)

资源审核还提供对用户活动详细信息的快速访问。 选择“审核类型”下的“激活”。 选择“(活动)”查看用户在 Azure 资源中的操作。
![活动详细信息](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![更多活动详细信息](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>我的审核

“我的审核”提供用户的个人角色活动的视图。 可以使用预定义日期或自定义范围筛选信息。
![个人角色活动](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>查看激活和 Azure 资源活动

若要查看特定用户在各种资源中执行的操作，可以查看与给定激活时段关联的 Azure 资源活动。 首先，从“成员”视图或特定角色成员列表中选择用户。 这会生成按日期显示用户在 Azure 资源中操作的图形视图。 其中还显示了同一时间段内的最近角色激活。

![用户详细信息](media/azure-pim-resource-rbac/rbac-user-details.png)

选择特定的角色激活会显示角色激活详细信息，以及在用户处于活动状态期间发生的相应 Azure 资源活动。

![选择角色激活](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

