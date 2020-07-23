---
title: 教程：使用 Azure CLI 创建 Azure 自定义角色 - Azure RBAC
description: 在本教程中，我们从使用 Azure CLI 和 Azure 基于角色的访问控制 (Azure RBAC) 创建 Azure 自定义角色着手。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: 3efe9670d03a754cc90689c4b66d0843de4e275f
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735498"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-cli"></a>教程：使用 Azure CLI 创建 Azure 自定义角色

如果 [Azure 内置角色](built-in-roles.md)不满足组织的特定需求，你可以创建自己的自定义角色。 对于本教程，你将使用 Azure CLI 创建名为 Reader Support Tickets 的自定义角色。 该自定义角色允许用户在订阅的管理平面中查看所有内容，以及创建支持票证。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建自定义角色
> * 列出自定义角色
> * 更新自定义角色
> * 删除自定义角色

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要：

- 有权创建自定义角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) 或 [Azure CLI](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure-cli"></a>登录 Azure CLI

登录到 [Azure CLI](/cli/azure/authenticate-azure-cli)。

## <a name="create-a-custom-role"></a>创建自定义角色

创建自定义角色的最简单方法是从 JSON 模板着手，添加你的更改，然后创建新角色。

1. 查看适用于 [Microsoft.Support 资源提供程序的](resource-provider-operations.md#microsoftsupport)操作列表。 这有助于了解可用来创建你的权限的操作。

    | Operation | 说明 |
    | --- | --- |
    | Microsoft.Support/register/action | 注册到支持资源提供程序 |
    | Microsoft.Support/supportTickets/read | 获取支持票证详细信息（包括状态、严重性、联系详细信息和通信），或获取各个订阅中的支持票证列表。 |
    | Microsoft.Support/supportTickets/write | 创建或更新支持票证。 可以针对技术、计费、配额或订阅管理相关的问题创建支持票证。 可以更新现有支持票证的严重性、联系详细信息和通信。 |
    
1. 创建一个名为 **ReaderSupportRole.json** 的新文件。

1. 在编辑器中打开 ReaderSupportRole.json 并添加以下 JSON。

    有关不同属性的信息，请参阅 [Azure 自定义角色](custom-roles.md)。

    ```json
    {
      "Name": "",
      "IsCustom": true,
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId1}"
      ]
    }
    ```
    
1. 将以下将操作添加到 `Actions` 属性。 这些操作允许用户查看订阅中的所有内容，以及创建支持票证。

    ```
    "*/read",
    "Microsoft.Support/*"
    ```

1. 使用 [az account list](/cli/azure/account#az-account-list) 命令获取你的订阅 ID。

    ```azurecli
    az account list --output table
    ```

1. 在 `AssignableScopes` 中，将 `{subscriptionId1}` 替换为你的订阅 ID。

    必须添加显式的订阅 ID，否则将不允许将角色导入到订阅中。

1. 将 `Name` 和 `Description` 属性更改为 "Reader Support Tickets" 和 "View everything in the subscription and also open support tickets"。

    JSON 文件应如下所示：

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. 若要新建自定义角色，请使用 [az role definition create](/cli/azure/role/definition#az-role-definition-create) 命令，并指定 JSON 角色定义文件。

    ```azurecli
    az role definition create --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

    现在，新的自定义角色可用，并可分配给用户、组或服务主体，就像内置角色一样。

## <a name="list-custom-roles"></a>列出自定义角色

- 若要列出所有自定义角色，请使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list) 命令及 `--custom-role-only` 参数。

    ```azurecli
    az role definition list --custom-role-only true
    ```
    
    ```Output
    [
      {
        "additionalProperties": {},
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ],
        "description": "View everything in the subscription and also open support tickets.",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
        "name": "22222222-2222-2222-2222-222222222222",
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Support/*",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Insights/diagnosticSettings/*/read"
            ],
            "additionalProperties": {},
            "dataActions": [],
            "notActions": [],
            "notDataActions": []
          }
        ],
        "roleName": "Reader Support Tickets",
        "roleType": "CustomRole",
        "type": "Microsoft.Authorization/roleDefinitions"
      }
    ]
    ```
    
    还可以在 Azure 门户中查看自定义角色。

    ![Azure 门户中导入的自定义角色屏幕截图](./media/tutorial-custom-role-cli/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>更新自定义角色

若要更新自定义角色，请更新 JSON 文件，然后更新自定义角色。

1. 打开 ReaderSupportRole.json 文件。

1. 在 `Actions` 中，添加用于创建和管理资源组部署 `"Microsoft.Resources/deployments/*"` 的操作。 请确保在上一操作后包括一个逗号。

    更新后的 JSON 文件应如下所示：

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. 若要更新自定义角色，请使用 [az role definition update](/cli/azure/role/definition#az-role-definition-update) 命令并指定更新后的 JSON 文件。

    ```azurecli
    az role definition update --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*",
            "Microsoft.Resources/deployments/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```
    
## <a name="delete-a-custom-role"></a>删除自定义角色

- 使用 [az role definition delete](/cli/azure/role/definition#az-role-definition-delete) 命令并指定角色名称或角色 ID 来删除自定义角色。

    ```azurecli
    az role definition delete --name "Reader Support Tickets"
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure CLI 创建或更新 Azure 自定义角色](custom-roles-cli.md)