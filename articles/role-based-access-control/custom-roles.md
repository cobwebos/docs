---
title: 为 Azure RBAC 创建自定义角色 | Microsoft Docs
description: 了解如何通过 Azure 基于角色的访问控制来定义自定义角色，以便在 Azure 订阅中进行更精确的身份管理。
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
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e2ea46ea1a6b5bd3f50d4d4c15492c16c5241c0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161050"
---
# <a name="create-custom-roles-in-azure"></a>在 Azure 中创建自定义角色

如果[内置角色](built-in-roles.md)不能满足特定访问需要，则可创建自己的自定义角色。 与内置角色一样，可以将自定义角色分配到订阅、资源组和资源范围内的用户、组和服务主体。 自定义角色存储在 Azure Active Directory (Azure AD) 租户中，可以在订阅之间共享。 可以使用 Azure PowerShell、Azure CLI 或 REST API 创建自定义角色。 本文介绍如何开始使用 PowerShell 和 Azure CLI 创建自定义角色的示例。

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>使用 PowerShell 创建自定义角色以提出支持请求

若要创建自定义角色，可以从内置角色着手，对其进行编辑，然后创建新角色。 对于此示例，内置[读取者](built-in-roles.md#reader)角色经过自定义，用于创建名为“读取者支持票证访问级别”的自定义角色。 此角色允许用户订阅中的所有内容，以及建立支持请求。

> [!NOTE]
> 允许用户建立支持请求的唯一两个内置角色是“所有者”和“参与者”。[](built-in-roles.md#owner)[](built-in-roles.md#contributor) 若使某个用户能够提出支持请求，必须在订阅范围为该用户分配角色，因为所有支持请求都是基于 Azure 订阅创建的。

在 PowerShell 中，使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) 命令导出 JSON 格式的“读取者”角色。[](built-in-roles.md#reader)

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

以下是[读取者](built-in-roles.md#reader)角色的 JSON 输出。 典型角色由三个主要部分组成：`Actions`、`NotActions` 和 `AssignableScopes`。 `Actions` 部分列出允许此角色执行的所有操作。 若要从 `Actions` 中排除操作，可将其添加到 `NotActions`。 通过从 `Actions` 操作中减去 `NotActions` 操作可以计算出有效权限。

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

接下来，编辑 JSON 输出以创建自定义角色。 在本例中，若要创建支持票证，必须添加 `Microsoft.Support/*` 操作。 每个操作都是通过资源提供程序提供的。 若要获取资源提供程序的操作列表，可以使用 [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) 命令，或参阅 [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)。

角色必须包含它所应用到的显式订阅 ID。 订阅 ID 必须列在 `AssignableScopes` 下面，否则无法在订阅中导入角色。

最后，必须将 `IsCustom` 属性设置为 `true` 来指定这是自定义角色。

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

若要新建自定义角色，请使用 [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) 命令，并提供更新的 JSON 角色定义文件。

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

运行 [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) 后，新的自定义角色可在 Azure 门户中使用，并可分配给用户。

![Azure 门户中导入的自定义角色屏幕截图](./media/custom-roles/18.png)

![将导入的自定义角色分配到同一目录中的用户的屏幕截图](./media/custom-roles/19.png)

![导入的自定义角色的权限屏幕截图](./media/custom-roles/20.png)

具有此自定义角色的用户可以创建新的支持请求。

![可创建支持请求的自定义角色的屏幕截图](./media/custom-roles/21.png)

具有此自定义角色的用户无法执行其他操作，例如创建 VM 或创建资源组。

![无法创建 VM 的自定义角色的屏幕截图](./media/custom-roles/22.png)

![无法创建新资源组的自定义角色的屏幕截图](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>使用 Azure CLI 创建自定义角色以提出支持请求

使用 Azure CLI 创建自定义角色的步骤类似于使用 PowerShell，只是 JSON 输出有所不同。

对于此示例，可以从内置的“读取者”角色着手。[](built-in-roles.md#reader) 若要列出[读取者](built-in-roles.md#reader)角色的操作，请使用 [az role definition list](/cli/azure/role/definition#az_role_definition_list) 命令。

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
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

使用以下格式创建 JSON 文件。 `Microsoft.Support/*` 操作已添加到 `Actions` 部分，以便此用户可以提出支持请求，同时继续充当读取者角色。 必须在 `AssignableScopes` 部分中添加此角色将要应用到的订阅 ID。

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

若要新建自定义角色，请使用 [az role definition create](/cli/azure/role/definition#az_role_definition_create) 命令。

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

现在，新角色可在 Azure 门户中使用，其使用过程与前面 PowerShell 部分中所述的过程相同。

![使用 CLI 1.0 创建自定义角色的 Azure 门户屏幕截图](./media/custom-roles/26.png)


## <a name="see-also"></a>另请参阅
- [了解角色定义](role-definitions.md)
- [使用 Azure PowerShell 管理基于角色的访问控制](role-assignments-powershell.md)
- [使用 Azure CLI 管理基于角色的访问控制](role-assignments-cli.md)
- [使用 REST API 管理基于角色的访问控制](role-assignments-rest.md)
