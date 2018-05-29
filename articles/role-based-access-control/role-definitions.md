---
title: 了解 Azure RBAC 中的角色定义 | Microsoft Docs
description: 了解基于角色的访问控制 (RBAC) 中的角色定义以及如何在 Azure 中定义自定义角色，以便对资源进行精细的访问权限管理。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161810"
---
# <a name="understand-role-definitions"></a>了解角色定义

如果想要了解角色是如何工作的，或者要创建自己的[自定义角色](custom-roles.md)，那么了解角色的定义方法会很有帮助。 本文介绍角色定义的详细信息，并提供了一些示例。

## <a name="role-definition-structure"></a>角色定义结构

*角色定义*是权限的集合。 它有时简称为“角色”。 角色定义列出了可以执行的操作，例如读取、写入和删除。 它还可以列出不能执行的操作。 角色定义具有以下结构：

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

使用以下格式的字符串指定操作：

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

操作字符串的 `{action}` 部分指定可以对某个资源类型执行的操作类型。 例如，将在 `{action}` 中看到以下子字符串：

| 操作子字符串    | 说明         |
| ------------------- | ------------------- |
| `*` | 通配符授予对与字符串匹配的所有操作的访问权限。 |
| `read` | 允许读取操作 (GET)。 |
| `write` | 允许写入操作（PUT、POST 和 PATCH）。 |
| `delete` | 允许删除操作 (DELETE)。 |

下面是 JSON 格式的[参与者](built-in-roles.md#contributor)角色定义。 `actions` 下的通配符 (`*`) 操作表示分配给此角色的主体可以执行所有操作，换句话说，它可以管理所有内容。 这包括将来定义的操作，因为 Azure 会添加新的资源类型。 `notActions` 下的操作会从 `actions` 中减去。 就[参与者](built-in-roles.md#contributor)角色而言，`notActions` 去除了此角色管理资源访问权限以及分配资源访问权限的能力。

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>管理操作

管理操作的基于角色的访问控制在角色定义的 `actions` 和 `notActions` 部分中指定。 下面是 Azure 中管理操作的一些示例：

- 管理存储帐户的访问权限
- 创建、更新或删除 blob 容器
- 删除资源组及其所有资源

数据不会继承管理访问权限。 此分隔可防止带通配符 (`*`) 的角色无限制地访问数据。 例如，如果用户对订阅具有[读者](built-in-roles.md#reader)角色，则他们可以查看存储帐户，但他们无法查看基础数据。

## <a name="actions"></a>actions

`actions` 权限指定角色授权访问的管理操作。 它是操作字符串的集合，可标识 Azure 资源提供程序的安全对象操作。 下面是一些可以在 `actions` 中使用的管理操作的示例。

| 操作字符串    | 说明         |
| ------------------- | ------------------- |
| `*/read` | 向所有 Azure 资源提供程序的所有资源类型的读取操作授予访问权限。|
| `Microsoft.Compute/*` | 向 Microsoft.Compute 资源提供程序中的所有资源类型的所有操作授予访问权限。|
| `Microsoft.Network/*/read` | 向 Microsoft.Network 资源提供程序中的所有资源类型的读取操作授予访问权限。|
| `Microsoft.Compute/virtualMachines/*` | 向虚拟机及其子资源类型的所有操作授予访问权限。|
| `microsoft.web/sites/restart/Action` | 授予重启 Web 应用的访问权限。|

## <a name="notactions"></a>notActions

`notActions` 权限指定从允许的 `actions` 中排除的管理操作。 如果排除受限制的操作可以更方便地定义希望允许的操作集，则使用 `notActions` 权限。 通过从 `actions` 操作中减去 `notActions` 操作可以计算出角色授予的访问权限（有效权限）。

> [!NOTE]
> 如果用户分配到的一个角色排除了 `notActions` 中的一个操作，而分配到的第二个角色向同一操作授予访问权限，则用户可以执行该操作。 `notActions` 不是拒绝规则 - 它只是一个简便方法，可在需要排除特定操作时创建一组允许的操作。
>

## <a name="assignablescopes"></a>assignableScopes

`assignableScopes` 部分指定角色可供分配的范围（管理组（目前处于预览状态）、订阅、资源组或资源）。 可以让角色只在需要它的订阅或资源组中进行分配，而不影响其他订阅或资源组的用户体验。 必须使用至少一个管理组、订阅、资源组或资源 ID。

内置角色已将 `assignableScopes` 设置为根范围 (`"/"`)。 根范围指示角色可供在所有范围中进行分配。 不能在自己的自定义角色中使用根范围。 如果你尝试，会收到一个授权错误。

有效的可分配范围的示例包括：

| 场景 | 示例 |
|----------|---------|
| 角色可供在单个订阅中进行分配 | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| 角色可供在两个订阅中进行分配 | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| 角色只能在网络资源组中进行分配 | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| 角色可供在所有范围中进行分配 | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes 和自定义角色

自定义角色的 `assignableScopes` 部分还可以控制谁可以创建、删除、修改或查看自定义角色。

| 任务 | Operation | 说明 |
| --- | --- | --- |
| 创建/删除自定义角色 | `Microsoft.Authorization/ roleDefinition/write` | 在自定义角色的所有 `assignableScopes` 上被允许此操作的用户可以创建（或删除）用于这些范围的自定义角色。 例如，订阅、资源组和资源的[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)。 |
| 修改自定义角色 | `Microsoft.Authorization/ roleDefinition/write` | 在自定义角色的所有 `assignableScopes` 上被允许此操作的用户可以修改这些范围中的自定义角色。 例如，订阅、资源组和资源的[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)。 |
| 查看自定义角色 | `Microsoft.Authorization/ roleDefinition/read` | 在某个范围内被允许此操作的用户可以查看可在该范围内分配的自定义角色。 所有内置角色都允许自定义角色可用于分配。 |

## <a name="role-definition-examples"></a>角色定义示例

以下示例显示了使用 Azure CLI 显示的[读者](built-in-roles.md#reader)角色定义：

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

以下示例显示了使用 Azure PowerShell 显示的用于监视和重启虚拟机的自定义角色：

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
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
  "NotActions":  [

                 ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>另请参阅

* [内置角色](built-in-roles.md)
* [自定义角色](custom-roles.md)
* [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)
