---
title: "使用 Azure PowerShell 管理基于角色的访问控制 (RBAC) | Microsoft Docs"
description: "如何使用 Azure PowerShell 管理 RBAC，包括列出角色、分配角色和删除角色分配。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 382d0cf15dafca51571346dab1e9d55eb2d09d67


---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>使用 Azure PowerShell 管理基于角色的访问控制
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)
> 
> 

可以使用 Azure 门户中基于角色的访问控制 (RBAC) 和 Azure 资源管理 API 精细管理对订阅的访问。 使用此功能，可以通过在特定范围内为 Active Directory 用户、组或服务主体分配某些角色来向其授予访问权限。

在使用 PowerShell 管理 RBAC 之前，必须具备以下条件：

* Azure PowerShell 0.8.8 版或更高版本。 若要安装最新版本并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。
* Azure Resource Manager cmdlets。 在 PowerShell 中安装 [Azure Resource Manager cmdlet](https://msdn.microsoft.com/library/mt125356.aspx)。

## <a name="list-roles"></a>列出角色
### <a name="list-all-available-roles"></a>列出所有可用的角色
若要列出可以进行分配的 RBAC 角色，并检查其授权访问的操作，请使用 `Get-AzureRmRoleDefinition`。

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - 屏幕截图](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>列出角色的操作
若要列出特定角色的操作，请使用 `Get-AzureRmRoleDefinition <role name>`。

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell - Get-AzureRmRoleDefinition for a specific role - 屏幕截图](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>查看谁具有访问权限
若要列出 RBAC 访问权限分配，请使用 `Get-AzureRmRoleAssignment`。

### <a name="list-role-assignments-at-a-specific-scope"></a>列举特定范围内的角色分配
可以查看指定订阅、资源组或资源的所有访问权限分配。 例如，若要查看资源组的所有活动分配，请使用 `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`。

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - Get-AzureRmRoleAssignment for a resource group - 屏幕截图](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>列举分配到用户的角色
若要列出分配给特定用户的所有角色和分配给用户所属组的所有角色，请使用 `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`。

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - Get-AzureRmRoleAssignment for a user - 屏幕截图](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>列出经典服务管理员和共同管理员角色分配
若要列出经典订阅管理员和共同管理员的访问权限分配，请使用：

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>授予访问权限
### <a name="search-for-object-ids"></a>搜索对象 ID
若要分配角色，需要标识对象（用户、组或应用程序）和范围。

如果不知道订阅 ID，可以在 Azure 门户的“订阅”边栏选项卡中查找。 若要了解如何查询订阅 ID，请参阅 MSDN 上的 [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx)。

若要获取 Azure AD 组的对象 ID，请使用：

    Get-AzureRmADGroup -SearchString <group name in quotes>

若要获取 Azure AD 服务主体或应用程序的对象 ID，请使用：

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>将角色分配给订阅范围内的应用程序
若要授权访问订阅范围内的应用程序，请使用：

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - 屏幕截图](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>将角色分配给资源组范围内的用户
若要授权访问资源组范围内的用户，请使用：

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - 屏幕截图](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>将角色分配给资源范围内的组
若要授权访问资源范围内的组，请使用：

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - 屏幕截图](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>删除访问权限
若要删除用户、组和应用程序的访问权限，请使用：

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - 屏幕截图](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>创建自定义角色
若要创建自定义角色，请使用 `New-AzureRmRoleDefinition` 命令。

使用 PowerShell 创建自定义角色时，需从[内置角色](role-based-access-built-in-roles.md)之一开始。 编辑属性以添加所需的 *Actions*、*notActions* 或 *scopes*，然后将所做的更改保存为新角色。

以下示例从*虚拟机参与者*角色开始，使用该角色创建名为*虚拟机操作员*的自定义角色。 该新角色授权访问 *Microsoft.Compute*、*Microsoft.Storage* 和 *Microsoft.Network* 资源提供程序的所有读取操作，并授权访问启动、重新启动和监视虚拟机。 该自定义角色可以在两个订阅中使用。

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - 屏幕截图](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## <a name="modify-a-custom-role"></a>修改自定义角色
若要修改自定义角色，请先使用 `Get-AzureRmRoleDefinition` 命令检索角色定义。 然后，对该角色定义作出所需更改。 最后，使用 `Set-AzureRmRoleDefinition` 命令保存修改后的角色定义。

以下示例将 `Microsoft.Insights/diagnosticSettings/*` 操作添加到*虚拟机操作员*自定义角色。

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - 屏幕截图](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

以下示例将 Azure 订阅添加到*虚拟机操作员*自定义角色的可分配范围。

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2"
Set-AzureRmRoleDefinition -Role $role)
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - 屏幕截图](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## <a name="delete-a-custom-role"></a>删除自定义角色
若要删除自定义角色，请使用 `Remove-AzureRmRoleDefinition` 命令。

以下示例删除了*虚拟机操作员*自定义角色。

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell - Remove-AzureRmRoleDefinition - 屏幕截图](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>列出自定义角色
若要列出可在某范围内进行分配的角色，请使用 `Get-AzureRmRoleDefinition` 命令。

以下示例列出了可在所选订阅中进行分配的所有角色。

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - 屏幕截图](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

在下面的示例中，*虚拟机操作员*自定义角色在 *Production4* 订阅中不可用，因为该订阅不在角色的 **AssignableScopes** 中。

![RBAC PowerShell - Get-AzureRmRoleDefinition - 屏幕截图](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>另请参阅
* [将 Azure PowerShell 与 Azure Resource Manager 配合使用](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]




<!--HONumber=Nov16_HO3-->


