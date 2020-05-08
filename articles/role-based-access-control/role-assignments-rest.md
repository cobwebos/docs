---
title: 使用 REST API 添加或删除 Azure 角色分配-Azure RBAC
description: 了解如何使用 REST API 和 Azure 基于角色的访问控制（Azure RBAC）为用户、组、服务主体或托管标识授予对 Azure 资源的访问权限。
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
ms.date: 05/06/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f9a8b35b07a4149fa2d6b9f8e6698e41f3e6870c
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891298"
---
# <a name="add-or-remove-azure-role-assignments-using-the-rest-api"></a>使用 REST API 添加或删除 Azure 角色分配

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 本文介绍如何使用 REST API 分配角色。

## <a name="prerequisites"></a>必备条件

若要添加或删除角色分配，必须拥有以下权限：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>添加角色分配

在 Azure RBAC 中，若要授予访问权限，请添加角色分配。 若要添加角色分配，请使用[角色分配 - Create](/rest/api/authorization/roleassignments/create) REST API 并指定安全主体、角色定义和范围。 若要调用此 API，必须具有对 `Microsoft.Authorization/roleAssignments/write` 操作的访问权限。 在内置角色中，只有[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)具有对此操作的访问权限。

1. 使用[角色定义 - List](/rest/api/authorization/roledefinitions/list) REST API 或参阅[内置角色](built-in-roles.md)，获取你想要分配的角色定义的标识符。

1. 使用 GUID 工具生成将用于角色分配标识符的唯一标识符。 标识符的格式为：`00000000-0000-0000-0000-000000000000`

1. 从以下请求和正文开始：

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. 在 URI 内，将“{scope}”  替换为角色分配的范围。

    > [!div class="mx-tableFixed"]
    > | 范围 | 类型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    > | `subscriptions/{subscriptionId1}` | 订阅 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 资源 |

    在前面的示例中，microsoft.web 是引用应用服务实例的资源提供程序。 同样，可以使用任何其他资源提供程序并指定范围。 有关详细信息，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)和支持的 [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)。  

1. 将 *{roleAssignmentId}* 替换为角色分配的 GUID 标识符。

1. 在请求正文中，将 *{scope}* 替换为角色分配的范围。

    > [!div class="mx-tableFixed"]
    > | 范围 | 类型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    > | `subscriptions/{subscriptionId1}` | 订阅 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 资源 |

1. 将“{roleDefinitionId}”  替换为角色定义标识符。

1. 将“{principalId}”  替换为将分配有角色的用户、组或服务主体的对象标识符。

以下请求和正文将[备份读取](built-in-roles.md#backup-reader)者角色分配给订阅范围内的用户：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

下面显示了输出示例：

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="remove-a-role-assignment"></a>删除角色分配

在 Azure RBAC 中，若要删除访问权限，请删除角色分配。 若要删除角色分配，请使用[角色分配 - Delete](/rest/api/authorization/roleassignments/delete) REST API。 若要调用此 API，必须具有对 `Microsoft.Authorization/roleAssignments/delete` 操作的访问权限。 在内置角色中，只有[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)具有对此操作的访问权限。

1. 获取角色分配标识符 (GUID)。 首次创建角色分配时将返回此标识符，也可以通过列出角色分配来获取它。

1. 从下面的请求开始：

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. 在 URI 内，将“{scope}”** 替换为删除角色分配的范围。

    > [!div class="mx-tableFixed"]
    > | 作用域 | 类型 |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | 管理组 |
    > | `subscriptions/{subscriptionId1}` | 订阅 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | 资源组 |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | 资源 |

1. 将 *{roleAssignmentId}* 替换为角色分配的 GUID 标识符。

以下请求在订阅范围内删除指定的角色分配：

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

下面显示了输出示例：

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="next-steps"></a>后续步骤

- [使用 REST API 列出 Azure 角色分配](role-assignments-list-rest.md)
- [使用 Resource Manager 模板和 Resource Manager REST API 部署资源](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API 参考](/rest/api/azure/)
- [使用 REST API 创建或更新 Azure 自定义角色](custom-roles-rest.md)
