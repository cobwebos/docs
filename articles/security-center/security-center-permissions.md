---
title: "Azure 安全中心中的权限 |Microsoft 文档"
description: "这篇文章介绍了 Azure 安全中心如何使用基于角色的访问控制可向用户分配权限，并标识每个角色允许的操作。"
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
ms.date: 06/13/2017
ms.author: terrylan
ms.openlocfilehash: 0aaa99dda44d2020afd3e841e84020eb4ff87a85
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="permissions-in-azure-security-center"></a>Azure 安全中心中的权限

Azure 安全中心使用[基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-configure.md)，该属性提供[内置角色](../active-directory/role-based-access-built-in-roles.md)，可分配给用户、 组和 Azure 中的服务。

安全中心评估你的资源来标识安全问题和漏洞的配置。 在安全中心中，你将只能看到时为你分配的订阅或资源所属的资源组的所有者、 参与者或读取器角色与某个资源相关的信息。

除了这些角色中，有两个特定的安全中心角色：

* **安全读取器**： 用户属于此角色具有查看权限的安全中心。 用户可以查看建议、 警报、 安全策略，和安全状态，但不能进行更改。
* **安全管理员**： 属于此角色的用户具有安全读取器与相同的权限和也可以更新的安全策略和停用警报和建议。

> [!NOTE]
> 安全读取器和安全管理员的安全角色具有访问权限仅在安全中心中。 安全角色没有访问 Azure 存储、 Web 和移动或物联网等其他服务区域。
>
>

## <a name="roles-and-allowed-actions"></a>角色和允许的操作

下表显示角色，并允许的安全中心中的操作。 X 指示，该角色的允许操作。

| 角色 | 编辑安全策略 | 应用的资源的安全建议 | 关闭警报和建议 | 查看警报和建议 |
|:--- |:---:|:---:|:---:|:---:|
| 订阅所有者 | X | X | X | X |
| 订阅参与者 | X | X | X | X |
| 资源组所有者 | -- | X | -- | X |
| 资源组参与者 | -- | X | -- | X |
| 读者 | -- | -- | -- | X |
| 安全管理员 | X | -- | X | X |
| 安全读取器 | -- | -- | -- | X |

> [!NOTE]
> 我们建议你分配所需的用户来完成其任务的权限级别最低的角色。 例如，将读取者角色分配给用户只需查看的资源信息的安全性运行状况，但未执行操作，如应用建议或编辑策略。
>
>

## <a name="next-steps"></a>后续步骤
本文所述的安全中心如何使用 RBAC 来将权限分配给用户，并标识每个角色允许的操作。 现在，你熟悉监视你的订阅的安全状态所需的角色分配，编辑安全策略和应用建议，请了解如何：

- [在安全中心中设置安全策略](security-center-policies.md)
- [管理安全中心中的安全建议](security-center-recommendations.md)
- [监视你的 Azure 资源的安全性运行状况](security-center-monitoring.md)
- [管理和响应的安全中心中的安全警报](security-center-managing-and-responding-alerts.md)
- [监视合作伙伴安全解决方案](security-center-partner-solutions.md)
