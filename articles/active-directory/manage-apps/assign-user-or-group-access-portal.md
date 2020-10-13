---
title: 在 Azure Active Directory 中管理应用的用户分配
description: 了解如何使用 Azure Active Directory 进行标识管理，为应用分配和取消分配用户和组。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: f49377743521e27c2312e95491762ca48d8448c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604319"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>在 Azure Active Directory 中管理应用的用户分配

本文介绍如何将用户和组分配到 Azure Active Directory (Azure AD) 中的企业应用程序，可以从 Azure 门户中或使用 PowerShell。 将某个用户分配到某个应用程序时，该应用程序会显示在该用户的[我的应用](https://myapps.microsoft.com/)中以方便访问。 如果应用程序公开角色，则你还可以将特定的角色分配给用户。

为了提高控制度，可将某些类型的企业应用程序配置为[需要进行用户分配](#configure-an-application-to-require-user-assignment)。 

> [!IMPORTANT]
> 如果你将某组分配到应用程序，只有属于此组的用户才有访问权限。 此分配不会级联到嵌套组。

> [!NOTE]
> 基于组的分配需要 Azure Active Directory Premium P1 或 P2 版本。 基于组的分配仅支持安全组。 目前不支持嵌套的组成员身份和 Microsoft 365 组。 有关本文中讨论的功能的其他许可要求，请参阅 [Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory)。 

## <a name="configure-an-application-to-require-user-assignment"></a>将应用程序配置为需要进行用户分配

对于以下类型的应用程序，可以选择要求先将用户分配到该应用程序，然后他们才能访问该应用程序：

- 经配置后可以使用基于 SAML 的身份验证进行联合单一登录 (SSO) 的应用程序
- 使用 Azure Active Directory 预身份验证的应用程序代理应用程序
- 在 Azure AD 应用程序平台上生成且使用 OAuth 2.0/OpenID Connect 身份验证的应用程序（前提是用户或管理员已许可该应用程序）。

需要进行用户分配时，只有（通过直接用户分配或基于组成员身份）显式分配到应用程序的用户才能登录。 他们可以在“我的应用”页上或者使用直接链接来访问该应用。 

不需要分配时（由于已将此选项设置为“否”，或者应用程序使用另一种 SSO 模式），任何获得了应用程序的直接链接或应用程序“属性”页中的“用户访问 URL”的用户都可以访问该应用程序。   

应用程序是否显示在“我的应用”上不受此设置的影响。 将某个用户或组分配到应用程序后，应用程序会显示在用户的“我的应用”访问面板上。 有关背景信息，请参阅[管理对应用的访问](what-is-access-management.md)。

若要要求为应用程序分配用户，请执行以下操作：
1. 使用管理员帐户或以应用程序所有者的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory”。 在左侧导航菜单中，选择“企业应用程序”。
3. 从列表中选择应用。 如果看不到该应用程序，请在搜索框中键入其名称。 或者使用筛选控件选择应用程序类型、状态或可见性，然后选择“应用”。
4. 在左侧导航菜单中，选择“属性”。
5. 确保“需要进行用户分配?”切换开关设置为“是”。 
   > [!NOTE]
   > 如果“需要进行用户分配?”切换开关不可用，可以使用 PowerShell 设置服务主体的 appRoleAssignmentRequired 属性。
6. 选择屏幕顶部的“保存”按钮。

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>使用 Azure 门户为应用分配或取消分配用户和组
若要了解如何使用 Azure 门户分配或取消分配用户或组，请参阅 [应用程序管理中的快速入门系列](add-application-portal-assign-users.md)。

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>使用图形 API 为应用分配或取消分配用户和组
可以使用图形 API 为应用分配或取消分配用户和组。 若要了解详细信息，请参阅[应用角色分配](https://docs.microsoft.com/graph/api/resources/approleassignment)。

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>使用 PowerShell 将用户和组分配给应用
1. 以提升的权限打开 Windows PowerShell 命令提示符。
   > [!NOTE]
   > 需要安装 AzureAD 模块（使用命令 `Install-Module -Name AzureAD`）。 出现安装 NuGet 模块或新的 Azure Active Directory V2 PowerShell 模块的提示时，请键入 Y，然后按 ENTER。
2. 运行 `Connect-AzureAD` 并使用全局管理员用户帐户登录。
3. 使用以下脚本将用户和角色分配到应用程序：

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
有关如何将用户分配到应用程序角色的详细信息，请参阅 [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0) 的文档。

若要将组分配到企业应用，必须将 `Get-AzureADUser` 替换为 `Get-AzureADGroup`，并将 `New-AzureADUserAppRoleAssignment` 替换为 `New-AzureADGroupAppRoleAssignment`。

有关如何将组分配到应用程序角色的详细信息，请参阅 [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureadgroupapproleassignment?view=azureadps-2.0) 的文档。

### <a name="example"></a>示例

此示例使用 PowerShell 将用户 Britta Simon 分配到 [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) 应用程序。

1. 在 PowerShell 中，将相应的值分配到变量 $username、$app_name 和 $app_role_name。

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. 在此示例中，我们并不确切地知道要将哪个应用程序角色名称分配给 Britta Simon。 运行以下命令，使用用户 UPN 和服务主体显示名称获取用户 ($user) 和服务主体 ($sp)。

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. 运行命令 `$sp.AppRoles`，显示可用于 Workplace Analytics 应用程序的角色。 在此示例中，我们要为 Britta Simon 分配“分析员”（访问权限受限）角色。
   ![显示使用 Workplace Analytics 角色的用户可用的角色](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. 将角色名称分配到 `$app_role_name` 变量。

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. 运行以下命令，将用户分配到应用角色：

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>使用 PowerShell 从应用中取消分配用户和组

1. 以提升的权限打开 Windows PowerShell 命令提示符。
   > [!NOTE]
   > 需要安装 AzureAD 模块（使用命令 `Install-Module -Name AzureAD`）。 出现安装 NuGet 模块或新的 Azure Active Directory V2 PowerShell 模块的提示时，请键入 Y，然后按 ENTER。
2. 运行 `Connect-AzureAD` 并使用全局管理员用户帐户登录。
3. 使用以下脚本将用户和角色从应用程序中删除：

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```


## <a name="related-articles"></a>相关文章

- [详细了解最终用户如何访问应用程序](end-user-experiences.md)
- [规划应用程序部署 Azure AD](access-panel-deployment-plan.md)
- [管理对应用的访问](what-is-access-management.md)
 
## <a name="next-steps"></a>后续步骤

- [查看所有组](../fundamentals/active-directory-groups-view-azure-portal.md)
- [删除企业应用的用户或组分配](remove-user-or-group-access-portal.md)
- [禁用企业应用的用户登录](disable-user-sign-in-portal.md)
- [Change the name or logo of an enterprise app](change-name-or-logo-portal.md)
