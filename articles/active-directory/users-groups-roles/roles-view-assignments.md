---
title: 在管理中心中查看管理员角色权限-Azure Active Directory |Microsoft Docs
description: 你现在可以在 "Azure AD 管理中心" 中查看和管理 Azure AD 管理员角色的成员。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf45d45481712e30d40bacec9a3c4d80d1ed56b6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707547"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>查看 Azure Active Directory 中的自定义角色分配

本文介绍如何查看在 Azure Active Directory (Azure AD) 中分配的自定义角色。 在 Azure Active Directory (Azure AD) 中, 可以在目录级别或单个应用程序范围内分配角色。 将目录范围内的角色分配添加到单个应用程序角色分配的列表中, 但单个应用程序范围内的角色分配不会添加到目录级别分配列表。

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-the-azure-ad-portal"></a>使用 Azure AD 门户查看具有目录作用域的角色分配

1. 在 Azure AD 组织中, 以特权角色管理员或全局管理员权限登录到 [Azure AD 管理中心](https://aad.portal.azure.com) 。
1. 选择 " **Azure Active Directory**", 选择 " **角色和管理员**", 然后选择要打开的角色, 并查看其属性。
1. 选择 "**分配**" 以查看角色的分配。

    ![当你从列表中打开角色时, 查看角色分配和权限](./media/roles-view-assignments/role-assignments.png)

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 查看目录作用域的角色分配

你可以使用 Azure PowerShell 自动执行将 Azure AD 管理员角色分配给用户的方式。 本文使用 [Azure Active Directory PowerShell 版本 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles) 模块。

### <a name="prepare-powershell"></a>准备 PowerShell

首先, 必须[下载 Azure AD 预览版 PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD/)。

若要安装 Azure AD PowerShell 模块，请使用以下命令：

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

若要验证模块是否可供使用，请运行下面的命令：

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>查看角色的分配

查看角色分配的示例。

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-microsoft-graph-api"></a>使用 Microsoft Graph API 查看目录作用域的角色分配

用于获取给定角色定义的角色分配的 HTTP 请求。

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

响应

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview"></a>使用 Azure AD 门户 (预览) 查看具有单一应用程序范围的角色分配

1. 在 Azure AD 组织中, 以特权角色管理员或全局管理员权限登录到 [Azure AD 管理中心](https://aad.portal.azure.com) 。
1. 选择 "Azure Active Directory", 选择 "**应用注册**", 然后选择 "应用注册" 来查看其属性。 可能需要选择 "**所有应用程序**" 才能查看 Azure AD 组织中的应用注册的完整列表。

    ![从 "应用注册" 页创建或编辑应用注册](./media/roles-create-custom/appreg-all-apps.png)

1. 选择 " **角色和管理员**", 然后选择要查看其属性的角色。

    ![从 "应用注册" 页查看应用注册角色分配](./media/roles-view-assignments/appreg-assignments.png)

1. 选择 "**分配**" 以查看角色的分配。

    ![查看应用注册的属性中的应用注册角色分配](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>后续步骤

* 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
* 有关角色以及管理员角色分配的详细信息，请参阅[分配管理员角色](directory-assign-admin-roles.md)。
* 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)。
