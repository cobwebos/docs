---
title: 在 Azure AD 基于角色的访问控制-Azure Active Directory 中创建自定义角色定义 |Microsoft Docs
description: Azure Active Directory 资源上的资源范围创建自定义 Azure AD 角色。
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
ms.openlocfilehash: ae773bc1a4e1831dbe462149bb827c26b7e74b96
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722318"
---
# <a name="create-a-custom-role-and-assign-a-resource-scope-in-azure-active-directory"></a>在 Azure Active Directory 中创建自定义角色并分配资源范围

本文介绍如何在 Azure Active Directory (Azure AD) 中创建新的自定义角色。 可以在 "Azure AD 概述" 页或["应用程序注册" 页](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)上的 "**角色和管理员**" 选项卡中创建自定义角色。 可以在目录级别的作用域中分配角色, 也可以将其限定为仅限应用注册。

有关详细信息, 请参阅自[定义角色概述](roles-custom-overview.md)。

## <a name="using-the-azure-ad-portal"></a>使用 Azure AD 门户

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>创建新的自定义角色, 以授予管理应用注册的访问权限

1. 在 Azure AD 组织中, 以特权角色管理员或全局管理员权限登录到 [Azure AD 管理中心](https://aad.portal.azure.com) 。
1. 选择 " **Azure Active Directory** > **角色和管理员** >" " **新建自定义角色**"。

   ![从 "角色和管理员" 页创建或编辑角色](./media/roles-create-custom/new-custom-role.png)

1. 在 "**基本**信息" 选项卡上, 为角色提供名称和说明。

   ![在 "基本信息" 选项卡上提供自定义角色的名称和说明](./media/roles-create-custom/basics-tab.png)

1. 选择管理应用注册凭据所需的权限和基本属性, 如 name:
   1. 在搜索栏中输入 "凭据", 然后选择`microsoft.directory/applications/credentials/update`权限。

      ![在 "权限" 选项卡上选择自定义角色的权限](./media/roles-create-custom/permissions-tab.png)

   1. 在搜索栏中输入 "基本", 选择权限`microsoft.directory/applications/basic/update` , 然后单击 "**下一步**"。
1. 在 "**查看**" 和 "创建" 选项卡上, 检查权限, 然后选择 "**创建**"。

你的自定义角色将显示在要分配的可用角色列表中。

## <a name="assign-a-role-scoped-to-a-resource"></a>分配作用域为资源的角色

与内置角色一样, 可以在默认的组织范围内分配自定义角色, 以授予对所有应用注册的访问权限。 但也可以在对象范围内分配自定义角色。 这样, 就可以向工作负责人授予更新单个应用的凭据和基本属性的权限, 而无需创建第二个自定义角色。

1. 通过 Azure AD 组织中的应用程序开发人员权限登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择“应用注册”。
1. 选择要向其授予访问权限的应用注册。 可能需要选择 "**所有应用程序**" 才能查看 Azure AD 组织中的应用注册的完整列表。

    ![选择应用注册作为角色分配的资源范围](./media/roles-create-custom/appreg-all-apps.png)

1. 在应用注册中, 选择 "**角色和管理员**"。 如果尚未创建一个说明, 请按照[前面的过程](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations)进行操作。

    如果从打开的应用注册的上下文分配此角色, 则工作负责人对该单个应用注册具有权限。 要分配的角色会在每个应用注册的列表中显示。 此访问模型, 其中的所有者可以按角色分配对特定 Azure AD 资源的权限, 这与 azure [RBAC](../../role-based-access-control/overview.md)中用于 azure 资源访问控制的模型类似。

1. 选择 "角色" 以打开 "**分配**" 页。
1. 选择 "**添加分配**" 以添加用户。 不会向该用户授予除所选应用程序注册以外的任何其他任何应用程序的权限。

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>使用 Azure AD PowerShell 创建自定义角色

### <a name="prepare-powershell"></a>准备 PowerShell

首先，必须[下载 Azure AD PowerShell 模块](https://www.powershellgallery.com/packages/AzureAD/)。

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

### <a name="create-the-custom-role"></a>创建自定义角色

使用以下 PowerShell 脚本创建新的角色:

``` PowerShell
# Basic role information
$description = "Application Support Administrator"
$displayName = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/allProperties/read",
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customAdmin = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>使用 Microsoft Graph API 创建自定义角色

1. 创建角色定义。

    用于创建自定义角色定义的 HTTP 请求。

    发布

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Body

    ``` HTTP
    {
    "description":"Can manage basic aspects of application registrations.",
    "displayName":"Application Support Administrator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "allowedResourceActions": 
            [
                "microsoft.directory/applications/basic/read",
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
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions/roleAssignments
    ```

    Body

    ``` HTTP
    {
    "principalId":"<GUID OF USER>",
    "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
    "resourceScope":["/<GUID OF APPLICATION REGISTRATION>"]
    }
    ```

## <a name="next-steps"></a>后续步骤

- 欢迎在 [Azure AD 管理角色论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上与我们分享知识。
- 有关角色以及管理员角色分配的详细信息，请参阅[分配管理员角色](directory-assign-admin-roles.md)。
- 有关默认用户权限，请参阅[默认来宾和成员用户权限的比较](../fundamentals/users-default-permissions.md)。
