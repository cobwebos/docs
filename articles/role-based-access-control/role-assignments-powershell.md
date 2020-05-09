---
title: 使用 Azure PowerShell 添加或删除 Azure 角色分配-Azure RBAC
description: 了解如何使用 Azure PowerShell 和 Azure 基于角色的访问控制（Azure RBAC）为用户、组、服务主体或托管标识授予对 Azure 资源的访问权限。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: db6b38f142254fa1812f34674e6a870629713d7e
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735651"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>使用 Azure PowerShell 添加或删除 Azure 角色分配

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 本文介绍如何使用 Azure PowerShell 分配角色。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

若要添加或删除角色分配，必须拥有以下权限：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)
- Azure Cloud Shell 或[Azure PowerShell](/powershell/azure/install-az-ps) [中的 PowerShell](/azure/cloud-shell/overview)

## <a name="get-object-ids"></a>获取对象 ID

若要添加或删除角色分配，可能需要指定对象的唯一 ID。 ID 的格式为：`11111111-1111-1111-1111-111111111111`。 可以使用 Azure 门户或 Azure PowerShell 获取 ID。

### <a name="user"></a>User

若要获取 Azure AD 用户的对象 ID，可以使用 [Get-AzADUser](/powershell/module/az.resources/get-azaduser)。

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>组

若要获取 Azure AD 组的对象 ID，可以使用 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup)。

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>应用程序

若要获取 Azure AD 服务主体的对象ID（应用程序使用的标识），可以使用 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)。 对于服务主体，请使用对象 ID，而**不**是使用应用程序 ID。

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>添加角色分配

在 Azure RBAC 中，若要授予访问权限，请添加角色分配。

### <a name="user-at-a-resource-group-scope"></a>资源组范围内的用户

若要为资源组范围内的用户添加角色分配，请使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)。

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>使用唯一角色 ID

很多时候角色名称可能会更改，例如：

- 你使用的是自己的自定义角色，你决定更改名称。
- 你使用的是预览版角色，其名称中有“(预览)”字样。  发布角色时重命名了角色。

> [!IMPORTANT]
> 预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

即使重命名了角色，角色 ID 也不会更改。 如果使用脚本或自动化来创建角色分配，最佳做法是使用唯一的角色 ID 而非角色名称。 这样一来，即使角色重命名，脚本仍可以使用。

若要使用唯一角色 ID 而非角色名称来添加角色分配，请使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)。

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

以下示例将[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色分配给 *pharma-sales* 资源组范围内的 alain\@example.com  用户。 若要获取唯一角色 ID，可以使用[AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)或参阅[Azure 内置角色](built-in-roles.md)。

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>资源范围内的组

若要为资源范围内的组添加角色分配，请使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)。 有关如何获取组的对象 ID 的信息，请参阅[获取对象 ID](#get-object-ids)。

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>订阅范围内的应用程序

若要为订阅范围内的应用程序添加角色分配，请使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)。 有关如何获取应用程序的对象 ID 的信息，请参阅[获取对象 ID](#get-object-ids)。

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>管理组范围内的用户

若要为管理组范围内的用户添加角色分配，请使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)。 若要获取管理组 ID，可以在 Azure 门户中的“管理组”  边栏选项卡上找到它，也可以使用 [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)。

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>删除角色分配

在 Azure RBAC 中，若要删除访问权限，请使用[AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)删除角色分配。

以下示例在 pharma-sales  资源组上从 alain\@example.com  用户删除“虚拟机参与者”角色分配  ：

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

以下示例在订阅范围内从 <object_id> 中删除 <role_name> 角色。

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

以下示例在管理组范围内从 <object_id> 中删除 <role_name> 角色。

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

如果收到错误消息：“提供的信息未映射到角色分配”，请确保还指定了 `-Scope` 或 `-ResourceGroupName` 参数。 有关详细信息，请参阅[AZURE RBAC 故障排除](troubleshooting.md#role-assignments-with-identity-not-found)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure PowerShell 列出 Azure 角色分配](role-assignments-list-powershell.md)
- [教程：使用 Azure PowerShell 向组授予对 Azure 资源的访问权限](tutorial-role-assignments-group-powershell.md)
- [教程：使用 Azure PowerShell 创建 Azure 自定义角色](tutorial-custom-role-powershell.md)
- [使用 Azure PowerShell 管理资源](../azure-resource-manager/management/manage-resources-powershell.md)
