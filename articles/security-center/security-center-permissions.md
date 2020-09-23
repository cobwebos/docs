---
title: Azure 安全中心的权限 | Microsoft Docs
description: 本文介绍 Azure 安全中心如何使用基于角色的访问控制将权限分配给用户，并辨别每个角色允许的操作。
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: memildin
ms.openlocfilehash: ed73c4781b9fd9926e12910ca1eb2f71d2b99245
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904819"
---
# <a name="permissions-in-azure-security-center"></a>Azure 安全中心的权限

Azure 安全中心使用 azure [RBAC)  (azure 基于角色的访问控制 ](../role-based-access-control/role-assignments-portal.md)，它提供可分配给 Azure 中的用户、组和服务的 [内置角色](../role-based-access-control/built-in-roles.md) 。

安全中心会评估资源的配置以识别安全问题和漏洞。 如果分配有资源所属的订阅或资源组的“所有者”、“参与者”或“读取者”角色，则仅可在安全中心看到与资源相关的信息。

除这些角色外，还有两个特定的安全中心角色：

* **安全读取者**：属于此角色的用户对安全中心具有查看权限。 该用户可查看建议、警报、安全策略和安全状态，但不能更改。
* **安全管理员**：属于此角色的用户具有与安全读取者相同的权限，此外，还可以更新安全策略、关闭警报和建议。

> [!NOTE]
> 安全角色（安全读取者和安全管理员）只能访问安全中心。 安全角色无权访问存储、Web 和移动或物联网等其他 Azure 服务区域。
>

## <a name="roles-and-allowed-actions"></a>角色和允许的操作

下表显示安全中心的角色和允许的操作。

|操作|安全读取器/ <br> 读取器 |安全管理员  |资源组参与者/ <br> 资源组所有者  |订阅参与者  |订阅所有者  |
|:--- |:---:|:---:|:---:|:---:|:---:|
|编辑安全策略|-|✔|-|-|✔|
|添加/分配计划 (包括) 法规符合性标准) |-|-|-|-|✔|
|启用/禁用 Azure Defender|-|✔|-|-|✔|
|启用/禁用自动预配|-|✔|-|✔|✔|
|应用资源的安全建议</br>  (和使用 [快速修复！](security-center-remediate-recommendations.md#quick-fix-remediation)) |-|-|✔|✔|✔|
|消除警报|-|✔|-|✔|✔|
|查看警报和建议|✔|✔|✔|✔|✔|

> [!NOTE]
> 对于需要完成任务的用户，建议尽可能为其分配权限最小的角色。 例如，将“读者”角色分配到只需查看有关资源的安全运行状况而不执行操作（例如应用建议或编辑策略）的用户。
>
>

## <a name="next-steps"></a>后续步骤
本文介绍安全中心如何使用 RBAC 将权限分配给用户，并辨别每个角色允许的操作。 现在，已熟悉监视订阅安全状态所需的角色分配，请编辑安全策略，并应用建议，了解如何：

- [在安全中心设置安全策略](tutorial-security-policy.md)
- [管理安全中心的安全建议](security-center-recommendations.md)
- [监视 Azure 资源的安全运行状况](security-center-monitoring.md)
- [管理和响应安全中心的安全警报](security-center-managing-and-responding-alerts.md)
- [监视合作伙伴安全解决方案](security-center-partner-solutions.md)
