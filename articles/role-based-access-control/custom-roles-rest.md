---
title: 使用 REST API 创建自定义角色 - Azure | Microsoft Docs
description: 了解如何使用 REST API 为基于角色的访问控制 (RBAC) 创建自定义角色。 这包括如何列出、创建、更新和删除自定义角色。
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
ms.topic: article
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8267846fed30baf2c37dcddd453ae9ead9341da9
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36323392"
---
# <a name="create-custom-roles-using-the-rest-api"></a>使用 REST API 创建自定义角色

如果[内置角色](built-in-roles.md)不能满足组织的特定需求，你可以创建自己的自定义角色。 本文介绍如何使用 REST API 创建和管理自定义角色。

## <a name="list-roles"></a>列出角色

若要列出所有角色，或者使用显示名称获取有关单个角色的信息，请使用[角色定义 - 列出](/rest/api/authorization/roledefinitions/list) REST API。 若要调用此 API，必须在相应范围内对 `Microsoft.Authorization/roleDefinitions/read` 操作拥有访问权限。 一些[内置角色](built-in-roles.md)均具有对此操作的访问权限。

1. 从下面的请求开始：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. 在 URI 中，将 *{scope}* 替换为要列出角色的范围。

    | 范围 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 订阅 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将 *{filter}* 替换为筛选角色列表时要应用的条件。

    | 筛选器 | 说明 |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | 列出在指定范围及其任何子范围内可用于分配的角色。 |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | 使用角色的准确显示名称的 URL 编码形式。 例如 `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>获取有关角色的信息

若要使用角色定义标识符获取某个角色的信息，请使用[角色定义 - 获取](/rest/api/authorization/roledefinitions/get) REST API。 若要调用此 API，必须在相应范围内对 `Microsoft.Authorization/roleDefinitions/read` 操作拥有访问权限。 一些[内置角色](built-in-roles.md)均具有对此操作的访问权限。

若要使用显示名称获取有关单个角色的信息，请参阅前面的[列出角色](custom-roles-rest.md#list-roles)部分。

1. 使用[角色定义 - 列出](/rest/api/authorization/roledefinitions/list) REST API 获取角色的 GUID 标识符。 对于内置角色，还可以从[内置角色](built-in-roles.md)获取标识符。

1. 从下面的请求开始：

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. 在 URI 中，将 *{scope}* 替换为要列出角色的范围。

    | 范围 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 订阅 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将 *{roleDefinitionId}* 替换为角色定义的 GUID 标识符。

## <a name="create-a-custom-role"></a>创建自定义角色

若要创建自定义角色，请使用[角色定义 - 创建或更新](/rest/api/authorization/roledefinitions/createorupdate) REST API。 若要调用此 API，必须对所有 `assignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 操作拥有访问权限。 在内置角色中，只有[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)具有对此操作的访问权限。 

1. 查看可用来为自定义角色创建权限的[资源提供程序操作](resource-provider-operations.md)列表。

1. 使用 GUID 工具生成用作自定义角色标识符的唯一标识符。 标识符的格式为：`00000000-0000-0000-0000-000000000000`

1. 从以下请求和正文开始：

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. 在 URI 中，将 *{scope}* 替换为自定义角色的第一个 `assignableScopes`。

    | 范围 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 订阅 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将 *{roleDefinitionId}* 替换为自定义角色的 GUID 标识符。

1. 在请求正文中的 `assignableScopes` 属性内，将 *{roleDefinitionId}* 替换为 GUID 标识符。

1. 将 *{subscriptionId}* 替换为订阅标识符。

1. 在 `actions` 属性中，添加该角色允许执行的操作。

1. 在 `notActions` 属性中，添加要从允许的 `actions` 中排除的操作。

1. 在 `roleName` 和 `description` 属性中，指定唯一的角色名称和说明。 有关属性的详细信息，请参阅[自定义角色](custom-roles.md)。

    下面显示了请求正文的示例：

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>更新自定义角色

若要更新自定义角色，请使用[角色定义 - 创建或更新](/rest/api/authorization/roledefinitions/createorupdate) REST API。 若要调用此 API，必须对所有 `assignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 操作拥有访问权限。 在内置角色中，只有[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)具有对此操作的访问权限。 

1. 使用[角色定义 - 列出](/rest/api/authorization/roledefinitions/list)或[角色定义 - 获取](/rest/api/authorization/roledefinitions/get) REST API 获取有关自定义角色的信息。 有关详细信息，请参阅前面的[列出角色](custom-roles-rest.md#list-roles)部分。

1. 从下面的请求开始：

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. 在 URI 中，将 *{scope}* 替换为自定义角色的第一个 `assignableScopes`。

    | 范围 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 订阅 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将 *{roleDefinitionId}* 替换为自定义角色的 GUID 标识符。

1. 根据自定义角色的信息，使用以下格式创建请求正文：

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. 使用想要对自定义角色所做的更改来更新请求正文。

    下面显示了已添加新诊断设置操作的请求正文示例：

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>删除自定义角色

若要删除自定义角色，请使用[角色定义 - 删除](/rest/api/authorization/roledefinitions/delete) REST API。 若要调用此 API，必须对所有 `assignableScopes` 的 `Microsoft.Authorization/roleDefinitions/delete` 操作拥有访问权限。 在内置角色中，只有[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)具有对此操作的访问权限。 

1. 使用[角色定义 - 列出](/rest/api/authorization/roledefinitions/list)或[角色定义 - 获取](/rest/api/authorization/roledefinitions/get) REST API 获取自定义角色的 GUID 标识符。 有关详细信息，请参阅前面的[列出角色](custom-roles-rest.md#list-roles)部分。

1. 从下面的请求开始：

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. 在 URI 中，将 *{scope}* 替换为要删除自定义角色的范围。

    | 范围 | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | 订阅 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | 资源组 |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | 资源 |

1. 将 *{roleDefinitionId}* 替换为自定义角色的 GUID 标识符。

## <a name="next-steps"></a>后续步骤

- [Azure 中的自定义角色](custom-roles.md)
- [使用 RBAC 和 REST API 管理访问权限](role-assignments-rest.md)
- [Azure REST API 参考](/rest/api/azure/)