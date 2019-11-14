---
title: 可用的自定义管理员角色权限-Azure AD |Microsoft Docs
description: 用于委托标识管理的自定义管理员角色权限。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025150"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Azure Active Directory 中的应用程序注册子类型和权限

本文包含 Azure Active Directory (Azure AD) 中的自定义角色定义当前可用的应用注册权限。

## <a name="permissions-for-managing-single-directory-applications"></a>管理单目录应用程序的权限

选择自定义角色的权限时，你可以选择授予访问权限，以便仅管理单一目录应用程序。 单目录应用程序仅可用于注册了应用程序的 Azure AD 组织中的用户。 单目录应用程序定义为仅在此组织目录中将**受支持的帐户类型**设置为 "帐户"。 在图形 API 中，单目录应用程序的 signInAudience 属性设置为 "AzureADMyOrg"。

若要授予访问权限以仅管理单一目录应用程序，请将下面的权限用于子类型**myOrganization**。 例如 microsoft.directory/applications.myOrganization/basic/update。

有关一般术语“子类型”、“权限”和“属性集”的含义，请参阅[自定义角色概述](roles-custom-overview.md)。 以下信息特定于应用程序注册。

### <a name="create-and-delete"></a>create 和 delete

可以使用两个权限来授予创建应用程序注册的能力，这两个权限各自有不同的行为：

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

分配此权限会导致将创建者添加为所创建应用注册的第一个所有者，创建的应用注册将计入到创建者的“创建 250 个对象”配额中。

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

分配此权限会导致不将创建者添加为所创建应用注册的第一个所有者，创建的应用注册不会计入到创建者的“创建 250 个对象”配额中。 请慎用此权限，因为在达到目录级配额之前，没有任何办法可阻止被分配者创建应用注册。 如果同时分配上述两个权限，此权限优先。

如果同时分配上述两个权限，/create 权限优先。 尽管 /createAsOwner 权限不会自动将创建者添加为第一个所有者，但使用图形 API 或 PowerShell cmdlet 时，可以在创建应用注册期间指定所有者。

create 权限授予对“新建注册”命令的访问权限。

[这些权限授予对“新建注册”门户命令的访问权限](./media/roles-create-custom/new-custom-role.png)

可使用两个权限授予删除应用注册的能力：

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

不管子类型是什么（单租户和多租户应用程序），都授予删除应用注册的能力。

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganization/delete

授予删除仅供组织中帐户或单租户应用程序（myOrganization 子类型）访问的应用注册的能力。

![这些权限授予对“删除应用注册”命令的访问权限](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> 分配包含 create 权限的角色时，必须在目录范围进行角色分配。 在资源范围分配的 create 权限不会授予创建应用注册的能力。

### <a name="read"></a>读取

默认情况下，组织中的所有成员用户都可以读取应用注册信息。 但是，来宾用户和应用程序服务主体无法读取这些信息。 如果你打算将某个角色分配给来宾用户或应用程序，必须包含相应的 read 权限。

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

能够读取无法在任何情况下（如凭据）读取的属性之外的单租户应用程序和多租户应用程序的所有属性。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

授予的权限与 microsoft.directory/applications/allProperties/read 相同，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

授予读取单租户和多租户应用程序中的所有者属性的能力。 授予对应用程序注册所有者页上的所有字段的访问权限：

![此权限授予对应用注册所有者页的访问权限](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft 目录/应用程序/标准/读取

授予读取标准应用程序注册属性的访问权限。 这包括跨应用程序注册页的属性。

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

授予的权限与 microsoft.directory/applications/standard/read 相同，但仅适用于单租户应用程序。

### <a name="update"></a>更新

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

能够更新单个目录和多目录应用程序上的所有属性。

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

授予的权限与 microsoft.directory/applications/allProperties/update 相同，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

能够更新单个目录和多目录应用程序上支持的帐户类型（signInAudience）属性。

![此权限在 "身份验证" 页上授予对应用注册支持的帐户类型属性的访问权限](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

授予的权限与 microsoft.directory/applications/audience/update 相同，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

可更新单租户和多租户应用程序中的回复 URL、注销 URL、隐式流和发布者域属性。 授予对应用程序注册身份验证页上的所有字段（支持的帐户类型除外）的访问权限：

![授予对应用注册身份验证（支持的帐户类型除外）的访问权限](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

授予的权限与 microsoft.directory/applications/authentication/update 相同，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

可更新单租户和多租户应用程序中的名称、徽标、主页 URL、服务条款 URL 和隐私声明 URL 属性。 授予对应用程序注册品牌页上的所有字段的访问权限：

![此权限授予对应用注册品牌页的访问权限](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

授予的权限与 microsoft.directory/applications/basic/update 相同，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

可更新单租户和多租户应用程序中的证书和客户端机密属性。 授予对应用程序注册证书和机密页上的所有字段的访问权限：

![此权限授予对应用注册证书和机密页的访问权限](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

授予的权限与 microsoft.directory/applications/credentials/update 相同，但仅适用于单目录应用程序。

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

可更新单租户和多租户应用程序中的所有者属性。 授予对应用程序注册所有者页上的所有字段的访问权限：

![此权限授予对应用注册所有者页的访问权限](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

授予的权限与 microsoft.directory/applications/owners/update 相同，但仅适用于单租户应用程序。

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

可更新单租户和多租户应用程序中的委托权限、应用程序权限、已授权的客户端应用程序、所需权限和授予许可属性。 不授予执行许可的能力。 授予对应用程序注册 API 权限和公开 API 页上的所有字段的访问权限：

![此权限授予对应用注册 API 权限页的访问权限](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![此权限授予对应用注册公开 API 页的访问权限](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

授予的权限与 microsoft.directory/applications/permissions/update 相同，但仅适用于单租户应用程序。

## <a name="required-license-plan"></a>所需的许可计划

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>后续步骤

- 使用 [Azure 门户、Azure AD PowerShell 和图形 API](roles-create-custom.md) 创建自定义角色
- [查看自定义角色的分配](roles-view-assignments.md)
