---
title: 使用 RBAC 和 REST API 管理访问权限 - Azure | Microsoft Docs
description: 了解如何使用基于角色的访问控制 (RBAC) 和 REST API 来管理用户、组和应用程序的访问权限。 这包括如何列出访问权限、授予访问权限以及删除访问权限。
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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 859a410a4ff9204e8e52fbd2cc3b38823f4bb830
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435212"
---
# <a name="manage-access-using-rbac-and-the-rest-api"></a>使用 RBAC 和 REST API 管理访问权限

可以在 Azure 中通过[基于角色的访问控制 (RBAC)](overview.md) 这种方式管理对资源的访问。 本文介绍如何使用 RBAC 和 REST API 来管理用户、组和应用程序的访问权限。

## <a name="list-access"></a>列出访问权限

在 RBAC 中，若要列出访问权限，请列出角色分配。 若要列出角色分配，可以使用其中一个[角色分配 - List](/rest/api/authorization/roleassignments/list) REST API。 若要优化结果，请指定一个范围和可选的筛选器。 若要调用 API，必须具有对指定范围的 `Microsoft.Authorization/roleAssignments/read` 操作的访问权限。 一些[内置角色](built-in-roles.md)均具有对此操作的访问权限。

1. 从下面的请求开始：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. 在 URI 中，将“{scope}”替换为要列出角色分配的范围。

    | 范围 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 订阅 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将“{filter}”替换为筛选角色分配列表时要应用的条件。

    | 筛选器 | 说明 |
    | --- | --- |
    | `$filter=atScope()` | 只列出指定范围内的角色分配，而不包括子范围内的角色分配。 |
    | `$filter=principalId%20eq%20'{objectId}'` | 列出特定用户、组或服务主体的角色分配。 |
    | `$filter=assignedTo('{objectId}')` | 列出特定用户（包括继承自组的用户）的角色分配。 |

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
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. 在 URI 内，将“{scope}”替换为角色分配的范围。

    | 范围 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 订阅 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将“{roleAssignmentName}”替换为角色分配的 GUID 标识符。

1. 在请求正文中，将“{subscriptionId}”替换为你的订阅标识符。

1. 将“{roleDefinitionId}”替换为角色定义标识符。

1. 将“{principalId}”替换为将分配有角色的用户、组或服务主体的对象标识符。

## <a name="remove-access"></a>删除访问权限

在 RBAC 中，若要删除访问权限，请删除角色分配。 若要删除角色分配，请使用[角色分配 - Delete](/rest/api/authorization/roleassignments/delete) REST API。 若要调用此 API，必须具有对 `Microsoft.Authorization/roleAssignments/delete` 操作的访问权限。 在内置角色中，只有[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)具有对此操作的访问权限。

1. 获取角色分配标识符 (GUID)。 首次创建角色分配时将返回此标识符，也可以通过列出角色分配来获取它。

1. 从下面的请求开始：

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. 在 URI 内，将“{scope}”替换为删除角色分配的范围。

    | 范围 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 订阅 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将“{roleAssignmentName}”替换为角色分配的 GUID 标识符。

## <a name="next-steps"></a>后续步骤

- [使用 Resource Manager 模板和 Resource Manager REST API 部署资源](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API 参考](/rest/api/azure/)
- [使用 REST API 创建自定义角色](custom-roles-rest.md)