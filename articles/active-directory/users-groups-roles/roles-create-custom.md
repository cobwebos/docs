---
title: 在 Azure AD 基于角色的访问控制中创建自定义角色 | Microsoft Docs
description: 通过 Azure Active Directory 资源的资源范围创建和分配自定义 Azure AD 角色。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e172a023cc9156f435b4f40b2262ee44128c138e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731998"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>在 Azure Active Directory 中创建和分配自定义角色

本文介绍如何在 Azure Active Directory (Azure AD) 中创建新的自定义角色。 有关自定义角色的基本信息，请参阅[自定义角色概述](roles-custom-overview.md)。 只能在目录级范围或应用注册资源范围中分配角色。

自定义角色可以在 Azure AD 概述页上的[角色和管理员](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)选项卡中创建。

## <a name="create-a-role-in-the-azure-portal"></a>在 Azure 门户中创建角色

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>创建新的自定义角色以授予管理应用注册的访问权限

1. 使用 Azure AD 组织中的特权角色管理员或全局管理员权限登录到  [Azure AD 管理中心](https://aad.portal.azure.com) 。
1. 选择“Azure Active Directory” > “角色和管理员” > “新建自定义角色”  。

   ![从“角色和管理员”页创建或编辑角色](./media/roles-create-custom/new-custom-role.png)

1. 在“基本信息”页上，提供角色的名称和说明，然后单击“下一个” 。

   ![在“基本信息”选项卡上提供自定义角色的名称和说明](./media/roles-create-custom/basics-tab.png)

1. 在“权限”选项卡上，选择管理应用注册的基本属性和凭据属性所需的权限。 有关每个权限的详细说明，请参阅[应用程序注册子类型和 Azure Active Directory 中的权限](./roles-custom-available-permissions.md)。
   1. 首先，在搜索栏中输入“凭据”，然后选择 `microsoft.directory/applications/credentials/update` 权限。

      ![在“权限”选项卡上选择自定义角色的权限](./media/roles-create-custom/permissions-tab.png)

   1. 接下来，在搜索栏中输入“基本信息”，选择 `microsoft.directory/applications/basic/update` 权限，然后单击“下一个”。
1. 在“查看 + 创建”选项卡上查看权限，然后选择“创建” 。

你的自定义角色将显示在要分配的可用角色列表中。

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

    创建自定义角色定义的 HTTP 请求。

    POST

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

  > [!Note]
  > “templateId”：“GUID”是根据需要在正文中发送的可选参数。 如果需要使用公共参数创建多个不同的自定义角色，最好创建一个模板并定义 templateId。 可以使用 powershell cmdlet (New-Guid).Guid 预先生成 templateId。 

1. 创建角色分配。

    创建自定义角色定义的 HTTP 请求。

    POST

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


## <a name="assign-a-custom-role-scoped-to-a-resource"></a>为资源分配自定义角色范围

与内置角色一样，默认情况下在默认组织范围内分配自定义角色，以授予对组织中所有应用注册的访问权限。 但与内置角色不同，自定义角色也可以在单个 Azure AD 资源的范围中分配。 这允许你授予用户更新单个应用的凭据和基本属性的权限，而无需创建第二个自定义角色。

1. 使用 Azure AD 组织中的应用程序开发人员权限登录到  [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择 **“应用注册”** 。
1. 选择要授予其管理权限的应用注册。 可能需要选择“所有应用程序”才能查看 Azure AD 组织中应用注册的完整列表。

    ![选择应用注册作为角色分配的资源范围](./media/roles-create-custom/appreg-all-apps.png)

1. 在应用注册中，选择“角色和管理员”。 如果尚未创建，请参照[上述过程](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)中的说明。

1. 选择角色以打开“分配”页面。
1. 选择“添加分配”以添加用户。 将仅向用户授予所选应用注册的任何权限。

## <a name="next-steps"></a>后续步骤

- 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
- 有关角色以及管理员角色分配的详细信息，请参阅[分配管理员角色](directory-assign-admin-roles.md)。
- 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)。
