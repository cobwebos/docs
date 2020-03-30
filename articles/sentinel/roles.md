---
title: Azure 哨兵中的权限 |微软文档
description: 本文介绍 Azure Sentinel 如何使用基于角色的访问控件向用户分配权限，并确定每个角色的允许操作。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587169"
---
# <a name="permissions-in-azure-sentinel"></a>Azure 哨兵中的权限

Azure Sentinel 使用 [基于角色的访问控制 （RBAC）](../role-based-access-control/role-assignments-portal.md)来提供可分配给 Azure 中的用户、组和服务的 [内置角色](../role-based-access-control/built-in-roles.md) 。

使用 RBAC，您可以在安全操作团队中使用和创建角色，以授予对 Azure Sentinel 的适当访问权限。 根据角色，您可以细粒度地控制有权访问 Azure Sentinel 的用户可以看到的内容。 可以直接在 Azure Sentinel 工作区中分配 RBAC 角色，也可以将 RBAC 角色分配给工作区所属的订阅或资源组。

有三个特定的内置 Azure Sentinel 角色。  
**所有 Azure Sentinel 内置角色都授予对 Azure Sentinel 工作区中数据的读取访问权限。**
- [Azure 哨兵读取器](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure 哨兵响应器](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure 哨兵参与者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

除了 Azure Sentinel 专用 RBAC 角色外，还有 Azure 和日志分析 RBAC 角色可以授予更广泛的权限集，其中包括对 Azure Sentinel 工作区和其他资源的访问权限：

- **Azure 角色：**[所有者](../role-based-access-control/built-in-roles.md#owner)、[参与者](../role-based-access-control/built-in-roles.md#contributor)和[读取器](../role-based-access-control/built-in-roles.md#reader)。 Azure 角色授予跨所有 Azure 资源（包括日志分析工作区和 Azure Sentinel 资源）的访问。

-   **日志分析角色：**[日志分析参与者](../role-based-access-control/built-in-roles.md#log-analytics-contributor)，[日志分析阅读器](../role-based-access-control/built-in-roles.md#log-analytics-reader)。 日志分析角色授予跨所有日志分析工作区的访问权限。 

> [!NOTE]
> 日志分析角色还授予所有 Azure 资源的读取访问权限，但只会将写入权限分配给日志分析资源。


例如，使用**Azure Sentinel 读取器读取器**和**Azure 参与者**（不是 Azure **Sentinel 参与者**）角色分配的用户将能够编辑 Azure Sentinel 中的数据，尽管他们只有**Sentinel 读取器**权限。 因此，如果要仅向 Azure Sentinel 中授予 的 权限，则应仔细删除此用户的先前权限，以确保不会破坏其他资源所需的任何权限角色。

> [!NOTE]
>- Azure Sentinel 使用行动手册进行自动威胁响应。 行动手册利用 Azure 逻辑应用，是单独的 Azure 资源。 您可能希望分配安全操作团队的特定成员，并选择使用逻辑应用进行安全编排、自动化和响应 （SOAR） 操作。 您可以使用[逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor)角色或[逻辑应用运算符](../role-based-access-control/built-in-roles.md#logic-app-operator)角色分配使用操作手册的显式权限。
>- 要添加数据连接器，每个连接器的必要角色是每个连接器类型，并列在相关的连接器页中。 此外，为了连接任何数据源，必须在 Azure Sentinel 工作区上具有写入权限。



## <a name="roles-and-allowed-actions"></a>角色和允许的操作

下表在 Azure Sentinel 中显示角色和允许的操作。 X 指示允许该角色执行该操作。

| 角色 | 创建和运行行动手册| 创建和编辑仪表板、分析规则和其他 Azure 哨兵资源 | 管理事件（解雇、分配等） | 查看数据、事件、仪表板和其他 Azure 哨兵资源 |
|--- |---|---|---|---|
| Azure 哨兵读取器 | -- | -- | -- | X |
| Azure 哨兵响应器|--|--| X | X |
| Azure 哨兵参与者 | -- | X | X | X |
| Azure 哨兵参与者 + 逻辑应用参与者 | X | X | X | X |


> [!NOTE]
> - 对于需要完成任务的用户，建议尽可能为其分配权限最小的角色。 例如，将 Azure Sentinel 参与者角色仅分配给需要创建规则或仪表板的用户。
> - 我们建议您在资源组作用域中设置 Azure Sentinel 的权限，以便用户可以访问同一资源组中的所有 Azure Sentinel 工作区。
>
## <a name="building-custom-rbac-roles"></a>生成自定义 RBAC 角色

除了使用内置 RBAC 角色外，还可以为 Azure Sentinel 创建自定义 RBAC 角色。 Azure Sentinel 的自定义 RBAC 角色的创建方式与创建其他[自定义 Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)角色的方式相同，具体取决于对 Azure Sentinel 和[Azure 日志分析资源](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)[的特定权限](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights)。

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>对存储在 Azure Sentinel 中的数据的高级 RBAC
  
您可以在 Azure Sentinel 工作区中的数据使用日志分析高级基于角色的访问控制。 这包括每个数据类型基于角色的访问控制以及以资源为中心的基于角色的访问控制。 有关日志分析角色的详细信息，请参阅在 [Azure 监视器 中管理日志数据和工作区](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)。

## <a name="next-steps"></a>后续步骤
在本文中，您学习了如何使用 Azure Sentinel 用户的角色，以及每个角色使用户能够做什么。

* [Azure 哨兵博客](https://aka.ms/azuresentinelblog). 查找关于 Azure 安全性及合规性的博客文章。
