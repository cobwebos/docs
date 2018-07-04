---
title: 使用 Azure PowerShell 创建自定义角色 | Microsoft Docs
description: 了解如何使用 Azure PowerShell 为基于角色的访问控制 (RBAC) 创建自定义角色。 这包括如何列出、创建、更新和删除自定义角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6205198d3f9c4ec5bfa2311014cf9b90dcade6dc
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36323292"
---
# <a name="create-custom-roles-using-azure-powershell"></a>使用 Azure PowerShell 创建自定义角色

如果[内置角色](built-in-roles.md)不能满足组织的特定需求，可以创建你自己的自定义角色。 本文介绍如何使用 Azure PowerShell 创建和管理自定义角色。

## <a name="prerequisites"></a>先决条件

若要创建自定义角色，需要具备以下条件：

- 有权创建自定义角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)
- 在本地安装了 [Azure PowerShell](/powershell/azure/install-azurerm-ps)

## <a name="list-custom-roles"></a>列出自定义角色

若要列出可在某范围内进行分配的角色，请使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) 命令。 以下示例列出了可在所选订阅中进行分配的所有角色。

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

以下示例仅列出了可在所选订阅中进行分配的自定义角色。

```azurepowershell
Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

如果所选订阅不在角色的 `AssignableScopes` 中，则不会列出自定义角色。

## <a name="create-a-custom-role"></a>创建自定义角色

若要创建自定义角色，请使用 [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) 命令。 构造角色有两种方法：使用 `PSRoleDefinition` 对象或 JSON 模板。 

### <a name="get-operations-for-a-resource-provider"></a>获取资源提供程序的操作

创建自定义角色时，请务必了解资源提供程序的所有可能操作。
可以查看[资源提供程序操作](resource-provider-operations.md)的列表，也可以使用 [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) 命令获取该信息。
例如，如果想要查看虚拟机的所有可用操作，请使用此命令：

```azurepowershell
Get-AzureRMProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>使用 PSRoleDefinition 对象创建自定义角色

使用 PowerShell 创建自定义角色时，可以使用某个[内置角色](built-in-roles.md)作为起点，也可以从头开始。 本部分中的第一个示例以内置角色开始，并为它自定义更多的权限。 编辑属性以添加所需的 `Actions`、`NotActions` 或 `AssignableScopes`，然后将这些更改保存为新角色。

以下示例从[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)内置角色开始，使用该角色创建名为“虚拟机操作员”的自定义角色。 该新角色授权访问 *Microsoft.Compute*、*Microsoft.Storage* 和 *Microsoft.Network* 资源提供程序的所有读取操作，并授权访问启动、重新启动和监视虚拟机。 该自定义角色可以在两个订阅中使用。

```azurepowershell
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
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzureRmRoleDefinition -Role $role
```

以下示例显示创建“虚拟机操作员”自定义角色的另一种方式。 首先，创建一个新 `PSRoleDefinition` 对象。 在 `perms` 变量中指定操作，然后将操作设置为 `Actions` 属性。 通过从 [虚拟机参与者](built-in-roles.md#virtual-machine-contributor)内置角色读取 `NotActions` 设置 `NotActions` 属性。 由于[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)没有任何 `NotActions`，因此不需要此行，但它显示了从另一个角色检索信息的方式。

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzureRmRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzureRmRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>使用 JSON 模板创建自定义角色

JSON 模板可用作自定义角色的源定义。 下面的示例创建一个有权读取存储和计算资源以及有权访问支持的自定义角色，并将该角色添加到两个订阅。 创建包含以下示例的新文件 `C:\CustomRoles\customrole1.json`。 创建初始角色时，应将 ID 设置为 `null`，因为会自动生成新的 ID。 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

要将角色添加到订阅，请运行以下 PowerShell 命令：

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>更新自定义角色

与创建自定义角色类似，可以使用 `PSRoleDefinition` 对象或 JSON 模板修改现有自定义角色。

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>使用 PSRoleDefinition 对象更新自定义角色

若要修改自定义角色，请先使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) 命令检索角色定义。 然后，对该角色定义作出所需更改。 最后，使用 [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) 命令保存修改后的角色定义。

以下示例将 `Microsoft.Insights/diagnosticSettings/*` 操作添加到*虚拟机操作员*自定义角色。

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

以下示例将 Azure 订阅添加到*虚拟机操作员*自定义角色的可分配范围。

```azurepowershell
Get-AzureRmSubscription -SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzureRmSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="update-a-custom-role-with-a-json-template"></a>使用 JSON 模板更新自定义角色

使用前面的 JSON 模板，可轻松修改现有自定义角色，以添加或删除操作。 更新 JSON 模板并添加读取网络操作，如以下示例中所示。 模板中列出的定义并不累积应用于现有定义，这意味着角色与模板中指定的完全一样。 还需要将 ID 字段更新为角色的 ID。 如果不确定此值是什么，可以使用 [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) cmdlet 来获取该信息。

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

若要更新现有角色，请运行以下 PowerShell 命令：

```azurepowershell
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>删除自定义角色

若要删除自定义角色，请使用 [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) 命令。

以下示例删除了*虚拟机操作员*自定义角色。

```azurepowershell
Get-AzureRmRoleDefinition "Virtual Machine Operator"
Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure PowerShell 创建自定义角色](tutorial-custom-role-powershell.md)
- [Azure 中的自定义角色](custom-roles.md)
- [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)
