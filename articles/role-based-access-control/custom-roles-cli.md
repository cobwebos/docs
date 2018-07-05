---
title: 使用 Azure CLI 创建自定义角色 | Microsoft Docs
description: 了解如何使用 Azure CLI 为基于角色的访问控制 (RBAC) 创建自定义角色。 这包括如何列出、创建、更新和删除自定义角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b1df50c73497e3f5ad64a7ba7210079871b155e0
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36324172"
---
# <a name="create-custom-roles-using-azure-cli"></a>使用 Azure CLI 创建自定义角色

如果[内置角色](built-in-roles.md)不能满足你的组织的特定需求，可以创建你自己的自定义角色。 本文介绍如何使用 Azure CLI 创建和管理自定义角色。

## <a name="prerequisites"></a>先决条件

若要创建自定义角色，需要具备以下条件：

- 有权创建自定义角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)
- 在本地安装 [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>列出自定义角色

若要列出可用于分配的自定义角色，请使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list)。 以下示例列出了当前订阅中的所有自定义角色。

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="create-a-custom-role"></a>创建自定义角色

若要创建自定义角色，请使用 [az role definition create](/cli/azure/role/definition#az-role-definition-create)。 角色定义可以是 JSON 说明，也可以是包含 JSON 说明的文件的路径。

```azurecli
az role definition create --role-definition <role_definition>
```

以下示例创建名为“虚拟机操作员”的自定义角色。 该自定义角色分配访问 *Microsoft.Compute*、*Microsoft.Storage* 和 *Microsoft.Network* 资源提供程序的所有读取操作的权限，并分配访问虚拟机启动、重启和监视操作的权限。 该自定义角色可以在两个订阅中使用。 此示例将 JSON 文件用作输入。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
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
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>更新自定义角色

若要更新自定义角色，请首先使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list) 检索角色定义。 然后，对该角色定义作出所需更改。 最后，使用 [az role definition update](/cli/azure/role/definition#az-role-definition-update) 保存更新的角色定义。

```azurecli
az role definition update --role-definition <role_definition>
```

以下示例将 *Microsoft.Insights/diagnosticSettings/* 操作添加到“虚拟机操作员”自定义角色的“Actions”。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
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
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>删除自定义角色

若要删除自定义角色，请使用 [az role definition delete](/cli/azure/role/definition#az-role-definition-delete)。 若要指定要删除的角色，请使用角色名称或角色 ID。 若要确定角色 ID，请使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list)。

```azurecli
az role definition delete --name <role_name or role_id>
```

以下示例删除了“虚拟机操作员”自定义角色。

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure CLI 创建自定义角色](tutorial-custom-role-cli.md)
- [Azure 中的自定义角色](custom-roles.md)
- [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)