---
title: 使用 Azure PowerShell Azure Active Directory 将自定义角色分配给资源范围 |Microsoft Docs
description: 使用 Azure PowerShell 管理 Azure AD 管理员自定义角色的成员。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/05/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ed8df92ac6a43a6cbf935bdb564f6cf0658608a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812787"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>在 Azure Active Directory 中使用 PowerShell 将自定义角色分配给资源范围

本文介绍如何在 Azure Active Directory (Azure AD) 中的组织范围内创建角色分配。 在组织范围内分配角色会授予跨 Azure AD 组织的访问权限。 若要创建具有单个 Azure AD 资源范围的角色分配, 请参阅[如何创建自定义角色并在资源范围内分配该角色](roles-create-custom.md)。本文使用[Azure Active Directory PowerShell 版本 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)模块。

有关 Azure AD 管理员角色的详细信息, 请参阅 [在 Azure Active Directory 中分配管理员角色](directory-assign-admin-roles.md)。

## <a name="required-permissions"></a>所需权限

连接到 Azure AD 租户，使用全局管理员帐户分配或删除角色。

## <a name="prepare-powershell"></a>准备 PowerShell

从[PowerShell 库](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安装 Azure AD PowerShell 模块。 然后使用以下命令导入 Azure AD PowerShell 预览模块:

``` PowerShell
import-module azureadpreview
```

若要验证该模块是否可供使用, 请将以下命令返回的版本与下面列出的命令匹配:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

现在可以开始使用模块中的 cmdlet 了。 有关 Azure AD 模块中 cmdlet 的完整说明, 请参阅[Azure AD 预览模块](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)的联机参考文档。

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>使用资源范围将角色分配给用户或服务主体

1. 打开 Azure AD 预览版 PowerShell 模块。
1. 执行命令`Connect-AzureAD`登录。
1. 使用以下 PowerShell 脚本创建新的角色。

``` PowerShell
# Get the user and role definition you want to link
 $user = Get-AzureADMSUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
    "displayName eq 'f/128 Filter Photos'"
$resourceScopes = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScopes $resourceScopes -RoleDefinitionId $roleDefinition.objectId -PrincipalId $user.objectId
```

若要将角色分配给服务主体而不是用户, 请使用[AzureADMSServicePrincipal cmdlet](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0)。

## <a name="operations-on-roledefinition"></a>针对 RoleDefinition 的操作

角色定义对象包含内置角色或自定义角色的定义, 以及该角色分配授予的权限。 此资源显示自定义角色定义和内置 directoryRoles (以 roleDefinition 等效形式显示)。 目前, Azure AD 组织最多可定义30个唯一自定义 RoleDefinitions。

### <a name="create-operations-on-roledefinition"></a>在 RoleDefinition 上创建操作

``` PowerShell
# Basic information

$description = "Application Registration Credential Administrator"
$displayName = "Custom Demo Admin"
$resourceScopes = @('/')
$templateId = "355aed8a-864b-4e2b-b225-ea95482e7570"

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/default/read",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinitions -RolePermissions $rolePermissions -ResourceScopes $resourceScopes -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>RoleDefinition 上的读取操作

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
$customAdmin = Get-AzureADMSRoleDefinitions -ObjectId '86593cfc-114b-4a15-9954-97c3494ef49b'

# Get single role definition by templateId
$customAdmin = Get-AzureADMSRoleDefinitions -Filter "templateId eq '355aed8a-864b-4e2b-b225-ea95482e757not
```

### <a name="update-operations-on-roledefinition"></a>针对 RoleDefinition 的更新操作

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinitions -ObjectId $customAdmin.ObjectId -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>RoleDefinition 上的 Delete 操作

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -ObjectId $customAdmin.ObjectId
```

## <a name="operations-on-roleassignment"></a>针对 RoleAssignment 的操作

角色分配包含将给定安全主体 (用户或应用程序服务主体) 链接到角色定义的信息。 如果需要, 可以为分配的权限添加单一 Azure AD 资源的作用域。  内置和自定义角色限制权限的作用域。

### <a name="create-operations-on-roleassignment"></a>在 RoleAssignment 上创建操作

``` PowerShell
# Scopes to scope granted permissions to
$resourceScopes = @('/')

# IDs of principal and role definition you want to link
$principalId = "27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac"
$roleDefinitionId = $customKeyCredAdmin.ObjectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignments -ResourceScopes $resourceScopes -RoleDefinitionId -PrincipalId $principalId
```

### <a name="read-operations-on-roleassignment"></a>RoleAssignment 上的读取操作

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignments -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignments -Filter "principalId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>RoleAssignment 上的 Delete 操作

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignments -ObjectId $roleAssignment.ObjectId
```

## <a name="next-steps"></a>后续步骤

- 在[Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们共享。
- 有关角色和 azure AD 管理员角色分配的详细信息, 请参阅[分配管理员角色](directory-assign-admin-roles.md)。
- 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)。
