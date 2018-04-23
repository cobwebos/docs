---
title: 使用 Azure CLI 管理基于角色的访问控制 (RBAC) | Microsoft Docs
description: 通过列出角色和角色操作、将角色分配到订阅和应用程序范围来了解如何使用 Azure 命令行接口管理基于角色的访问控制 (RBAC)。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/03/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 9a4489c575de9f63740c68bda8cbf921592402ec
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>使用 Azure 命令行接口管理基于角色的访问控制

> [!div class="op_single_selector"]
> * [PowerShell](role-assignments-powershell.md)
> * [Azure CLI](role-assignments-cli.md)
> * [REST API](role-assignments-rest.md)


使用基于角色的访问控制 (RBAC) 时，可以通过分配特定范围的角色，为用户、组和服务主体定义访问权限。 本文介绍如何使用 Azure 命令行接口 (CLI) 来管理角色分配。

## <a name="prerequisites"></a>先决条件

若要使用 Azure CLI 管理角色分配，必须具备以下先决条件：

* [Azure CLI 2.0](/cli/azure)。 可以通过 [Azure Cloud Shell](../cloud-shell/overview.md) 在浏览器中使用它，也可以将其[安装](/cli/azure/install-azure-cli)在 macOS、Linux 和 Windows 上，然后从命令行运行它。

## <a name="list-role-definitions"></a>列出角色定义

若要列出所有可用的角色定义，请使用 [az role definition list](/cli/azure/role/definition#az_role_definition_list)：

```azurecli
az role definition list
```

以下示例列出了所有可用的角色定义的名称和说明：

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

下面的示例列出了所有内置的角色定义：

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role-definition"></a>列出角色定义的操作

若要列出角色定义的操作，请使用 [az role definition list](/cli/azure/role/definition#az_role_definition_list)：

```azurecli
az role definition list --name <role_name>
```

下面的示例列出了“参与者”角色定义：

```azurecli
az role definition list --name "Contributor"
```

```Output
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

以下示例列出了“参与者”角色的 *actions* 和 *notActions*：

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

以下示例列出了“虚拟机参与者”角色的 actions：

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-role-assignments"></a>列出角色分配

### <a name="list-role-assignments-for-a-user"></a>为用户列出角色分配

若要列出特定用户的角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)：

```azurecli
az role assignment list --assignee <assignee>
```

默认情况下，只会列出局限于订阅的分配。 若要查看按资源或组来确定范围的分配，请使用 `--all`。

以下示例列出的角色分配是直接分配给 *patlong@contoso.com* 用户的：

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>列出资源组的角色分配

若要列出存在的针对资源组的角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)：

```azurecli
az role assignment list --resource-group <resource_group>
```

以下示例列出 *pharma-sales-projectforecast* 资源组的角色分配：

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="create-role-assignments"></a>创建角色分配

### <a name="create-a-role-assignment-for-a-user"></a>为用户创建角色分配

若要为资源组范围的用户创建角色分配，请使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)：

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

以下示例将“虚拟机参与者”角色分配给 *pharma-sales-projectforecast* 资源组范围内的 *patlong@contoso.com* 用户：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>为组创建角色分配

若要为组创建角色分配，请使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)：

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

以下示例将“读者”角色分配给订阅范围内 ID 为 22222222-2222-2222-2222-222222222222 的“Ann Mack 团队”组。 若要获取组的 ID，可以使用 [az ad group list](/cli/azure/ad/group#az_ad_group_list) 或 [az ad group show](/cli/azure/ad/group#az_ad_group_show)。

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

以下示例将“虚拟机参与者”角色分配给名为 *pharma-sales-project-network* 的虚拟网络的资源范围内 ID 为 22222222-2222-2222-2222-222222222222 的“Ann Mack 团队”组：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>为应用程序创建角色分配

若要为应用程序创建角色，请使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)：

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

以下示例将“虚拟机参与者”角色分配给 *pharma-sales-projectforecast* 资源组范围内对象 ID 为 44444444-4444-4444-4444-444444444444 的应用程序。 若要获取应用程序的对象 ID，可以使用 [az ad app list](/cli/azure/ad/app#az_ad_app_list) 或 [az ad app show](/cli/azure/ad/app#az_ad_app_show)。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-a-role-assignment"></a>删除角色分配

若要删除角色分配，请使用 [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete)：

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

以下示例在 *pharma-sales-projectforecast* 资源组上从 *patlong@contoso.com* 用户删除“虚拟机参与者”角色分配：

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

以下示例将“读者”角色从订阅范围内 ID 为 22222222-2222-2222-2222-222222222222 的“Ann Mack 团队”组删除。 若要获取组的 ID，可以使用 [az ad group list](/cli/azure/ad/group#az_ad_group_list) 或 [az ad group show](/cli/azure/ad/group#az_ad_group_show)。

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>自定义角色

### <a name="list-custom-roles"></a>列出自定义角色

若要列出可在某范围内进行分配的角色，请使用 [az role definition list](/cli/azure/role/definition#az_role_definition_list)。

下面的两个示例都列出了当前订阅中的所有自定义角色：

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
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>创建自定义角色

若要创建自定义角色，请使用 [az role definition create](/cli/azure/role/definition#az_role_definition_create)。 角色定义可以是 JSON 说明，也可以是包含 JSON 说明的文件的路径。

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

### <a name="update-a-custom-role"></a>更新自定义角色

若要更新自定义角色，请首先使用 [az role definition list](/cli/azure/role/definition#az_role_definition_list) 检索角色定义。 然后，对该角色定义作出所需更改。 最后，使用 [az role definition update](/cli/azure/role/definition#az_role_definition_update) 保存更新的角色定义。

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

### <a name="delete-a-custom-role"></a>删除自定义角色

若要删除自定义角色，请使用 [az role definition delete](/cli/azure/role/definition#az_role_definition_delete)。 若要指定要删除的角色，请使用角色名称或角色 ID。 若要确定角色 ID，请使用 [az role definition list](/cli/azure/role/definition#az_role_definition_list)。

```azurecli
az role definition delete --name <role_name or role_id>
```

以下示例删除了“虚拟机操作员”自定义角色：

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

