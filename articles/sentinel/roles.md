---
title: Azure Sentinel 中的权限 |Microsoft Docs
description: 本文介绍 Azure Sentinel 如何使用基于角色的访问控制向用户分配权限，并标识每个角色允许的操作。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: b48ff1043ae8128a5cbfdcbba0548d89b5af2624
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565836"
---
# <a name="permissions-in-azure-sentinel"></a>Azure Sentinel 中的权限

Azure Sentinel 使用 azure [RBAC)  (azure 基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)来提供 [built-in roles](../role-based-access-control/built-in-roles.md)   可分配给 Azure 中的用户、组和服务的内置角色。

使用 RBAC 创建和分配安全操作团队中的角色，以授予 Azure Sentinel 的适当访问权限。 不同角色使你可以精细控制 Azure Sentinel 的用户可以查看和执行的操作。 可以直接在 Azure Sentinel 工作区中分配 azure 角色 (参阅下面) 或工作区所属的订阅或资源组中的说明，Azure Sentinel 将继承该工作区。

## <a name="roles-for-working-in-azure-sentinel"></a>Azure Sentinel 中使用的角色

### <a name="azure-sentinel-specific-roles"></a>Azure Sentinel 特定的角色

有三个专用的内置 Azure Sentinel 角色。

**所有 Azure Sentinel 内置角色都授予对 Azure Sentinel 工作区中数据的读取访问权限。**

- [Azure Sentinel 读者](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) 可以查看数据、事件、工作簿和其他 Azure Sentinel 资源。

- 除上述外， [Azure Sentinel 响应](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)程序还可以管理事件 (分配、消除等 ) 

- 除了以上，还可以创建和编辑工作簿、分析规则和其他[Azure sentinel 资源](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)。

> [!NOTE]
>
> - 为获得最佳结果，应在包含 Azure Sentinel 工作区的 **资源组** 上分配这些角色。 这样一来，这些角色将应用于所有部署的资源，以支持 Azure Sentinel，因为这些资源也应该放在同一资源组中。
>
> - 另一种方法是直接在 Azure Sentinel **工作区** 本身上分配角色。 如果执行此操作，还必须在该工作区中的 SecurityInsights **解决方案资源** 上分配相同的角色。 你可能还需要将它们分配给其他资源，并且需要持续管理资源的角色分配。

### <a name="additional-roles-and-permissions"></a>其他角色和权限

可能需要为具有特定作业要求的用户分配其他角色或特定权限，才能完成其任务。

- 使用行动手册自动完成对威胁的响应

    Azure Sentinel 使用自动响应的 **行动手册** 。 行动手册基于 **Azure 逻辑应用**，是独立的 azure 资源。 你可能想要将逻辑应用用于安全业务流程、自动化和响应 (之忠诚度) 操作，以将其分配给安全操作团队的特定成员。 可以使用 [逻辑应用参与者](../role-based-access-control/built-in-roles.md#logic-app-contributor) 角色或 [逻辑应用操作员](../role-based-access-control/built-in-roles.md#logic-app-operator) 角色分配显式权限来使用行动手册。

- 将数据源连接到 Azure Sentinel

    要使用户能够添加 **数据连接器**，必须在 Azure Sentinel 工作区中分配用户写入权限。 另外，请注意每个连接器所需的附加权限，如相关的连接器页中所列。

- 来宾用户分配事件

    如果来宾用户需要能够分配事件，则除了 Azure Sentinel 响应方角色外，还需要为该用户分配 [目录读取](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)者角色。 请注意，此角色 *不* 是 Azure RBAC 角色，而是 **Azure Active Directory** 角色，并且在默认情况下， (非来宾) 用户已分配此角色。 

有关并行比较，请参阅 [下表](#roles-and-allowed-actions)。

### <a name="other-roles-you-might-see-assigned"></a>你可能会看到分配的其他角色

在分配 Azure 特定于 Azure 的 Azure 角色中，你可能会遇到其他 Azure 并 Log Analytics 可能已分配给用户以供其他目的的 Azure 角色。 应注意，这些角色授予了更广泛的权限集，其中包括对 Azure Sentinel 工作区和其他资源的访问权限：

- **Azure 角色：** [所有者](../role-based-access-control/built-in-roles.md#owner)、 [参与者](../role-based-access-control/built-in-roles.md#contributor)和 [读者](../role-based-access-control/built-in-roles.md#reader)。 Azure 角色授予所有 Azure 资源的访问权限，包括 Log Analytics 工作区和 Azure Sentinel 资源。

- **Log Analytics 角色：** [Log Analytics 参与者](../role-based-access-control/built-in-roles.md#log-analytics-contributor) 和 [Log Analytics 读者](../role-based-access-control/built-in-roles.md#log-analytics-reader)。 Log Analytics 角色授予对 Log Analytics 工作区的访问权限。 

例如，如果分配了 Azure 级别**参与者**角色，则分配有**azure sentinel 读者**角色但不是**azure sentinel 参与者**角色的用户仍可以编辑 azure sentinel 中的项目。 因此，如果希望仅在 Azure Sentinel 中向用户授予权限，则应仔细删除此用户以前的权限，确保不会中断任何需要访问其他资源的权限。

## <a name="roles-and-allowed-actions"></a>角色和允许的操作

下表汇总了 Azure Sentinel 中的角色和允许的操作。 

| 角色 | 创建和运行行动手册| 创建和编辑工作簿、分析规则和其他 Azure Sentinel 资源 | 管理事件 (关闭、分配等 )  | 查看数据、事件、工作簿和其他 Azure Sentinel 资源 |
|---|---|---|---|---|
| Azure Sentinel 读取者 | -- | -- | -- | &#10003; |
| Azure Sentinel 响应方 | -- | -- | &#10003; | &#10003; |
| Azure Sentinel 参与者 | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel 参与者 + 逻辑应用参与者 | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-rbac"></a>自定义角色和高级 RBAC

- 除了或使用 Azure 内置角色，你还可以为 Azure Sentinel 创建 Azure 自定义角色。 Azure Sentinel 的 azure 自定义角色的创建方式与创建其他[自定义 AZURE RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)角色的方式相同，具体取决于 azure Sentinel 和[azure Log Analytics 资源](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)的[特定权限](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights)。

- 可以在 Azure Sentinel 工作区中的数据上使用 Log Analytics 高级基于角色的访问控制。 这包括基于数据类型的 RBAC 和以资源为中心的 RBAC。 有关 Log Analytics 角色的详细信息，请参阅 [在 Azure Monitor 中管理日志数据和工作区](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 Azure Sentinel 用户角色，以及每个角色允许用户执行的操作。

* [Azure Sentinel 博客](https://aka.ms/azuresentinelblog)。 查找关于 Azure 安全性及合规性的博客文章。
