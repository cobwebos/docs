---
title: 将用户或组分配到 Azure AD 中的企业应用
description: 如何选择企业应用，在 Azure Active Directory 中向其分配用户或组
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a5135f97ffb7d29c9fd928382ca4344beaa654d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274743"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中向企业应用分配用户或组

若要将用户或组分配到企业应用程序，你应该分配了以下任一管理员角色：全局管理员、应用程序管理员、云应用程序管理员或被分配为企业应用的所有者。  对于 Microsoft 应用程序（例如 Office 365 应用），请使用 PowerShell 将用户分配到企业应用。

> [!NOTE]
> 有关本文中讨论的功能的许可要求，请参阅 [Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory)。

## <a name="assign-a-user-to-an-app---portal"></a>将用户分配到应用 - 门户

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”，在文本框中输入 Azure Active Directory，并选择“Enter”。
1. 选择“企业应用程序”。
1. 在“企业应用程序 - 所有应用程序”窗格上，你会看到你可以管理的应用的列表。 选择一个应用。
1. 在 ***appname*** 窗格（即标题中包含所选应用的名称的窗格）中，选择“用户和组”。
1. 在“appname - 用户和组”窗格中，选择“添加用户”。
1. 在 "**添加分配**" 窗格中，选择 "**用户和组**"。

   ![将用户或组分配给应用](./media/assign-user-or-group-access-portal/assign-users.png)

1. 在 "**用户和组**" 窗格中，从列表中选择一个或多个用户或组，然后选择窗格底部的 "**选择**" 按钮。
1. 在“添加分配”窗格中选择“角色”。 然后，在 "**选择角色**" 窗格中，选择要应用于所选用户或组的角色，然后在窗格底部选择 **"确定"** 。
1. 在“添加分配”窗格中，选择窗格底部的“分配”按钮。 已分配用户或组的权限将是该企业应用的选定角色所定义的权限。

## <a name="allow-all-users-to-access-an-app---portal"></a>允许所有用户访问某个应用 - 门户

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”，在文本框中输入 Azure Active Directory，并选择“Enter”。
1. 选择“企业应用程序”。
1. 在“企业应用程序”窗格中，选择“所有应用程序”。 随后会列出你可以管理的应用。
1. 在“企业应用程序 - 所有应用程序”窗格中，选择一个应用。
1. 在“appname”窗格上，选择“属性”。
1. 在“appname - 属性”窗格上，将“需要进行用户分配?”设置设置为“否”。

“需要进行用户分配?”选项：

- 如果此选项设置为“是”，则必须先将用户分配到此应用程序，然后用户才能访问它。
- 如果此选项设置为“否”，则任何直接导航到应用程序深层链接 URL 或应用程序 URL 的用户都将被授予访问权限
- 不会影响应用程序是否显示在应用程序访问面板上。 要在访问面板上显示应用程序，需要为应用程序分配相应的用户或组。
- 仅适用于使用为 SAML 单一登录配置的云应用程序的函数、使用 Azure Active Directory 预身份验证的应用程序代理应用程序或在用户或管理员同意该应用程序后直接在使用 OAuth 2.0/OpenID Connect Authentication 的 Azure AD 应用程序平台上构建的应用程序。 请参阅[应用程序的单一登录](what-is-single-sign-on.md)。 请参阅[配置最终用户对应用程序的许可方式](configure-user-consent.md)。
- 当为任何其他单一登录模式配置应用程序时，此选项不起作用。

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

   ![显示使用 Workplace Analytics 角色的用户可用的角色](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

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
