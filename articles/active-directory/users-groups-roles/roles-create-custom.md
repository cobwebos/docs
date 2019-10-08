---
title: 在 Azure AD 基于角色的访问控制-Azure Active Directory 中创建和分配自定义角色 |Microsoft Docs
description: 创建资源范围并将其分配给 Azure Active Directory 资源上的自定义 Azure AD 角色。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 09/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1a5aee1be7580956c32947e9bda7e2928a006c3
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026397"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>在 Azure Active Directory 中创建和分配自定义角色

本文介绍如何在 Azure Active Directory (Azure AD) 中创建新的自定义角色。 有关自定义角色的基础知识，请参阅[自定义角色概述](roles-custom-overview.md)。 角色只能在目录级别范围或应用注册资源范围分配。

自定义角色可以在 Azure AD 概览页的 [角色和管理员](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) 选项卡中创建。

## <a name="create-a-role-in-the-azure-portal"></a>在 Azure 门户中创建角色

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>创建新的自定义角色，授予管理应用注册所需的访问权限

1. 登录到 [Azure AD 管理中心](https://aad.portal.azure.com)"@no__t 1with" Azure AD 组织中的特权角色管理员或全局管理员权限。
1. 选择**Azure Active Directory**@no__t "**角色和管理员**" @no__t "**新建自定义角色**"。

   ![在“角色和管理员”页中创建或编辑角色](./media/roles-create-custom/new-custom-role.png)

1. 在“基本信息”选项卡中提供角色的名称和说明，然后单击“下一步”。

   ![在“基本信息”选项卡中提供自定义角色的名称和说明](./media/roles-create-custom/basics-tab.png)

1. 在“权限”选项卡中，选择管理应用注册的基本属性和凭据属性所需的权限。 如需每个权限的详细说明，请参阅 [Azure Active Directory 中的应用程序注册子类型和权限](./roles-custom-available-permissions.md)。
   1. 首先在搜索栏中输入“credentials”，然后选择 `microsoft.directory/applications/credentials/update` 权限。

      ![在“权限”选项卡上选择自定义角色的权限](./media/roles-create-custom/permissions-tab.png)

   1. 接下来，在搜索栏中输入“basic”，选择 `microsoft.directory/applications/basic/update` 权限，然后单击“下一步”。
1. 在“查看 + 创建”选项卡上查看权限，然后选择“创建”。

自定义角色将显示在要分配的可用角色的列表中。

## <a name="create-a-role-using-powershell"></a>使用 PowerShell 创建角色

### <a name="prepare-powershell"></a>准备 PowerShell

首先，必须[下载 Azure AD 预览版 PowerShell 模块](https://www.powershellgallery.com/packages/AzureADPreview)。

若要安装 Azure AD PowerShell 模块，请使用以下命令：

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

若要验证模块是否可供使用，请运行下面的命令：

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>创建自定义角色

使用以下 PowerShell 脚本创建新角色：

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 分配自定义角色

使用以下 PowerShell 脚本分配角色：

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-graph-api"></a>使用图形 API 创建角色

1. 创建角色定义。

    用于创建自定义角色定义的 HTTP 请求。

    发布

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. 创建角色分配。

    用于创建自定义角色定义的 HTTP 请求。

    发布

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Body

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>分配作用域为资源的自定义角色

与内置角色一样，默认情况下，在默认的组织范围内分配自定义角色，以授予对组织中所有应用注册的访问权限。 但与内置角色不同的是，也可以在单个 Azure AD 资源的作用域中分配自定义角色。 这样，就可以向用户授予更新单个应用程序的凭据和基本属性的权限，而无需创建第二个自定义角色。

1. 通过 Azure AD 组织中的应用程序开发人员权限登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择“应用注册”。
1. 选择要向其授予管理权限的应用注册。 可能必须选择“所有应用程序”，以便在 Azure AD 组织中查看应用注册的完整列表。

    ![选择应用注册作为角色分配的资源范围](./media/roles-create-custom/appreg-all-apps.png)

1. 在应用注册中，选择“角色和管理员”。 如果尚未创建，请按[上一过程](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)中的说明操作。

1. 选择角色以打开“分配”页。
1. 选择“添加分配”以添加用户。 系统仅向用户授予所选应用注册的任何权限。

## <a name="next-steps"></a>后续步骤

- 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
- 有关角色以及管理员角色分配的详细信息，请参阅[分配管理员角色](directory-assign-admin-roles.md)。
- 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)。
