---
title: 教程 - 使用 RBAC 和 Azure PowerShell 授予用户的访问权限 | Microsoft Docs
description: 使用基于角色的访问控制 (RBAC) 授予用户访问权限，以便通过 Azure PowerShell 查看订阅中的所有内容并管理资源组中的一切。
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
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: 4eeae864721040bda18409b48abf5d33b81ca609
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295892"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-azure-powershell"></a>教程：使用 RBAC 和 Azure PowerShell 授予用户的访问权限

可以在 Azure 中通过[基于角色的访问控制 (RBAC)](overview.md) 这种方式管理对资源的访问。 在本教程中，请授予用户访问权限，以便通过 Azure PowerShell 查看订阅中的所有内容并管理资源组中的一切。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在不同范围授予用户访问权限
> * 列出访问权限
> * 删除访问权限

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

- 在 Azure Active Directory 中创建用户的权限（或者有现成的用户）
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>角色分配

在 RBAC 中，若要授予访问权限，请创建角色分配。 角色分配包含三个元素：安全主体、角色订阅、范围。 下面是两个将要在本教程中执行的角色分配：

| 安全主体 | 角色定义 | 范围 |
| --- | --- | --- |
| 用户<br>（RBAC 教程用户） | [读者](built-in-roles.md#reader) | 订阅 |
| 用户<br>（RBAC 教程用户）| [参与者](built-in-roles.md#contributor) | 资源组<br>(rbac-tutorial-resource-group) |

   ![用户的角色分配](./media/tutorial-role-assignments-user-powershell/rbac-role-assignments-user.png)

## <a name="create-a-user"></a>创建用户

若要分配角色，需要一个用户、组或服务主体。 如果还没有用户，可以创建一个。

1. 在 Azure Cloud Shell 中创建一个符合密码复杂性要求的密码。

    ```azurepowershell
    $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    $PasswordProfile.Password = "Password"
    ```

1. 使用 [New-AzureADUser](/powershell/module/azuread/new-azureaduser) 命令为域创建新用户。

    ```azurepowershell
    New-AzureADUser -DisplayName "RBAC Tutorial User" -PasswordProfile $PasswordProfile `
      -UserPrincipalName "rbacuser@example.com" -AccountEnabled $true -MailNickName "rbacuser"
    ```
    
    ```Example
    ObjectId                             DisplayName        UserPrincipalName    UserType
    --------                             -----------        -----------------    --------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial User rbacuser@example.com Member
    ```

## <a name="create-a-resource-group"></a>创建资源组

请使用资源组来演示如何在资源组范围分配角色。

1. 使用 [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) 命令获取区域位置的列表。

   ```azurepowershell
   Get-AzureRmLocation | select Location
   ```

1. 选择附近的一个位置，将其分配给某个变量。

   ```azurepowershell
   $location = "westus"
   ```

1. 使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令创建新的资源组。

   ```azurepowershell
   New-AzureRmResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>授予访问权限

若要为用户授予访问权限，请使用 [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) 命令分配一个角色。 必须指定安全主体、角色定义和范围。

1. 使用 [Get-AzureRmSubscription](/powershell/module/azurerm.resources/get-azurermsubscription) 命令获取订阅的 ID。

    ```azurepowershell
    Get-AzureRmSubscription
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

1. 将[读者](built-in-roles.md#reader)角色分配给订阅范围内的用户。

    ```azurepowershell
    New-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

1. 将[参与者](built-in-roles.md#contributor)角色分配给资源组范围内的用户。

    ```azurepowershell
    New-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

## <a name="list-access"></a>列出访问权限

1. 若要验证订阅的访问权限，请使用 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) 命令列出角色分配。

    ```azurepowershell
    Get-AzureRmRoleAssignment -SignInName rbacuser@example.com -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    在输出中，可以看到“读者”角色已分配给订阅范围的“RBAC 教程用户”。

1. 若要验证资源组的访问权限，请使用 [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) 命令列出角色分配。

    ```azurepowershell
    Get-AzureRmRoleAssignment -SignInName rbacuser@example.com -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    在输出中，可以看到“参与者”角色和“读者”角色已分配给“RBAC 教程用户”。 “参与者”角色处于 rbac-tutorial-resource-group 范围，“读者”角色在订阅范围继承。

## <a name="optional-list-access-using-the-azure-portal"></a>（可选）使用 Azure 门户列出访问权限

1. 若要查看角色分配在 Azure 门户中的显示情况，请查看“访问控制(IAM)”边栏选项卡，以了解相关订阅。

    ![用户在订阅范围的角色分配](./media/tutorial-role-assignments-user-powershell/role-assignments-subscription-user.png)

1. 查看“访问控制(IAM)”边栏选项卡，了解相关资源组。

    ![用户在资源组范围的角色分配](./media/tutorial-role-assignments-user-powershell/role-assignments-resource-group-user.png)

## <a name="remove-access"></a>删除访问权限

若要删除用户、组和应用程序的访问权限，请使用 [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) 删除角色分配。

1. 使用以下命令，删除用户在资源组范围的“参与者”角色分配。

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. 使用以下命令，删除用户在订阅范围的“读者”角色分配。

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>清理资源

若要清理本教程创建的资源，请删除资源组和用户。

1. 使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令删除资源组。

    ```azurepowershell
    Remove-AzureRmResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. 系统要求确认时，请键入“Y”。只需数秒钟即可删除。

1. 使用 [Remove-AzureADUser](/powershell/module/azuread/remove-azureaduser) 命令删除用户。

    ```azurepowershell
    Remove-AzureADUser -ObjectId "rbacuser@example.com"
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 RBAC 和 PowerShell 管理访问权限](role-assignments-powershell.md)