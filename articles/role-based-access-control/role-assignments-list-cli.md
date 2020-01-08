---
title: 使用 Azure RBAC 和 Azure CLI 列出角色分配
description: 了解如何使用 Azure 基于角色的访问控制（RBAC）和 Azure CLI 来确定用户、组、服务主体或托管标识有权访问哪些资源。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 12ecca5873ac7c2c3bfa30d4c73c7d8e268aabfb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355723"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>使用 Azure RBAC 和 Azure CLI 列出角色分配

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文介绍了如何使用 Azure CLI 列出角色分配。

## <a name="prerequisites"></a>必备组件

- [Bash Azure Cloud Shell](/azure/cloud-shell/overview)或[Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>为用户列出角色分配

若要列出特定用户的角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --assignee <assignee>
```

默认情况下，只会显示当前订阅的角色分配。 若要查看当前订阅和下面的角色分配，请添加 `--all` 参数。 若要查看继承的角色分配，请添加 `--include-inherited` 参数。

以下示例列出了直接分配给*patlong\@contoso.com*用户的角色分配：

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>列出资源组的角色分配

若要列出资源组作用域中存在的角色分配，请使用[az role 指配 list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --resource-group <resource_group>
```

以下示例列出了*医药*资源组的角色分配：

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>列出订阅的角色分配

若要列出订阅范围内的所有角色分配，请使用[az role 分配 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要获取订阅 ID，可以在 Azure 门户中的 "**订阅**" 边栏选项卡上找到，也可以使用[az account list](/cli/azure/account#az-account-list)。

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>列出管理组的角色分配

若要列出管理组作用域的所有角色分配，请使用[az role 分配 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要获取管理组 ID，你可以在 Azure 门户的 "**管理组**" 边栏选项卡中找到它，也可以使用[az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)。

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>列出托管标识的角色分配

1. 获取系统分配的或用户分配的托管标识的对象 ID。 

    若要获取用户分配的托管标识的对象 ID，可以使用[az ad sp list](/cli/azure/ad/sp#az-ad-sp-list)或[az identity list](/cli/azure/identity#az-identity-list)。

    ```azurecli
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    若要获取系统分配的托管标识的对象 ID，可以使用[az ad sp list](/cli/azure/ad/sp#az-ad-sp-list)。

    ```azurecli
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. 若要列出角色分配，请使用[az role 分配 list](/cli/azure/role/assignment#az-role-assignment-list)。

    默认情况下，只会显示当前订阅的角色分配。 若要查看当前订阅和下面的角色分配，请添加 `--all` 参数。 若要查看继承的角色分配，请添加 `--include-inherited` 参数。

    ```azurecli
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>后续步骤

- [使用 Azure RBAC 和 Azure CLI 添加或删除角色分配](role-assignments-cli.md)
