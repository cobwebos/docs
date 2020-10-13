---
title: 了解 Azure RBAC 的作用域
description: 了解 Azure RBAC) 的 Azure 基于角色的访问 (控制的作用域，以及如何确定资源的作用域（& a）。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: ad906e3665c6ffc354cf6292c2559d1184037594
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856138"
---
# <a name="understand-scope-for-azure-rbac"></a>了解 Azure RBAC 的作用域

*作用域* 是指 access 适用的资源集。 分配角色时，请务必了解作用域，以便能够仅授予安全主体真正需要的访问权限。 通过限制作用域，如果安全主体泄露，则限制有风险的资源。

## <a name="scope-levels"></a>范围级别

在 Azure 中，可以指定以下四个级别的作用域： [管理组](../governance/management-groups/overview.md)、订阅、 [资源组](../azure-resource-manager/management/overview.md#resource-groups)和资源。 范围采用父子关系结构。 层次结构的每个级别都使范围更具针对性。 可以在其中任何一个范围级别分配角色。 你选择的级别决定了角色的应用范围。 较低级别继承更高级别的角色权限。 

![角色分配的范围](./media/scope-overview/rbac-scope-no-label.png)

管理组是订阅的范围级别，但管理组支持更复杂的层次结构。 下图显示了可以定义的管理组和订阅的层次结构的示例。 有关管理组的详细信息，请参阅 [什么是 Azure 管理组？](../governance/management-groups/overview.md)。

![管理组和订阅层次结构](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>作用域格式

如果使用命令行添加角色分配，则需要指定作用域。 对于命令行工具，作用域是一种可能的长字符串，用于标识角色分配的确切范围。 在 Azure 门户中，此作用域通常作为 *资源 ID*列出。

范围由一系列标识符（由斜杠 (/) 字符分隔）组成。 可以将此字符串视为表示以下层次结构，其中没有占位符的文本 (`{}`) 是固定标识符：

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` 是要使用的订阅的 ID (GUID)。
- `{resourcesGroupName}` 是包含资源组的名称。
- `{providerName}` 处理资源的 [资源提供程序](../azure-resource-manager/management/azure-services-resource-providers.md) 的名称，然后在 `{resourceType}` `{resourceSubType*}` 该资源提供程序中标识更多级别。
- `{resourceName}` 是标识特定资源的字符串的最后一部分。

管理组是高于订阅的级别，具有最广泛 (最不具体的) 范围。 此级别的角色分配适用于管理组中的订阅。 管理组的作用域具有以下格式：

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>作用域示例

> [!div class="mx-tableFixed"]
> | 范围 | 示例 |
> | --- | --- |
> | 管理组 | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | 订阅 | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | 资源组 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | 资源 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>如何确定资源的作用域

确定管理组、订阅或资源组的范围非常简单。 你只需知道名称和订阅 ID。 不过，确定资源的作用域会花一些时间。 可以通过以下几种方式确定资源的作用域。

- 在 Azure 门户中，打开资源，然后查看属性。 资源应列出可用于确定作用域的 **资源 ID** 。 例如，以下是存储帐户的资源 Id。

    ![Azure 门户中的存储帐户的资源 Id](./media/scope-overview/scope-resource-id.png)

- 另一种方法是使用 Azure 门户在资源范围内临时分配角色，然后使用 [Azure PowerShell](role-assignments-list-powershell.md) 或 [Azure CLI](role-assignments-list-cli.md) 来列出角色分配。 在输出中，作用域将作为一个属性列出。

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>后续步骤

- [角色分配的添加步骤](role-assignments-steps.md)
- [Azure 服务的资源提供程序](../azure-resource-manager/management/azure-services-resource-providers.md)
- [什么是 Azure 管理组？](../governance/management-groups/overview.md)
