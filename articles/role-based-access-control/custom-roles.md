---
title: Azure 中的自定义角色 | Microsoft Docs
description: 了解如何使用 Azure 基于角色的访问控制 (RBAC) 定义自定义角色，以便对 Azure 中的资源进行精细的访问权限管理。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 074c305cb15bc1fb25dfa5cfc52dcce53b661a7e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36324182"
---
# <a name="custom-roles-in-azure"></a>Azure 中的自定义角色

如果[内置角色](built-in-roles.md)不能满足组织的特定需求，你可以创建自己的自定义角色。 与内置角色一样，可以将自定义角色分配到订阅、资源组和资源范围内的用户、组和服务主体。 自定义角色存储在 Azure Active Directory (Azure AD) 租户中，可以在订阅之间共享。 每个租户最多可以有 2000 个自定义角色。 可以使用 Azure PowerShell、Azure CLI 或 REST API 创建自定义角色。

## <a name="custom-role-example"></a>自定义角色示例

下面提供了使用 Azure PowerShell 显示的用于监视和重启虚拟机的自定义角色：

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
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

创建自定义角色后，该角色会显示在 Azure 门户中，并带有一个橙色资源图标。

![自定义角色图标](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>创建自定义角色的步骤

1. 确定所需的权限

    创建自定义角色时，需要知道可用于定义权限的资源提供程序操作。 若要查看操作列表，可以使用 [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) 或 [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list) 命令。
    若要指定自定义角色的权限，请将操作添加到[角色定义](role-definitions.md)的 `actions` 或 `notActions` 属性。 如果有数据操作，请将这些操作添加到 `dataActions` 或 `notDataActions` 属性。

2. 创建自定义角色

    可以使用 Azure PowerShell 或 Azure CLI 创建自定义角色。 通常，我们会从一个现有的内置角色着手，并根据需要对其进行修改。 然后，使用 [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) 或 [az role definition create](/cli/azure/role/definition#az-role-definition-create) 命令创建自定义角色。 若要创建自定义角色，必须拥有所有 `assignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 权限，例如[所有者](built-in-roles.md#owner)或[用户访问权限管理员](built-in-roles.md#user-access-administrator)。

3. 测试自定义角色

    创建自定义角色后，必须对其进行测试，以验证它是否按预期工作。 如果需要做出调整，可以更新自定义角色。

## <a name="custom-role-properties"></a>自定义角色属性

自定义角色具有以下属性。

| 属性 | 必选 | Type | 说明 |
| --- | --- | --- | --- |
| `Name` | 是 | String | 自定义角色的显示名称。 此名称必须在租户中唯一。 可以包含字母、数字、空格和特殊字符。 最多包含 128 个字符。 |
| `Id` | 是 | String | 自定义角色的唯一 ID。 如果使用 Azure PowerShell 和 Azure CLI，在创建新角色时会自动生成此 ID。 |
| `IsCustom` | 是 | String | 指示此角色是否为自定义角色。 设置为 `true` 表示是自定义角色。 |
| `Description` | 是 | String | 自定义角色的说明。 可以包含字母、数字、空格和特殊字符。 最多包含 1024 个字符。 |
| `Actions` | 是 | String[] | 一个字符串数组，指定该角色允许执行的管理操作。 有关详细信息，请参阅 [actions](role-definitions.md#actions)。 |
| `NotActions` | 否 | String[] | 一个字符串数组，指定要从允许的 `actions` 中排除的管理操作。 有关详细信息，请参阅 [notActions](role-definitions.md#notactions)。 |
| `DataActions` | 否 | String[] | 一个字符串数组，指定该角色允许对该对象中的数据执行的数据操作。 有关详细信息，请参阅 [dataActions（预览）](role-definitions.md#dataactions-preview)。 |
| `NotDataActions` | 否 | String[] | 一个字符串数组，指定要从允许的 `dataActions` 中排除的数据操作。 有关详细信息，请参阅 [notDataActions（预览）](role-definitions.md#notdataactions-preview)。 |
| `AssignableScopes` | 是 | String[] | 一个字符串数组，指定自定义角色的可分配范围。 不能设置为根范围 (`"/"`)。 有关详细信息，请参阅 [assignableScopes](role-definitions.md#assignablescopes)。 |

## <a name="assignablescopes-for-custom-roles"></a>自定义角色的 assignableScopes

与在内置角色中一样，`assignableScopes` 属性指定角色的可配置范围。 但是，你不能在自己的自定义角色中使用根范围 (`"/"`)。 如果你尝试，会收到一个授权错误。 自定义角色的 `assignableScopes` 属性还控制谁可以创建、删除、修改或查看自定义角色。

| 任务 | Operation | 说明 |
| --- | --- | --- |
| 创建/删除自定义角色 | `Microsoft.Authorization/ roleDefinition/write` | 在自定义角色的所有 `assignableScopes` 上被允许此操作的用户可以创建（或删除）用于这些范围的自定义角色。 例如，订阅、资源组和资源的[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)。 |
| 修改自定义角色 | `Microsoft.Authorization/ roleDefinition/write` | 在自定义角色的所有 `assignableScopes` 上被允许此操作的用户可以修改这些范围中的自定义角色。 例如，订阅、资源组和资源的[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)。 |
| 查看自定义角色 | `Microsoft.Authorization/ roleDefinition/read` | 在某个范围内被允许此操作的用户可以查看可在该范围内分配的自定义角色。 所有内置角色都允许自定义角色可用于分配。 |

## <a name="next-steps"></a>后续步骤
- [使用 Azure PowerShell 创建自定义角色](custom-roles-powershell.md)
- [使用 Azure CLI 创建自定义角色](custom-roles-cli.md)
- [了解角色定义](role-definitions.md)
