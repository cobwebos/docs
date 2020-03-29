---
title: 使用 Azure RBAC 和 REST API 列出角色分配
description: 了解如何使用 Azure 基于角色的访问控制 (RBAC) 和 REST API 来确定用户、组、服务主体和托管标识有权访问的资源内容。
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a494e7fd4c9fb79faa6a1d8cb2c3c871796ccdc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062159"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-rest-api"></a>使用 Azure RBAC 和 REST API 列出角色分配

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文介绍如何使用 REST API 列出角色分配。

> [!NOTE]
> 如果您的组织将管理功能外包给使用[Azure 委派资源的](../lighthouse/concepts/azure-delegated-resource-management.md)服务提供商，则该服务提供商授权的角色分配将不会在此处显示。

## <a name="list-role-assignments"></a>列出角色分配

在 RBAC 中，若要列出访问权限，请列出角色分配。 若要列出角色分配，可以使用其中一个[角色分配 - List](/rest/api/authorization/roleassignments/list) REST API。 若要优化结果，请指定一个范围和可选的筛选器。

1. 从下面的请求开始：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. 在 URI 中，将“{scope}”** 替换为要列出角色分配的范围。

    > [!div class="mx-tableFixed"]
    > | 范围 | 类型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    > | `subscriptions/{subscriptionId1}` | 订阅 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | 资源 |

    在前面的示例中，microsoft.web 是引用应用服务实例的资源提供程序。 同样，可以使用任何其他资源提供程序并指定范围。 有关详细信息，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)和支持的 [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)。  
     
1. 将“{filter}”** 替换为筛选角色分配列表时要应用的条件。

    > [!div class="mx-tableFixed"]
    > | “筛选器” | 描述 |
    > | --- | --- |
    > | `$filter=atScope()` | 只列出指定范围内的角色分配，而不包括子范围内的角色分配。 |
    > | `$filter=assignedTo('{objectId}')` | 列出指定用户或服务主体的角色分配。<br/>如果用户是具有角色分配的组的成员，则该角色分配也会列出。 此筛选器对于组是可传递的，这意味着如果用户是组的成员，并且该组是具有角色分配的另一个组的成员，则该角色分配也会列出。<br/>此筛选器仅接受用户或服务主体的对象 ID。 不能传递组的对象 ID。 |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | 列出指定用户或服务主体和指定作用域的角色分配。 |
    > | `$filter=principalId+eq+'{objectId}'` | 列出指定用户、组或服务主体的角色分配。 |

## <a name="next-steps"></a>后续步骤

- [使用 Azure RBAC 和 REST API 添加或删除角色分配](role-assignments-rest.md)
- [Azure REST API 参考](/rest/api/azure/)
