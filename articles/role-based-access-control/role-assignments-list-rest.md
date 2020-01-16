---
title: 使用 Azure RBAC 和 REST API 列出角色分配
description: 了解如何使用 Azure 基于角色的访问控制（RBAC）和 REST API 来确定用户、组、服务主体或托管标识有权访问哪些资源。
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0db3e1b222aad7d2a5aa9fc20663fc6e17ea4f8c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981078"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>使用 Azure RBAC 和 REST API 列出角色分配

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文介绍了如何使用 REST API 列出角色分配。

> [!NOTE]
> 如果你的组织对使用[Azure 委托资源管理](../lighthouse/concepts/azure-delegated-resource-management.md)的服务提供商具有外包管理功能，则此处将不会显示该服务提供商授权的角色分配。

## <a name="list-role-assignments"></a>列出角色分配

在 RBAC 中，若要列出访问权限，请列出角色分配。 若要列出角色分配，可以使用其中一个[角色分配 - List](/rest/api/authorization/roleassignments/list) REST API。 若要优化结果，请指定一个范围和可选的筛选器。

1. 从下面的请求开始：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. 在 URI 中，将“{scope}”替换为要列出角色分配的范围。

    | 范围 | 类型 |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    | `subscriptions/{subscriptionId1}` | 订阅 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 资源 |

    在前面的示例中，microsoft 是引用应用服务实例的资源提供程序。 同样，可以使用任何其他资源提供程序并指定范围。 有关详细信息，请参阅[Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)以及受支持的[azure 资源管理器资源提供程序操作](resource-provider-operations.md)。  
     
1. 将“{filter}”替换为筛选角色分配列表时要应用的条件。

    | 筛选 | Description |
    | --- | --- |
    | `$filter=atScope()` | 仅列出指定范围内的角色分配，不包括而处的角色分配。 |
    | `$filter=principalId%20eq%20'{objectId}'` | 列出指定用户、组或服务主体的角色分配。 |
    | `$filter=assignedTo('{objectId}')` | 列出指定用户或服务主体的角色分配。 如果用户是具有角色分配的组的成员，则还会列出该角色分配。 此筛选器可传递给组，这意味着，如果用户是组的成员，并且该组是具有角色分配的另一个组的成员，则还会列出该角色分配。 此筛选器仅接受用户或服务主体的对象 ID。 不能传递组的对象 ID。 |

## <a name="next-steps"></a>后续步骤

- [使用 Azure RBAC 和 REST API 添加或删除角色分配](role-assignments-rest.md)
- [Azure REST API 参考](/rest/api/azure/)
