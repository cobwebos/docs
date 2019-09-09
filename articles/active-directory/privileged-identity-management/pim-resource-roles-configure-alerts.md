---
title: 在 PIM 中为 Azure 资源角色配置安全警报 - Azure Active Directory | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中为 Azure 资源角色配置安全警报。
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
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 177f61392c3e441c891ba1b531301b3dae8c0db2
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804233"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>在 PIM 中为 Azure 资源角色配置安全警报
如果环境中有可疑活动或不安全的活动，Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 将生成警报。 当警报触发时，它将显示在“警报”页上。 

![“Azure 资源 - 警报”页，其中列出警报、风险级别和计数](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>查看警报
选择某个警报可查看报告，其中列出了触发该警报的用户或角色以及修正建议。

![警报报告，其中显示上次扫描时间、说明、缓解步骤、类型、严重性、安全影响以及如何防止下次触发警报](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>警报
| 警报 | 严重性 | 触发器 | 建议 |
| --- | --- | --- | --- |
| **向资源分配的所有者过多** |中型 |拥有所有者角色的用户过多。 |查看列表中的用户，并将某些用户重新分配到具有较低权限的角色。 |
| **向资源分配的永久所有者过多** |中型 |太多用户永久地分配到了某个角色。 |查看列表中的用户，并将某些用户重新分配为需要激活才能使用该角色。 |
| **创建了重复角色** |中型 |多个角色具有相同的条件。 |仅使用这些角色中的一个角色。 |


### <a name="severity"></a>Severity
* **高**：因策略违反需要立即采取措施。 
* **中**：不需要立即采取措施但指示潜在的策略违反。
* **低**：不需要立即采取措施，但建议考虑首选的策略更改。

## <a name="configure-security-alert-settings"></a>配置安全警报设置
从“警报”页转到“设置”。

![突出显示了“设置”的“警报”页](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

在各个警报上自定义设置以适应你的环境和安全目标。

![警报的“设置”页，用于启用和配置设置](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
