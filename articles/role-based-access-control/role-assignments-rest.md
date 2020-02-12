---
title: 添加或删除带有 RBAC 和 REST API 的角色分配
description: 了解如何使用 Azure 基于角色的访问控制（RBAC）和 REST API 为用户、组、服务主体或托管标识授予对 Azure 资源的访问权限。
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ae6fce5ab962637fe477ade75cf81b6ac237bdd2
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138316"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-rest-api"></a>使用 Azure RBAC 和 REST API 添加或删除角色分配

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 本文介绍了如何使用 REST API 分配角色。

## <a name="prerequisites"></a>先决条件

若要添加或删除角色分配，必须具备：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>添加角色分配

在 RBAC 中，若要授予访问权限，请添加角色分配。 若要添加角色分配，请使用[角色分配-创建](/rest/api/authorization/roleassignments/create)REST API，并指定安全主体、角色定义和作用域。 若要调用此 API，必须具有对 `Microsoft.Authorization/roleAssignments/write` 操作的访问权限。 在内置角色中，只有[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)具有对此操作的访问权限。

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
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | 资源 |

1. 将“{roleAssignmentName}”替换为角色分配的 GUID 标识符。

1. 在请求正文中，将 *{scope}* 替换为角色分配的作用域。

    | 范围 | 类型 |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    | `subscriptions/{subscriptionId1}` | 订阅 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | 资源 |

1. 将“{roleDefinitionId}”替换为角色定义标识符。

1. 将“{principalId}”替换为将分配有角色的用户、组或服务主体的对象标识符。

## <a name="remove-a-role-assignment"></a>删除角色分配

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
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | 资源 |

1. 将“{roleAssignmentName}”替换为角色分配的 GUID 标识符。

## <a name="next-steps"></a>后续步骤

- [使用 Azure RBAC 和 REST API 列出角色分配](role-assignments-list-rest.md)
- [使用 Resource Manager 模板和 Resource Manager REST API 部署资源](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API 参考](/rest/api/azure/)
- [使用 REST API 为 Azure 资源创建自定义角色](custom-roles-rest.md)
