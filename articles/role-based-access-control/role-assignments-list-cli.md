---
title: 使用 Azure CLI 列出 Azure 角色分配-Azure RBAC
description: 了解如何使用 Azure CLI 和 Azure 基于角色的访问控制（Azure RBAC）确定用户、组、服务主体或托管标识有权访问哪些资源。
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f4b635d6867c36b8b0f385320e3720bea41b54d1
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735736"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>使用 Azure CLI 列出 Azure 角色分配

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文介绍如何使用 Azure CLI 列出角色分配。

> [!NOTE]
> 如果你的组织对使用[Azure 委托资源管理](../lighthouse/concepts/azure-delegated-resource-management.md)的服务提供商具有外包管理功能，则此处将不会显示该服务提供商授权的角色分配。

## <a name="prerequisites"></a>先决条件

- [Bash Azure Cloud Shell](/azure/cloud-shell/overview)或[Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>为用户列出角色分配

若要列出特定用户的角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli-interactive
az role assignment list --assignee <assignee>
```

默认情况下，将仅显示当前订阅的角色分配。 若要查看当前订阅和以下订阅的角色分配，请添加 `--all` 参数。 若要查看继承的角色分配，请添加 `--include-inherited` 参数。

以下示例列出的角色分配直接分配给 *patlong\@contoso.com* 用户：

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

若要列出存在于资源组范围的角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

以下示例列出 *pharma-sales* 资源组的角色分配：

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
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

若要列出订阅范围内的所有角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)。 若要获取订阅 ID，可以在 Azure 门户中的“订阅”**** 边栏选项卡上找到它，也可以使用 [az account list](/cli/azure/account#az-account-list)。

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

示例：

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>列出管理组的角色分配

若要列出管理组范围内的所有角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)。 若要获取管理组 ID，可以在 Azure 门户中的“管理组”**** 边栏选项卡上找到它，也可以使用 [az account management-group list](/cli/azure/account/management-group#az-account-management-group-list)。

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

示例：

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>列出托管标识的角色分配

1. 获取系统分配的或用户分配的托管标识的对象 ID。

    若要获取用户分配的托管标识的对象 ID，可以使用 [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) 或 [az identity list](/cli/azure/identity#az-identity-list)。

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    若要获取系统分配的托管标识的对象 ID，可以使用 [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list)。

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. 若要列出角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)。

    默认情况下，将仅显示当前订阅的角色分配。 若要查看当前订阅和以下订阅的角色分配，请添加 `--all` 参数。 若要查看继承的角色分配，请添加 `--include-inherited` 参数。

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 添加或删除 Azure 角色分配](role-assignments-cli.md)
