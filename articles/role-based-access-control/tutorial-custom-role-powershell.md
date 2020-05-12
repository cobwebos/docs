---
title: 教程：使用 Azure PowerShell 创建 Azure 自定义角色 - Azure RBAC
description: 在本教程中，我们从使用 Azure PowerShell 和 Azure 基于角色的访问控制 (Azure RBAC) 创建 Azure 自定义角色着手。
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
ms.openlocfilehash: 8aedc59fc34278bf84983d78bf0e9a31fe38ee93
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735583"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-powershell"></a>教程：使用 Azure PowerShell 创建 Azure 自定义角色

如果 [Azure 内置角色](built-in-roles.md)不满足组织的特定需求，你可以创建自己的自定义角色。 对于本教程，你将使用 Azure PowerShell 创建名为 Reader Support Tickets 的自定义角色。 该自定义角色允许用户在订阅的管理平面中查看所有内容，以及创建支持票证。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建自定义角色
> * 列出自定义角色
> * 更新自定义角色
> * 删除自定义角色

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要：

- 有权创建自定义角色，例如[所有者](built-in-roles.md#owner)或[用户访问管理员](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) 或 [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>登录到 Azure PowerShell

登录到 [Azure PowerShell](/powershell/azure/authenticate-azureps)。

## <a name="create-a-custom-role"></a>创建自定义角色

创建自定义角色的最简单方法是从内置角色着手，对其进行编辑，然后创建新角色。

1. 在 PowerShell 中，使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 命令获取适用于 Microsoft.Support 资源提供程序的操作列表。 这有助于了解可用来创建你的权限的操作。 还可以在 [Azure 资源管理器资源提供程序操作](resource-provider-operations.md#microsoftsupport)中查看所有操作的列表。

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. 使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令以 JSON 格式输出 [Reader](built-in-roles.md#reader) 角色。

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. 在编辑器中打开 **ReaderSupportRole.json** 文件。

    下面显示了 JSON 输出。 有关不同属性的信息，请参阅 [Azure 自定义角色](custom-roles.md)。

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. 编辑 JSON 文件来向 `Actions` 属性添加 `"Microsoft.Support/*"` 操作。 请确保在读取操作后包括一个逗号。 此操作将允许用户创建支持票证。

1. 使用 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) 命令获取订阅的 ID。

    ```azurepowershell
    Get-AzSubscription
    ```

1. 在 `AssignableScopes` 中，采用以下格式添加订阅 ID：`"/subscriptions/00000000-0000-0000-0000-000000000000"`

    必须添加显式的订阅 ID，否则将不允许将角色导入到订阅中。

1. 删除 `Id` 属性行并将 `IsCustom` 属性更改为 `true`。

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
    
1. 若要新建自定义角色，请使用 [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) 命令，并指定 JSON 角色定义文件。

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    现在，新的自定义角色在 Azure 门户中可用，并可分配给用户、组或服务主体，就像内置角色一样。

## <a name="list-custom-roles"></a>列出自定义角色

- 若要列出所有自定义角色，请使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令。

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    还可以在 Azure 门户中查看自定义角色。

    ![Azure 门户中导入的自定义角色屏幕截图](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>更新自定义角色

若要更新自定义角色，可以更新 JSON 文件或使用 `PSRoleDefinition` 对象。

1. 若要更新 JSON 文件，请使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令以 JSON 格式输出自定义角色。

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. 在编辑器中打开该文件。

1. 在 `Actions` 中，添加用于创建和管理资源组部署 `"Microsoft.Resources/deployments/*"` 的操作。

    更新后的 JSON 文件应如下所示：

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
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
        
1. 若要更新自定义角色，请使用 [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) 命令并指定更新后的 JSON 文件。

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. 若要使用 `PSRoleDefintion` 对象更新你的自定义角色，请首先使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令来获取该角色。

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. 调用 `Add` 方法来添加用于读取诊断设置的操作。

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. 使用 [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) 来更新角色。

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>删除自定义角色

1. 使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令获取自定义角色的 ID。

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. 使用 [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) 命令并指定角色 ID 来删除自定义角色。

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. 系统要求确认时，请键入“Y”  。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure PowerShell 创建或更新 Azure 自定义角色](custom-roles-powershell.md)
