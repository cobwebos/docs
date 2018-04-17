---
title: Azure 资源的 Privileged Identity Management - 安全警报 | Microsoft Docs
description: 介绍了 PIM 安全警报。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 86c9a0f12b2598ffbd02810a11622b13b0363a1f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---alerts"></a>Privileged Identity Management - 资源角色 - 警报
当环境中有可疑活动或不安全活动时，Azure 资源的 PIM 将生成警报。 当警报触发时，它将显示在“警报”页上。 

![](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>查看警报
选择某个警报可查看报告，其中列出了触发该警报的用户或角色以及修正建议。
![](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>警报
| 警报 | 严重性 | 触发者 | 建议 |
| --- | --- | --- | --- |
| **向资源分配的所有者过多** |中 |过多的用户具有“所有者”角色。 |查看列表中的用户，并将某些用户重新分配到具有较低权限的角色。 |
| **向资源分配的永久所有者过多** |中 |太多用户永久地分配到了某个角色。 |查看列表中的用户，并将某些用户重新分配为需要激活才能使用该角色。 |
| **创建了重复角色** |中 |多个角色具有相同的条件。 |仅使用这些角色中的一个。 |


### <a name="severity"></a>严重性
* **高**：因策略冲突需要立即采取措施。 
* **中**：不需要立即采取措施但有潜在的策略冲突。
* **低**：不需要立即采取措施，但建议考虑可取的策略更改。

## <a name="configure-security-alert-settings"></a>配置安全警报设置
从“警报”页面中，导航到“设置”。
![](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

在各个警报上自定义设置以适应你的环境和安全目标。
![](media/azure-pim-resource-rbac/rbac-alert-settings.png)
