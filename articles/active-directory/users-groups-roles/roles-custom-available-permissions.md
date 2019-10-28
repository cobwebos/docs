---
title: 应用注册管理的自定义管理员角色权限-Azure Active Directory |Microsoft Docs
description: 委托标识管理的自定义管理员角色权限。
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
ms.openlocfilehash: 7ebffbc1c451404d93383a483eecdd4a25acf6b6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72928622"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Azure Active Directory 中的应用程序注册子类型和权限

本文包含 Azure Active Directory （Azure AD）中的自定义角色定义的当前可用应用注册权限。

## <a name="permissions-for-managing-single-directory-applications"></a>管理单目录应用程序的权限

选择自定义角色的权限时，你可以选择授予访问权限，以便仅管理单一目录应用程序。 单目录应用程序仅可用于注册了应用程序的 Azure AD 组织中的用户。 单目录应用程序定义为仅在此组织目录中将**受支持的帐户类型**设置为 "帐户"。 在图形 API 中，单目录应用程序的 signInAudience 属性设置为 "AzureADMyOrg"。

若要授予访问权限以仅管理单一目录应用程序，请将下面的权限用于子类型**myOrganization**。 例如，myOrganization/basic/update。

有关常规术语 "子类型"、"权限" 和 "属性集" 的含义，请参阅[自定义角色概述](roles-custom-overview.md)。 以下信息特定于应用程序注册。

### <a name="create-and-delete"></a>创建和删除

有两个权限可用于授予创建应用程序注册的能力，每个权限都有不同的行为：

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft 目录/应用程序/createAsOwner

分配此权限会导致将创建者添加为所创建的应用注册的第一个所有者，并且创建的应用注册将计入创建者的250创建对象配额。

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft. 目录/应用程序/创建

如果分配此权限，则不会将创建者添加为所创建的应用注册的第一个所有者，并且创建的应用注册将不会在创建者的250创建对象配额的情况下进行计数。 请谨慎使用此权限，因为在达到目录级配额之前，不会阻止代理人创建应用注册。 如果同时分配了这两个权限，则此权限优先。

如果同时分配了这两个权限，则/create 权限将优先。 尽管/createAsOwner 权限不会自动添加创建者作为第一个所有者，但当使用图形 Api 或 PowerShell cmdlet 时，可以在创建应用注册期间指定所有者。

Create 权限：授予访问**新注册**命令的权限。

[这些权限授予对新注册门户命令的访问权限](./media/roles-create-custom/new-custom-role.png)

有两个权限可用于授予删除应用注册的能力：

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft. directory/应用程序/删除

授予删除应用注册的功能，而不考虑子类型。这就是单租户应用程序和多租户应用程序。

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft myOrganization/delete

授予删除仅可供组织中的帐户或单租户应用程序（myOrganization 子类型）访问的应用注册的权限。

![这些权限向删除应用注册命令授予访问权限](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> 分配包含 create 权限的角色时，必须在目录范围内进行角色分配。 在资源范围分配的 create 权限不会授予创建应用注册的能力。

### <a name="read"></a>读取

默认情况下，组织中的所有成员用户都可以读取应用注册信息。 但是，来宾用户和应用程序服务主体不能。 如果计划将角色分配给来宾用户或应用程序，则必须包含相应的读取权限。

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft 目录/应用程序/allProperties/读取

能够读取无法在任何情况下（如凭据）读取的属性之外的单租户应用程序和多租户应用程序的所有属性。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>myOrganization/allProperties/read

授予与 microsoft. directory/applications/allProperties/read 相同的权限，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft. directory/应用程序/所有者/读取

授予在单租户应用程序和多租户应用程序上读取所有者属性的功能。 授予对 "应用程序注册所有者" 页上所有字段的访问权限：

![此权限授予对 "应用注册所有者" 页的访问权限](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft 目录/应用程序/标准/读取

授予读取标准应用程序注册属性的访问权限。 这包括跨应用程序注册页的属性。

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>myOrganization/standard/read

授予的权限与 microsoft 目录/应用程序/标准/读取相同，但仅适用于单租户应用程序。

### <a name="update"></a>更新

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft 目录/应用程序/allProperties/更新

能够更新单个目录和多目录应用程序上的所有属性。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>myOrganization/allProperties/update

授予与 allProperties/update 相同的权限，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft. 目录/应用程序/受众/更新

能够更新单个目录和多目录应用程序上支持的帐户类型（signInAudience）属性。

![此权限在 "身份验证" 页上授予对应用注册支持的帐户类型属性的访问权限](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>myOrganization/受众/更新

授予与 microsoft 目录/应用程序/受众/更新相同的权限，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft. 目录/应用程序/身份验证/更新

能够更新单租户应用程序和多租户应用程序上的回复 URL、注销 URL、隐式流和发布服务器域属性。 除了支持的帐户类型之外，授予对应用程序注册身份验证页上的所有字段的访问权限：

![授予对应用注册身份验证的访问权限，但不支持此类帐户类型](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>myOrganization/authentication/authentication/update

授予与 microsoft 目录/应用程序/身份验证/更新相同的权限，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft 目录/应用程序/基本/更新

能够更新单租户应用程序和多租户应用程序的名称、徽标、主页 URL、服务条款 URL 和隐私声明 URL 属性。 授予对应用程序注册品牌页面上所有字段的访问权限：

![此权限授予对应用注册品牌页的访问权限](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>myOrganization/basic/update

授予与 microsoft 目录/应用程序/基本/更新相同的权限，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft 目录/应用程序/凭据/更新

能够更新单租户应用程序和多租户应用程序上的证书和客户端密码属性。 授予对 "应用程序注册证书" & 机密 "页上所有字段的访问权限：

![此权限授予对应用注册证书 & 机密 "页的访问权限](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>myOrganization/凭据/更新

授予与 microsoft 目录/应用程序/凭据/更新相同的权限，但仅适用于单目录应用程序。

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft. 目录/应用程序/所有者/更新

能够更新单个租户和多租户的所有者属性。 授予对 "应用程序注册所有者" 页上所有字段的访问权限：

![此权限授予对 "应用注册所有者" 页的访问权限](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>myOrganization/物主/物主/更新

授予与 microsoft 目录/应用程序/所有者/更新相同的权限，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft. 目录/应用程序/权限/更新

能够更新委托权限、应用程序权限、授权客户端应用程序、所需权限，以及在单租户应用程序和多租户应用程序上授予许可属性。 不允许执行许可。 授予对应用程序注册 API 权限的所有字段的访问权限，并公开 API 页面：

![此权限授予对 "应用注册 API 权限" 页的访问权限](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![此权限授予对应用注册的访问权限公开 API 页面](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>myOrganization/权限/更新

授予与 microsoft 目录/应用程序/权限/更新相同的权限，但仅适用于单租户应用程序。

## <a name="required-license-plan"></a>所需的许可计划

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>后续步骤

- 使用[Azure 门户、Azure AD PowerShell 和图形 API](roles-create-custom.md)创建自定义角色
- [查看自定义角色的分配](roles-view-assignments.md)
