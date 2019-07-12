---
title: 在 Azure Active Directory 中向企业应用分配用户或组 | Microsoft Docs
description: 如何选择企业应用，在 Azure Active Directory 中向其分配用户或组
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 249dfeeb231c61b05af2e89f0dc02822cc18e627
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702175"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中向企业应用分配用户或组

若要将用户或组分配到企业应用，必须具有适当的权限才能管理企业应用，并且必须是目录的全局管理员。 对于 Microsoft 应用程序（例如 Office 365 应用），请使用 PowerShell 将用户分配到企业应用。

> [!NOTE]
> 有关本文中讨论的功能的许可要求，请参阅 [Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory)。

## <a name="assign-a-user-to-an-app---portal"></a>将用户分配到应用 - 门户

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”  ，在文本框中输入 Azure Active Directory，并选择“Enter”  。
1. 选择“企业应用程序”。 
1. 在“企业应用程序 - 所有应用程序”  窗格上，你会看到你可以管理的应用的列表。 选择一个应用。
1. 在 ***appname*** 窗格（即标题中包含所选应用的名称的窗格）中，选择“用户和组”  。
1. 在“appname - 用户和组”窗格中，选择“添加用户”。  
1. 上**添加分配**窗格中，选择**用户和组**。

   ![将用户或组分配给应用](./media/assign-user-or-group-access-portal/assign-users.png)

1. 上**用户和组**窗格中，从列表中选择一个或多个用户或组，然后选择**选择**窗格底部的按钮。
1. 在“添加分配”窗格中选择“角色”   。 然后，在**选择角色**窗格中，选择一个角色将应用到所选的用户或组，然后选择**确定**窗格的底部。
1. 在“添加分配”窗格中，选择窗格底部的“分配”按钮。   已分配用户或组的权限将是该企业应用的选定角色所定义的权限。

## <a name="allow-all-users-to-access-an-app---portal"></a>允许所有用户访问某个应用 - 门户

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”  ，在文本框中输入 Azure Active Directory，并选择“Enter”  。
1. 选择“企业应用程序”。 
1. 在“企业应用程序”  窗格中，选择“所有应用程序”  。 随后会列出你可以管理的应用。
1. 在“企业应用程序 - 所有应用程序”  窗格中，选择一个应用。
1. 在“appname”窗格上，选择“属性”  。
1. 在“appname - 属性”窗格上，将“需要进行用户分配?”设置设置为“否”。   

“需要进行用户分配?”选项  ：

- 不会影响应用程序出现在应用程序访问面板上。 要在访问面板上显示应用程序，需要为应用程序分配相应的用户或组。
- 仅适用于为 SAML 单一登录配置的云应用程序以及使用应用程序代理配置的本地应用程序。 请参阅[应用程序的单一登录](what-is-single-sign-on.md)。
- 需要用户许可某应用程序。 管理员可以为所有用户授予许可。  请参阅[配置最终用户对应用程序的许可方式](configure-user-consent.md)。

## <a name="assign-a-user-to-an-app---powershell"></a>将用户分配到应用 - PowerShell

1. 以提升的权限打开 Windows PowerShell 命令提示符。

   > [!NOTE]
   > 需要安装 AzureAD 模块（使用命令 `Install-Module -Name AzureAD`）。 出现安装 NuGet 模块或新的 Azure Active Directory V2 PowerShell 模块的提示时，请键入 Y，然后按 ENTER。

1. 运行 `Connect-AzureAD` 并使用全局管理员用户帐户登录。
1. 使用以下脚本将用户和角色分配到应用程序：

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

有关如何将用户分配到应用程序角色的详细信息，请访问 [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0) 的文档

若要将组分配到企业应用，需要将 `Get-AzureADUser` 替换为 `Get-AzureADGroup`。

### <a name="example"></a>示例

此示例使用 PowerShell 将用户 Britta Simon 分配到 [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) 应用程序。

1. 在 PowerShell 中，将相应的值分配到变量 $username、$app_name 和 $app_role_name。

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. 在此示例中，我们并不确切地知道要将哪个应用程序角色名称分配给 Britta Simon。 运行以下命令，使用用户 UPN 和服务主体显示名称获取用户 ($user) 和服务主体 ($sp)。

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. 运行命令 `$sp.AppRoles`，显示可用于 Workplace Analytics 应用程序的角色。 在此示例中，我们要为 Britta Simon 分配“分析员”（访问权限受限）角色。

   ![显示用户使用 Workplace Analytics 角色可用的角色](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. 将角色名称分配到 `$app_role_name` 变量。

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. 运行以下命令，将用户分配到应用角色：

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>后续步骤

- [查看所有组](../fundamentals/active-directory-groups-view-azure-portal.md)
- [删除企业应用的用户或组分配](remove-user-or-group-access-portal.md)
- [禁用企业应用的用户登录](disable-user-sign-in-portal.md)
- [更改企业应用的名称或徽标](change-name-or-logo-portal.md)
