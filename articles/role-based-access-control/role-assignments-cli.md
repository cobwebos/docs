---
title: 使用 Azure RBAC 和 Azure CLI 添加或删除角色分配
description: 了解如何使用 Azure 基于角色的访问控制（RBAC）和 Azure CLI 为用户、组、服务主体或托管标识授予对 Azure 资源的访问权限。
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b32df50715d5e7276861e0696df1bd6ceb3f684e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471986"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>使用 Azure RBAC 和 Azure CLI 添加或删除角色分配

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 本文介绍如何使用 Azure CLI 分配角色。

## <a name="prerequisites"></a>必备条件

若要添加或删除角色分配，必须具备：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)
- [Bash Azure Cloud Shell](/azure/cloud-shell/overview)或[Azure CLI](/cli/azure)

## <a name="get-object-ids"></a>获取对象 Id

若要添加或删除角色分配，则可能需要指定对象的唯一 ID。 ID 的格式为： `11111111-1111-1111-1111-111111111111`。 您可以使用 Azure 门户或 Azure CLI 获取该 ID。

### <a name="user"></a>用户

若要获取 Azure AD 用户的对象 ID，可以使用[az AD user show](/cli/azure/ad/user#az-ad-user-show)。

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>组

若要获取 Azure AD 组的对象 ID，可以使用[az ad group show](/cli/azure/ad/group#az-ad-group-show)或[az ad group list](/cli/azure/ad/group#az-ad-group-list)。

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>应用程序

若要获取 Azure AD 服务主体（应用程序使用的标识）的对象 ID，可以使用[az AD sp list](/cli/azure/ad/sp#az-ad-sp-list)。 对于服务主体，请使用对象 ID，而**不**是应用程序 id。

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>添加角色分配

在 RBAC 中，若要授予访问权限，请添加角色分配。

### <a name="user-at-a-resource-group-scope"></a>资源组范围内的用户

若要为资源组范围内的用户添加角色分配，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

下面的示例将*虚拟机参与者*角色分配给*医药*资源组作用域上的*patlong\@contoso.com*用户：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>使用唯一角色 ID

角色名称可能会发生变化，例如：

- 你使用的是自己的自定义角色，并决定更改该名称。
- 你使用的是名称中包含 **（预览版）** 的预览角色。 当角色被释放时，角色会重命名。

> [!IMPORTANT]
> 提供的预览版本没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

即使重命名了角色，角色 ID 也不会更改。 如果要使用脚本或自动化来创建角色分配，最佳做法是使用唯一角色 ID，而不是角色名称。 因此，如果重命名了某个角色，则脚本将更有可能运行。

若要使用唯一角色 ID 而不是角色名称添加角色分配，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

下面的示例将[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色分配给*医药*资源组作用域上的*patlong\@contoso.com*用户。 若要获取唯一角色 ID，可以使用[az role definition list](/cli/azure/role/definition#az-role-definition-list)或参阅[Azure 资源的内置角色](built-in-roles.md)。

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>订阅范围内的组

若要为组添加角色分配，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。 有关如何获取组的对象 ID 的信息，请参阅[获取对象](#get-object-ids)id。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

下面的示例将*读者*角色分配给订阅范围内 ID 为22222222-2222-2222-2222-222222222222 的*王 Mack 团队*组。

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>资源范围内的组

若要为组添加角色分配，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。 有关如何获取组的对象 ID 的信息，请参阅[获取对象](#get-object-ids)id。

下面的示例将*虚拟机参与者*角色分配给名为*医药*的虚拟网络的资源范围内 ID 为22222222-2222-2222-2222-222222222222 的*王 Mack 团队*组。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>资源组范围内的应用程序

若要为应用程序添加角色分配，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。 有关如何获取应用程序的对象 ID 的信息，请参阅[获取对象](#get-object-ids)id。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

以下示例在*医药*资源组范围内将*虚拟机参与者*角色分配到对象 ID 为44444444-4444-4444-4444-444444444444 的应用程序。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>订阅范围内的用户

若要为订阅范围内的用户添加角色分配，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。 若要获取订阅 ID，可以在 Azure 门户中的 "**订阅**" 边栏选项卡上找到，也可以使用[az account list](/cli/azure/account#az-account-list)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

下面的示例将*读取*者角色分配给订阅范围内的*annm\@example.com*用户。

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>管理组范围内的用户

若要为管理组范围内的用户添加角色分配，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。 若要获取管理组 ID，你可以在 Azure 门户的 "**管理组**" 边栏选项卡中找到它，也可以使用[az account management-group list](/cli/azure/account/management-group#az-account-management-group-list)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

下面的示例将*计费读取*者角色分配给管理组作用域上的*alain\@example.com*用户。

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>新服务主体

如果创建新的服务主体并立即尝试将角色分配给该服务主体，则在某些情况下该角色分配可能会失败。 例如，如果使用脚本创建新的托管标识，然后尝试将角色分配给该服务主体，则角色分配可能会失败。 此故障的原因可能是复制延迟。 在一个区域中创建服务主体;但是，角色分配可能发生在尚未复制服务主体的其他区域中。 若要解决这种情况，应在创建角色分配时指定主体类型。

若要添加角色分配，请使用[az role 分配 create](/cli/azure/role/assignment#az-role-assignment-create)，为 `--assignee-object-id`指定一个值，然后将 `--assignee-principal-type` 设置为 `ServicePrincipal`。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

以下示例在*医药*资源组范围内将*虚拟机参与者*角色分配给*msi 测试*托管标识：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>删除角色分配

在 RBAC 中，若要删除访问权限，请使用 [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) 删除角色分配：

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

以下示例从*医药*资源组上的*patlong\@Contoso.com*用户删除*虚拟机参与者*角色分配：

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

下面的示例从订阅范围内的 ID 为22222222-2222-2222-2222-222222222222 的*王 Mack 团队*组中删除*Reader*角色。 有关如何获取组的对象 ID 的信息，请参阅[获取对象](#get-object-ids)id。

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

以下示例从管理组作用域中的*alain\@example.com*用户删除*计费读取*者角色。 若要获取管理组的 ID，你可以使用[az account management-group list](/cli/azure/account/management-group#az-account-management-group-list)。

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>后续步骤

- [使用 Azure RBAC 和 Azure CLI 列出角色分配](role-assignments-list-cli.md)
- [使用 Azure CLI 管理 Azure 资源和资源组](../azure-resource-manager/cli-azure-resource-manager.md)
