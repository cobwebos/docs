---
title: 为 Privileged Identity Management-Azure Active Directory 中的 Azure 资源角色配置安全警报 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中为 Azure 资源角色配置安全警报。
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
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023084"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中配置 Azure 资源角色的安全警报

当 Azure Active Directory （Azure AD）组织中存在可疑或不安全的活动时，Privileged Identity Management （PIM）会生成警报。 当警报触发时，它将显示在“警报”页上。

![“Azure 资源 - 警报”页，其中列出警报、风险级别和计数](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>查看警报

选择一个警报，查看列出触发该警报的用户或角色的报表，以及修正指南。

![警报报告，其中显示上次扫描时间、说明、缓解步骤、类型、严重性、安全影响以及如何防止下次触发警报](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>警报

| 警报 | 严重性 | 触发器 | 建议 |
| --- | --- | --- | --- |
| **向资源分配的所有者过多** |中型 |拥有所有者角色的用户过多。 |查看列表中的用户，并将某些用户重新分配到具有较低权限的角色。 |
| **向资源分配的永久所有者过多** |中型 |太多用户永久地分配到了某个角色。 |查看列表中的用户，并将某些用户重新分配为需要激活才能使用该角色。 |
| **创建了重复角色** |中型 |多个角色具有相同的条件。 |仅使用这些角色中的一个角色。 |

### <a name="severity"></a>严重性

- **高**：因策略冲突需要立即采取措施。 
- **中**：不需要立即采取措施但有潜在的策略冲突。
- **低**：不需要立即采取措施，但建议考虑可取的策略更改。

## <a name="configure-security-alert-settings"></a>配置安全警报设置

从“警报”页转到“设置”。

![突出显示了“设置”的“警报”页](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

在各个警报上自定义设置以适应你的环境和安全目标。

![警报的“设置”页，用于启用和配置设置](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
