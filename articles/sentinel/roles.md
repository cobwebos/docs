---
title: Azure Sentinel 中的权限 |Microsoft Docs
description: 本文介绍 Azure Sentinel 如何使用基于角色的访问控制向用户分配权限并标识每个角色允许的操作。
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
ms.openlocfilehash: 2e1b1a4786670974a40b22d44fc219c6be5d97a3
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744751"
---
# <a name="permissions-in-azure-sentinel"></a>Azure Sentinel 中的权限

Azure Sentinel 使用 [基于角色的访问控制（RBAC）](../role-based-access-control/role-assignments-portal.md)来提供可分配给 Azure 中的用户、组和服务的 [内置角色](../role-based-access-control/built-in-roles.md) 。

使用 RBAC，你可以使用和创建安全操作团队中的角色来授予对 Azure Sentinel 的适当访问权限。 基于这些角色，你可以精细地控制有权访问 Azure Sentinel 的用户可以看到的内容。 可以直接在 Azure Sentinel 工作区中分配 RBAC 角色，也可以在工作区所属的订阅或资源组中分配 RBAC 角色。

有三个特定的内置 Azure Sentinel 角色。  
**所有 Azure Sentinel 内置角色都授予对 Azure Sentinel 工作区中数据的读取访问权限。**
- [Azure Sentinel 读取器](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel 响应程序](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel 参与者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

除了 Azure Sentinel 专用 RBAC 角色外，还可以使用 Azure 和 Log Analytics RBAC 角色，这些角色可授予一组更广泛的权限，包括对 Azure Sentinel 工作区和其他资源的访问权限：

- **Azure 角色：** [所有者](../role-based-access-control/built-in-roles.md#owner)、[参与者](../role-based-access-control/built-in-roles.md#contributor)和[读者](../role-based-access-control/built-in-roles.md#reader)。 Azure 角色授予所有 Azure 资源的访问权限，包括 Log Analytics 工作区和 Azure Sentinel 资源。

-   **Log Analytics 角色：** [Log Analytics 参与者](../role-based-access-control/built-in-roles.md#log-analytics-contributor)、 [Log Analytics 读取](../role-based-access-control/built-in-roles.md#log-analytics-reader)者。 Log Analytics 角色授予所有 Log Analytics 工作区的访问权限。 

> [!NOTE]
> Log Analytics 角色还会授予对所有 Azure 资源的读取访问权限，但只会为 Log Analytics 资源分配写入权限。


例如，使用**Azure sentinel reader**和**azure 捐助**者（而不是**azure sentinel 参与者**）角色分配的用户将可以在 Azure sentinel 中编辑数据，不过它们只具有**Sentinel 读取器**权限。 因此，如果希望仅在 Azure Sentinel 中向用户授予权限，则应仔细删除此用户以前的权限，确保不会中断其他资源所需的任何权限角色。

> [!NOTE]
>- Azure Sentinel 使用自动响应的行动手册。 行动手册利用 Azure 逻辑应用，是单独的 Azure 资源。 你可能想要将安全操作团队的特定成员指定为使用逻辑应用进行安全业务流程、自动化和响应（之忠诚度）操作的选项。 可以使用[逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor)角色或[逻辑应用操作员](../role-based-access-control/built-in-roles.md#logic-app-operator)角色分配显式权限来使用行动手册。
>- 若要添加数据连接器，每个连接器的必要角色都按连接器类型列出，并在相关连接器页中列出。 此外，若要连接任何数据源，您必须对 Azure Sentinel 工作区具有写入权限。



## <a name="roles-and-allowed-actions"></a>角色和允许的操作

下表显示 Azure Sentinel 中的角色和允许的操作。 X 指示允许该角色执行该操作。

| 角色 | 创建和运行行动手册| 创建和编辑仪表板、分析规则和其他 Azure Sentinel 资源 | 管理事件（解除、分配等） | 查看数据、事件、仪表板和其他 Azure Sentinel 资源 |
|--- |---|---|---|---|
| Azure Sentinel 读取器 | -- | -- | -- | X |
| Azure Sentinel 响应程序|--|--| X | X |
| Azure Sentinel 参与者 | -- | X | X | X |
| Azure Sentinel 参与者 + 逻辑应用参与者 | X | X | X | X |


> [!NOTE]
> - 对于需要完成任务的用户，建议尽可能为其分配权限最小的角色。 例如，仅将 Azure Sentinel 参与者角色分配给需要创建规则或仪表板的用户。
> - 建议在资源组作用域中设置 Azure Sentinel 的权限，以便用户可以访问同一资源组中的所有 Azure Sentinel 工作区。
>
## <a name="building-custom-rbac-roles"></a>生成自定义 RBAC 角色

除了、或而不是使用内置 RBAC 角色，你还可以为 Azure Sentinel 创建自定义 RBAC 角色。 Azure Sentinel 的自定义 RBAC 角色的创建方式与创建其他[自定义 AZURE RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)角色的方式相同，具体取决于 azure Sentinel 和[azure Log Analytics 资源](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)的[特定权限](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights)。

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>对存储在 Azure Sentinel 中的数据的高级 RBAC
  
可以在 Azure Sentinel 工作区中的数据上使用 Log Analytics 高级基于角色的访问控制。 这包括每个数据类型的基于角色的访问控制和以资源为中心的基于角色的访问控制。 有关 Log Analytics 角色的详细信息，请参阅 [在 Azure Monitor 中管理日志数据和工作区](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何使用 Azure Sentinel 用户角色，以及每个角色允许用户执行的操作。

* [Azure Sentinel 博客](https://aka.ms/azuresentinelblog)。 查找关于 Azure 安全性及合规性的博客文章。
