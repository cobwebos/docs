---
title: 教程：使用 Azure PowerShell 授予组对 Azure 资源的访问权限 - Azure RBAC
description: 本教程介绍如何使用 Azure PowerShell 和 Azure 基于角色的访问控制 (Azure RBAC) 授予组对 Azure 资源的访问权限。
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
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: 0d72ea23b74137e7e57f892b831b0be1b4a89de5
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735515"
---
# <a name="tutorial-grant-a-group-access-to-azure-resources-using-azure-powershell"></a>教程：使用 Azure PowerShell 授予组对 Azure 资源的访问权限

可以通过 [Azure 基于角色的访问控制 (Azure RBAC)](overview.md) 管理对 Azure 资源的访问权限。 在本教程中，请授予组访问权限，以便通过 Azure PowerShell 查看订阅中的所有内容并管理资源组中的一切。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在不同范围授予组访问权限
> * 列出访问权限
> * 删除访问权限

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要：

- 在 Azure Active Directory 中创建组的权限（或者有现成的组）
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>角色分配

在 Azure RBAC 中，若要授予访问权限，请创建角色分配。 角色分配包含三个要素：安全主体、角色订阅和范围。 下面是两个将要在本教程中执行的角色分配：

| 安全主体 | 角色定义 | 范围 |
| --- | --- | --- |
| 组<br>（RBAC 教程组） | [读取者](built-in-roles.md#reader) | 订阅 |
| 组<br>（RBAC 教程组）| [参与者](built-in-roles.md#contributor) | 资源组<br>(rbac-tutorial-resource-group) |

   ![组的角色分配](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>创建组

若要分配角色，需要一个用户、组或服务主体。 如果还没有组，可以创建一个。

- 在 Azure Cloud Shell 中，使用 [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) 命令创建一个新的组。

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

如果你无权创建组，可以尝试改用[教程：使用 Azure PowerShell 授予用户对 Azure 资源的访问权限](tutorial-role-assignments-user-powershell.md)。

## <a name="create-a-resource-group"></a>创建资源组

请使用资源组来演示如何在资源组范围分配角色。

1. 使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 命令获取区域位置的列表。

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. 选择附近的一个位置，将其分配给某个变量。

   ```azurepowershell
   $location = "westus"
   ```

1. 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建新的资源组。

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>授予访问权限

若要为组授予访问权限，请使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 命令分配一个角色。 必须指定安全主体、角色定义和范围。

1. 使用 [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup) 命令获取组的对象 ID。

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. 在变量中保存组对象 ID。

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. 使用 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) 命令获取订阅的 ID。

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. 在变量中保存订阅范围。

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. 将[读者](built-in-roles.md#reader)角色分配给订阅范围内的组。

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. 将[参与者](built-in-roles.md#contributor)角色分配给资源组范围内的组。

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>列出访问权限

1. 若要验证订阅的访问权限，请使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 命令列出角色分配。

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    在输出中，可以看到“读者”角色已分配给订阅范围的“RBAC 教程组”。

1. 若要验证资源组的访问权限，请使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 命令列出角色分配。

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    在输出中，可以看到“参与者”角色和“读者”角色已分配给“RBAC 教程组”。 “参与者”角色处于 rbac-tutorial-resource-group 范围，“读者”角色在订阅范围继承。

## <a name="optional-list-access-using-the-azure-portal"></a>（可选）使用 Azure 门户列出访问权限

1. 若要查看角色分配在 Azure 门户中的显示情况，请查看“访问控制(IAM)”边栏选项卡，以了解相关订阅。 

    ![组在订阅范围的角色分配](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. 查看“访问控制(IAM)”边栏选项卡，了解相关资源组。 

    ![组在资源组范围的角色分配](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>删除访问权限

若要删除用户、组和应用程序的访问权限，请使用 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 删除角色分配。

1. 使用以下命令，删除组在资源组范围的“参与者”角色分配。

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. 使用以下命令，删除组在订阅范围的“读者”角色分配。

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>清理资源

若要清理本教程创建的资源，请删除资源组和组。

1. 使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令删除资源组。

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. 系统要求确认时，请键入“Y”  。只需数秒钟即可删除。

1. 使用 [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup) 命令删除组。

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    如果在尝试删除组时收到错误，也可在门户中删除组。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure PowerShell 添加或删除 Azure 角色分配](role-assignments-powershell.md)
