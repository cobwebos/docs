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
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e36e4625a60362c54972b16b53f0f3e6753fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409186"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中向企业应用分配用户或组

本文介绍如何从 Azure 门户内或通过使用 PowerShell 将用户或组分配到 Azure Active Directory （Azure AD）中的企业应用程序。 将用户分配到应用程序时，应用程序会显示在用户的["我的应用访问面板](https://myapps.microsoft.com/)" 中，以便于访问。 如果应用程序公开角色，你还可以将特定角色分配给用户。

为了更好地控制，可以将某些类型的企业应用程序配置为[要求用户分配](#configure-an-application-to-require-user-assignment)。 

若要[将用户或组分配到企业应用](#assign-users-or-groups-to-an-app-via-the-azure-portal)，需要以 "全局管理员"、"应用程序管理员"、"云应用程序管理员" 或 "企业应用程序分配的所有者" 身份登录。

> [!NOTE]
> 基于组的分配需要 Azure Active Directory Premium P1 或 P2 版本。 仅安全组支持基于组的分配。 当前不支持嵌套的组成员身份和 Office 365 组。 有关本文中所述功能的更多许可要求，请参阅[Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory)。 

## <a name="configure-an-application-to-require-user-assignment"></a>将应用程序配置为要求用户分配

使用以下类型的应用程序时，你可以选择要求用户先分配到应用程序，然后才能访问该应用程序：

- 为联合单一登录（SSO）配置了基于 SAML 的身份验证的应用程序
- 使用 Azure Active Directory 预身份验证的应用程序代理应用程序
- 在用户或管理员同意该应用程序后，在 Azure AD 应用程序平台上构建的应用程序使用 OAuth 2.0/OpenID Connect 身份验证。

需要用户分配时，只有显式分配给该应用程序的用户才能登录。 他们可以在 "我的应用" 页上或通过使用直接链接来访问应用程序。 

当*不需要*赋值时，由于已将此选项设置为 "**否**" 或应用程序使用另一个 SSO 模式，因此，如果应用程序在应用程序的 "**属性**" 页中具有指向应用程序或**用户访问 URL**的直接链接，则任何用户都将能够访问该应用程序。 

此设置不会影响应用程序是否出现在 "我的应用" 访问面板上。 向应用程序分配用户或组后，应用程序会显示在用户的 "我的应用" 访问面板上。 有关背景知识，请参阅[管理对应用的访问权限](what-is-access-management.md)。


需要对应用程序进行用户分配：

1. 使用管理员帐户或应用程序所有者登录到[Azure 门户](https://portal.azure.com)。

2. 选择“Azure Active Directory”  。 在左侧导航菜单中，选择 "**企业应用程序**"。

3. 从列表中选择应用程序。 如果看不到该应用程序，请开始在搜索框中键入其名称。 或使用筛选器控件选择应用程序类型、状态或可见性，然后选择 "**应用**"。

4. 在左侧导航菜单中，选择 "**属性**"。

5. 请确保 "**需要进行用户分配"** 切换为 **"是"**。

   > [!NOTE]
   > 如果 "**需要用户分配" 切换功能**不可用，则可以使用 PowerShell 设置服务主体上的 appRoleAssignmentRequired 属性。

6. 选择屏幕顶部的 "**保存**" 按钮。

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>通过 Azure 门户将用户或组分配给应用

1. 使用 "全局管理员"、"应用程序管理员" 或 "云应用程序管理员" 帐户或 "企业应用的分配所有者" 登录到[Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory”  。 在左侧导航菜单中，选择 "**企业应用程序**"。
3. 从列表中选择应用程序。 如果看不到该应用程序，请开始在搜索框中键入其名称。 或使用筛选器控件选择应用程序类型、状态或可见性，然后选择 "**应用**"。
4. 在左侧导航菜单中，选择 "**用户和组**"。
   > [!NOTE]
   > 如果要将用户分配到 Microsoft 应用程序（如 Office 365 应用程序），其中一些应用程序使用 PowerShell。 
5. 选择“添加用户”按钮。****
6. 在 "**添加分配**" 窗格中，选择 "**用户和组**"。
7. 选择要分配给应用程序的用户或组，或开始在搜索框中键入用户或组的名称。 你可以选择多个用户和组，并且你的选择将显示在 "**选定项**" 下。
8. 完成后，单击 "**选择**"。

   ![将用户或组分配给应用](./media/assign-user-or-group-access-portal/assign-users.png)

9. 在 "**用户和组**" 窗格中，从列表中选择一个或多个用户或组，然后选择窗格底部的 "**选择**" 按钮。
10. 如果应用程序支持，则可以将角色分配给用户或组。 在 "**添加分配**" 窗格中，选择 "**选择角色**"。 然后，在 "**选择角色**" 窗格中，选择要应用于所选用户或组的角色，然后在窗格底部选择 **"确定"** 。 

    > [!NOTE]
    > 如果应用程序不支持角色选择，则分配默认访问角色。 在这种情况下，应用程序管理用户拥有的访问级别。

2. 在“添加分配”窗格中，选择窗格底部的“分配”按钮。********

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>通过 PowerShell 将用户或组分配给应用

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

## <a name="related-articles"></a>相关文章

- [详细了解最终用户对应用程序的访问](end-user-experiences.md)
- [规划 Azure AD 访问面板部署](access-panel-deployment-plan.md)
- [管理对应用的访问](what-is-access-management.md)
 
## <a name="next-steps"></a>后续步骤

- [查看所有组](../fundamentals/active-directory-groups-view-azure-portal.md)
- [删除企业应用的用户或组分配](remove-user-or-group-access-portal.md)
- [禁用企业应用的用户登录](disable-user-sign-in-portal.md)
- [更改企业应用的名称或徽标](change-name-or-logo-portal.md)
