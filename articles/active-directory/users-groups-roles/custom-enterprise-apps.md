---
title: 企业应用访问分配的自定义角色权限-Azure Active Directory |Microsoft Docs
description: 为企业应用创建和分配自定义 Azure AD 角色 Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 439f31672b5471eb37752e1248704f3f26e44dc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994875"
---
# <a name="assign-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>分配自定义角色以在 Azure Active Directory 中管理企业应用

本文介绍如何创建一个自定义角色，该角色有权管理 Azure Active Directory (Azure AD) 中用户和组的企业应用分配。 有关角色分配的元素以及子类型、权限和属性集等术语的含义，请参阅 [自定义角色概述](roles-custom-overview.md)。

## <a name="enterprise-app-role-permissions"></a>企业应用程序角色权限

本文中讨论了两个企业应用权限。 所有示例都使用 "更新" 权限。

* 若要读取范围内的用户和组分配，请授予 `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` 权限
* 若要管理范围内的用户和组分配，请授予 `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` 权限

授予 "更新" 权限将导致工作负责人能够管理向企业应用程序分配的用户和组。 可以为单个应用程序或授予所有应用程序的用户和/或组分配的范围。 如果是在组织范围内授予的，则工作负责人可以管理所有应用程序的分配。 如果是在应用程序级别进行的，则工作负责人只能管理指定应用程序的分配。

授予 update 权限的步骤分为两个步骤：

1. 创建具有权限的自定义角色 `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. 向用户或组授予权限，以管理对企业应用的用户和组分配。 这是你可以将范围设置为组织范围级别或单个应用程序。

## <a name="use-the-azure-ad-admin-center"></a>使用 Azure AD 管理中心

### <a name="create-a-new-custom-role"></a>创建新的自定义角色

>[!NOTE]
> 自定义角色是在组织范围内创建和管理的，只在组织的 "概述" 页中提供。

1. 在组织中以特权角色管理员或全局管理员权限登录到 [Azure AD 管理中心](https://aad.portal.azure.com) 。
1. 依次选择“Azure Active Directory”、“角色和管理员”和“新建自定义角色”  。

    ![从 "角色" 列表中添加新的自定义角色 Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. 在 " **基本** 信息" 选项卡上，为角色名称提供 "管理用户和组分配"，并为角色说明提供 "管理用户和组分配的权限"，然后选择 " **下一步**"。

    ![提供自定义角色的名称和描述](./media/custom-enterprise-apps/role-name-and-description.png)

1. 在 " **权限** " 选项卡上，在 "搜索" 框中输入 "服务主体/appRoleAssignedTo/update"，然后选择所需权限旁边的复选框，然后选择 " **下一步**"。

    ![将权限添加到自定义角色](./media/custom-enterprise-apps/role-custom-permissions.png)

1. 在“查看 + 创建”选项卡上查看权限，然后选择“创建” 。

    ![现在，你可以创建自定义角色](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>使用 Azure AD 门户将角色分配给用户

1. 登录到具有特权角色管理员角色权限的 [Azure AD 管理中心](https://aad.portal.azure.com) 。
1. 依次选择“Azure Active Directory”、“角色和管理员” 。
1. 选择 " **授予管理用户和组分配的权限** " 角色。

    ![打开角色和管理员并搜索自定义角色](./media/custom-enterprise-apps/select-custom-role.png)

1. 选择 " **添加分配**"，选择所需的用户，然后单击 " **选择** " 将角色分配添加到用户。

    ![向用户添加自定义角色的分配](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>赋值提示

* 若要向工作负责人授予管理整个企业应用程序的用户和组访问权限的权限，请从组织的 Azure AD**概述**页上的组织范围的**角色和管理员**列表开始。
* 若要向工作负责人授予管理特定企业应用程序的用户和组访问权限，请在 Azure AD 中找到该应用，并在该应用的 **角色和管理员** 列表中打开该应用。 选择新的自定义角色并完成用户或组分配。 工作负责人只能管理特定应用的用户和组访问权限。
* 若要测试自定义角色分配，请以工作负责人身份登录，并打开应用程序的 " **用户和组** " 页，验证是否已启用 " **添加用户** " 选项。

    ![验证用户权限](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>使用 Azure AD PowerShell

有关更多详细信息，请参阅使用 PowerShell [创建和分配自定义角色](roles-create-custom.md) 并 [使用资源范围分配自定义角色](roles-assign-powershell.md)。

首先，从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安装 Azure AD PowerShell 模块。 然后使用以下命令导入 Azure AD PowerShell 预览版模块：

```powershell
PowerShell
import-module azureadpreview
```

若要验证该模块是否可供使用，请将以下命令返回的版本与此处列出的版本之一进行匹配：

```powershell
PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>创建自定义角色

使用以下 PowerShell 脚本创建新角色：

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =@( "microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>分配自定义角色

使用此 PowerShell 脚本分配角色。

```powershell
PowerShell
# Basic role information

$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="use-the-microsoft-graph-api"></a>使用 Microsoft Graph API

使用 Microsoft Graph API 中提供的示例创建自定义角色。 有关更多详细信息，请参阅 [创建和分配自定义角色](roles-create-custom.md) 并 [使用 Microsoft Graph API 分配自定义管理员角色](roles-assign-graph.md)。

用于创建自定义角色的 HTTP 请求。

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>使用 Microsoft 图形 API 分配自定义角色

角色分配结合了安全主体 ID (可以是用户或服务主体) 、角色定义 ID 和 Azure AD 资源范围。 有关角色分配的元素的详细信息，请参阅 [自定义角色概述](roles-custom-overview.md)

用于分配自定义角色的 HTTP 请求。

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>后续步骤

* [了解适用于企业应用的自定义角色权限](custom-enterprise-app-permissions.md)
