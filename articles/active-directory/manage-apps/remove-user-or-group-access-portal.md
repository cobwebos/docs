---
title: 在 Azure Active Directory 中删除企业应用的用户或组分配 | Microsoft Docs
description: 如何在 Azure Active Directory 的企业应用中删除对用户或组的访问权限分配
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ad995db26b67f365cd8ed9b2aeba5e47fe9c01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65826071"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>在 Azure Active Directory 中删除企业应用的用户或组分配
若要删除用户或组从已分配的访问，与 Azure Active Directory (Azure AD) 中对企业应用程序的容易。 你需要具有合适的权限才能管理企业应用。 而且，你必须是目录的全局管理员。

> [!NOTE]
> 对于 Microsoft 应用程序（例如 Office 365 应用），请使用 PowerShell 删除到企业应用的用户分配。

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>如何在 Azure 门户中删除到企业应用的用户或组分配？
1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”  ，在文本框中输入 **Azure Active Directory**，并选择“Enter”  。
1. 在“Azure Active Directory - *directoryname*”页面（即，正在管理的目录的 Azure AD 页面）上，选择“企业应用程序”。  
1. 在“企业应用程序 - 所有应用程序”  页上，你会看到你可以管理的应用的列表。 选择一个应用。
1. 在 ***appname*** 概览页面（即标题中包含所选应用的名称的页面）上，选择“用户和组”  。
1. 在“***appname*** - 用户和组分配”页面上，选择一个或多个用户或组，并选择“删除”命令。   出现提示时确认所作的决定。

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>如何使用 PowerShell 删除到企业应用的用户或组分配？
1. 以提升的权限打开 Windows PowerShell 命令提示符。

    >[!NOTE] 
    > 需要安装 AzureAD 模块（使用命令 `Install-Module -Name AzureAD`）。 出现安装 NuGet 模块或新的 Azure Active Directory V2 PowerShell 模块的提示时，请键入 Y，然后按 ENTER。

1. 运行 `Connect-AzureAD` 并使用全局管理员用户帐户登录。
1. 使用以下脚本将用户和角色从应用程序中删除：

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
   ## <a name="next-steps"></a>后续步骤

- [查看所有组](../fundamentals/active-directory-groups-view-azure-portal.md)
- [向企业应用分配用户或组](assign-user-or-group-access-portal.md)
- [禁用企业应用的用户登录](disable-user-sign-in-portal.md)
- [更改企业应用的名称或徽标](change-name-or-logo-portal.md)
