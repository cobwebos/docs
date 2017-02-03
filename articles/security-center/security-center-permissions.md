---
title: "Azure 安全中心的权限 | Microsoft Docs"
description: "本文介绍 Azure 安全中心如何使用基于角色的访问控制将权限分配给用户，并辨别每个角色允许的操作。"
services: security-center
cloud: na
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
ms.assetid: 
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 5c4a08d96175d431e0a29dfc5927b64567c40117
ms.openlocfilehash: d1c8a39bbcda7d22fdce08d122098e994ca87451


---

# <a name="permissions-in-azure-security-center"></a>Azure 安全中心的权限

Azure 安全中心使用[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md) 提供可在 Azure 中分配给用户、组和服务的[内置角色](../active-directory/role-based-access-built-in-roles.md)。

安全中心会评估资源的配置以识别安全问题和漏洞。 如果分配有资源所属的订阅或资源组的“所有者”、“参与者”或“读取者”角色，仅可在安全中心看到与资源相关的信息。

## <a name="roles-and-allowed-actions"></a>角色和允许的操作

下表显示安全中心的角色和允许的操作。 X 指示允许该角色执行该操作。

| 角色 | 编辑安全策略 | 应用资源的安全建议 | 修正或消除警报 | 查看订阅中的警报 | 查看特定资源的警报 |
|:--- |:---:|:---:|:---:|:---:|:---:|
| 订阅所有者 | X | X | X | X | X |
| 订阅参与者 | X | X | X | X | X |
| 资源组所有者 | -- | X | -- | -- | X |
| 资源组参与者 | -- | X | -- | -- | X |
| 读取器 | -- | -- | -- | X | X |

> [!NOTE]
> 对于需要完成任务的用户，建议尽可能为其分配权限最小的角色。 例如，将“读者”角色分配到只需查看有关资源的安全运行状况而不执行操作（例如应用建议或编辑策略）的用户。
>
>

## <a name="next-steps"></a>后续步骤
本文介绍安全中心如何使用 RBAC 将权限分配给用户，并辨别每个角色允许的操作。 现在，已熟悉监视订阅安全状态所需的角色分配，请编辑安全策略，然后应用建议，了解如何：

- [在安全中心设置安全策略](security-center-policies.md)
- [管理安全中心的安全建议](security-center-recommendations.md)
- [监视 Azure 资源的安全运行状况](security-center-monitoring.md)
- [管理和响应安全中心的安全警报](security-center-managing-and-responding-alerts.md)
- [监视合作伙伴安全解决方案](security-center-partner-solutions.md)



<!--HONumber=Dec16_HO2-->


