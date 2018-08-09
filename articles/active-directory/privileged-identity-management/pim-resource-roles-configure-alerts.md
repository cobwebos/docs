---
title: 使用 Privileged Identity Management 管理 Azure 资源的安全警报 | Microsoft Docs
description: 介绍了 PIM 安全警报。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 46b19866ec3f276c8337cf9501b779701377d0a5
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620410"
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>使用 Privileged Identity Management 管理 Azure 资源的安全警报
如果环境中有可疑活动或不安全活动，Azure 资源的 Privileged Identity Management (PIM) 会生成警报。 当警报触发时，它将显示在“警报”页上。 

![“警报”页](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>查看警报
选择某个警报可查看报告，其中列出了触发该警报的用户或角色以及修正建议。

![警报报告](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>警报
| 警报 | Severity | 触发器 | 建议 |
| --- | --- | --- | --- |
| **向资源分配的所有者过多** |中型 |拥有“所有者”角色的用户过多。 |查看列表中的用户，并将某些用户重新分配到具有较低权限的角色。 |
| **向资源分配的永久所有者过多** |中型 |太多用户永久地分配到了某个角色。 |查看列表中的用户，并将某些用户重新分配为需要激活才能使用该角色。 |
| **创建了重复角色** |中型 |多个角色具有相同的条件。 |仅使用这些角色中的一个角色。 |


### <a name="severity"></a>Severity
* **高**：因策略冲突需要立即采取措施。 
* **中**：不需要立即采取措施但有潜在的策略冲突。
* **低**：不需要立即采取措施，但建议考虑可取的策略更改。

## <a name="configure-security-alert-settings"></a>配置安全警报设置
从“警报”页转到“设置”。
![设置](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

在各个警报上自定义设置以适应你的环境和安全目标。
![自定义设置](media/azure-pim-resource-rbac/rbac-alert-settings.png)
