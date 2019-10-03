---
title: 使用 RBAC 和 REST API 管理对 Azure 资源的访问 - Azure | Microsoft Docs
description: 了解如何使用基于角色的访问控制 (RBAC) 和 REST API 来管理用户、组和应用程序对 Azure 资源的访问。 这包括如何列出访问权限、授予访问权限以及删除访问权限。
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996473"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>使用 RBAC 和 REST API 管理对 Azure 资源的访问权限

可以通过[基于角色的访问控制 (RBAC)](overview.md) 管理对 Azure 资源的访问权限。 本文介绍如何使用 RBAC 和 REST API 来管理用户、组和应用程序的访问权限。

## <a name="list-access"></a>列出访问权限

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
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

    在前面的示例中，microsoft 是引用应用服务实例的资源提供程序。 同样，可以使用任何其他资源提供程序并指定范围。 有关详细信息，请参阅[Azure 资源提供程序和类型](../azure-resource-manager/resource-manager-supported-services.md)以及受支持的[azure 资源管理器资源提供程序操作](resource-provider-operations.md)。  
     
1. 将“{filter}”替换为筛选角色分配列表时要应用的条件。

    | 筛选 | 描述 |
    | --- | --- |
    | `$filter=atScope()` | 只列出指定范围内的角色分配，而不包括子范围内的角色分配。 |
    | `$filter=principalId%20eq%20'{objectId}'` | 列出指定用户、组或服务主体的角色分配。 |
    | `$filter=assignedTo('{objectId}')` | 列出指定用户或服务主体的角色分配。 如果用户是具有角色分配的组的成员，则该角色分配也会列出。 此筛选器对于组是可传递的，这意味着如果用户是组的成员，并且该组是具有角色分配的另一个组的成员，则该角色分配也会列出。 此筛选器仅接受用户或服务主体的对象 ID。 不能传递组的对象 ID。 |

## <a name="grant-access"></a>授予访问权限

在 RBAC 中，若要授予访问权限，请创建角色分配。 若要创建角色分配，请使用[角色分配 - Create](/rest/api/authorization/roleassignments/create) REST API 并指定安全主体、角色定义和范围。 若要调用此 API，必须具有对 `Microsoft.Authorization/roleAssignments/write` 操作的访问权限。 在内置角色中，只有[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)具有对此操作的访问权限。

1. 使用[角色定义 - List](/rest/api/authorization/roledefinitions/list) REST API 或参阅[内置角色](built-in-roles.md)，获取你想要分配的角色定义的标识符。

1. 使用 GUID 工具生成将用于角色分配标识符的唯一标识符。 标识符的格式为：`00000000-0000-0000-0000-000000000000`

1. 从以下请求和正文开始：

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. 在 URI 内，将“{scope}”替换为角色分配的范围。

    | 范围 | 类型 |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    | `subscriptions/{subscriptionId1}` | 订阅 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. 将“{roleAssignmentName}”替换为角色分配的 GUID 标识符。

1. 在请求正文中，将 *{scope}* 替换为角色分配的作用域。

    | 范围 | type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    | `subscriptions/{subscriptionId1}` | 订阅 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. 将“{roleDefinitionId}”替换为角色定义标识符。

1. 将“{principalId}”替换为将分配有角色的用户、组或服务主体的对象标识符。

## <a name="remove-access"></a>撤消访问权限

在 RBAC 中，若要删除访问权限，请删除角色分配。 若要删除角色分配，请使用[角色分配 - Delete](/rest/api/authorization/roleassignments/delete) REST API。 若要调用此 API，必须具有对 `Microsoft.Authorization/roleAssignments/delete` 操作的访问权限。 在内置角色中，只有[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)具有对此操作的访问权限。

1. 获取角色分配标识符 (GUID)。 首次创建角色分配时将返回此标识符，也可以通过列出角色分配来获取它。

1. 从下面的请求开始：

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. 在 URI 内，将“{scope}”替换为删除角色分配的范围。

    | 范围 | 类型 |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    | `subscriptions/{subscriptionId1}` | 订阅 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. 将“{roleAssignmentName}”替换为角色分配的 GUID 标识符。

## <a name="next-steps"></a>后续步骤

- [使用 Resource Manager 模板和 Resource Manager REST API 部署资源](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API 参考](/rest/api/azure/)
- [使用 REST API 为 Azure 资源创建自定义角色](custom-roles-rest.md)
