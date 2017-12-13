---
title: "在 Azure Active Directory 中向企业应用分配用户或组 | Microsoft Docs"
description: "如何选择企业应用，在 Azure Active Directory 中向其分配用户或组"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: daveba
ms.reviewer: luleon
ms.openlocfilehash: 65727ee9330a1a6650eb54595ebc93a7a693923c
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2017
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中向企业应用分配用户或组
若要将用户或组分配到企业应用，必须具有适当的权限才能管理企业应用，并且必须是目录的全局管理员。
> [!NOTE]
> 对于 Microsoft 应用程序（例如 Office 365 应用），请使用 PowerShell 将用户分配到企业应用。

## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>如何在 Azure 门户中分配用户对企业应用的访问权限？
1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，在文本框中输入“Azure Active Directory”，并选择“Enter”。
3. 在“Azure Active Directory - 目录名”边栏选项卡（即，正在管理的目录的 Azure AD 边栏选项卡）中，选择“企业应用程序”。

    ![打开企业应用](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. 在“企业应用程序”边栏选项卡中，选择“所有应用程序”。 随后会列出你可以管理的应用。
5. 在在“企业应用程序 - 所有应用程序”边栏选项卡中，选择一个应用。
6. 在“appname”边栏选项卡（即标题中包含所选应用名称的边栏选项卡）中，选择“用户和组”。

    ![选择“所有应用程序”命令](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. 在“***appname*** - 用户和组分配”边栏选项卡中，选择“添加”命令。
8. 在“添加分配”边栏选项卡中，选择“用户和组”。

    ![将用户或组分配给应用](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. 在“用户和组”边栏选项卡的列表中选择一个或多个用户或组，并选择边栏选项卡底部的“选择”按钮。
10. 在“添加分配”边栏选项卡中，选择“角色”。 然后，在“选择角色”边栏选项卡中选择一个需要应用到所选用户或组的角色，再选择边栏选项卡底部的“确定”按钮。
11. 在“添加分配”边栏选项卡中，选择边栏选项卡底部的“分配”按钮。 已分配用户或组的权限将是该企业应用的选定角色所定义的权限。

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>如何使用 PowerShell 将用户分配到企业应用？

1. 以提升的权限打开 Windows PowerShell 命令提示符。

    >[!NOTE] 
    > 需要安装 AzureAD 模块（使用命令 `Install-Module -Name AzureAD`）。 出现安装 NuGet 模块或新的 Azure Active Directory V2 PowerShell 模块的提示时，请键入 Y，然后按 ENTER。

2. 运行 `Connect-AzureAD` 并使用全局管理员用户帐户登录。
3. 使用以下脚本将用户和角色分配到应用程序：

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

有关如何将用户分配到应用程序角色的详细信息，请访问 [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/en-us/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0) 的文档

### <a name="example"></a>示例

此示例使用 PowerShell 将用户 Britta Simon 分配到 [Microsoft Workplace Analytics](https://products.office.com/en-us/business/workplace-analytics) 应用程序。

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
    
    ![Workplace Analytics 角色](media/active-directory-coreapps-assign-user-azure-portal/workplace-analytics-role.png)

4. 将角色名称分配到 `$app_role_name` 变量。
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    ```

5. 运行以下命令，将用户分配到应用角色：

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>后续步骤
* [查看所有组](active-directory-groups-view-azure-portal.md)
* [删除企业应用的用户或组分配](active-directory-coreapps-remove-assignment-azure-portal.md)
* [禁用企业应用的用户登录](active-directory-coreapps-disable-app-azure-portal.md)
* [更改企业应用的名称或徽标](active-directory-coreapps-change-app-logo-user-azure-portal.md)
