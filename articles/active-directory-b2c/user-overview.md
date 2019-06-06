---
title: Azure Active Directory B2C 中的用户帐户概述 | Microsoft Docs
description: 了解 Azure Active Directory B2C 中的用户帐户。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 42a38b47f9e49652b5fe80aa5bbd0047dc7d1e74
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511960"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的用户帐户概述

在 Azure Active Directory (Azure AD) B2C 中，可以使用不同类型的帐户。 可以使用的用户帐户类型中的 Azure Active Directory、Azure Active Directory B2B 和 Azure Active Directory B2C 共享。

可以使用以下类型的帐户：

- 工作帐户  - 工作帐户可以访问租户中的资源，并且通过管理员角色可以管理租户。
- 来宾帐户  - 来宾帐户只能是一个 Microsoft 帐户或可用于访问应用程序或管理租户的 Azure Active Directory 用户。 
- **使用者帐户** - 通过执行 Azure AD B2C 应用程序中的注册用户流或使用 Azure AD Graph API 创建使用者帐户，并由注册 Azure AD B2C 的应用程序的用户使用。 

## <a name="work-account"></a>工作帐户

基于 Azure AD 的所有租户以相同的方式创建工作帐户。 若要创建工作帐户，可以使用[快速入门：向 Azure Active Directory 添加新用户](../active-directory/fundamentals/add-users-azure-active-directory.md)中的信息。 使用 Azure 门户中的“新建用户”  选项创建工作帐户。

添加新工作帐户时，需要考虑以下配置设置：

- “名称”  和“用户名”  - Name  属性包含用户的名和姓。 用户名  是用户登录时输入的标识符。 用户名包括完整域。 用户名的域名部分必须是初始默认域名“your-domain.onmicrosoft.com”  ，或已验证的非联合[自定义域名](../active-directory/fundamentals/add-custom-domain.md)（例如“contoso.com”  ）。
- 配置文件  - 通过用户数据的配置文件设置帐户。 可以输入名字、姓氏、职务和部门名称。 可以在创建帐户后编辑配置文件。
- 组  - 使用组执行管理任务，例如一次为多个用户或设备分配许可证或权限。 可以将新帐户放入租户现有的[组](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)中。 
- 目录角色  - 需要指定用户帐户对租户中资源的访问级别。 可以使用以下权限级别：

    - 用户  - 用户可以访问分配的资源，但不能管理大多数租户资源。
    - 全局管理员  - 全局管理员可以完全控制所有租户资源。
    - 受限的管理员  - 为用户选择一个或多个管理角色。 有关可以选择的角色的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。 

### <a name="create-a-work-account"></a>创建工作帐户

可以使用以下信息创建新的工作帐户：

- [Azure 门户](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>更新用户配置文件

可以使用以下信息来更新用户的配置文件：

- [Azure 门户](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>重置用户密码

可以使用以下信息来重置用户密码： 

- [Azure 门户](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>来宾用户

可以邀请外部用户作为来宾用户访问租户。 邀请来宾用户访问 Azure AD B2C 租户的典型方案是共享管理职责。 有关使用来宾帐户的示例，请参阅 [Azure Active Directory B2B 协作用户的属性](../active-directory/b2b/user-properties.md)。

在邀请来宾用户访问租户时，你需要提供收件人的电子邮件地址以及描述邀请的消息。 邀请链接会将用户转到同意页面，在该页面选中“开始使用”  按钮并接受权限审核。 如果未将收件箱附加到电子邮件地址，则用户可以通过使用受邀凭据转到 Microsoft 页面来导航到同意页面。 然后，强制用户兑换邀请，方法就是单击电子邮件中的链接。 例如：`https://myapps.microsoft.com/B2CTENANTNAME`。

还可以使用 [Microsoft Graph API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) 来邀请来宾用户。

## <a name="consumer-user"></a>使用者用户

使用者用户可以登录受 Azure AD B2C 保护的应用程序，但无法访问 Azure 门户等 Azure 资源。  使用者用户可以使用本地帐户或联合帐户，例如 Facebook 或 Twitter。 使用[注册或登录用户流](../active-directory-b2c/active-directory-b2c-reference-policies.md)创建使用者帐户。

可以指定使用自定义用户属性创建使用者用户帐户时收集的数据。 有关详细信息，请参阅在[在 Azure Active Directory B2C 中定义自定义属性](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md)。

可以使用[使用 Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) 的创建使用者用户帐户  部分中的信息创建 Azure AD B2C 使用者帐户。 还可以使用同一文章中的“更新使用者用户帐户”  部分中的信息来管理帐户的属性。

### <a name="migrate-consumer-user-accounts"></a>迁移使用者用户帐户

可能需要将现有的使用者用户帐户从任何标识提供者迁移到 Azure AD B2C。 有关详细信息，请参阅[用户迁移](active-directory-b2c-user-migration.md)或[迁移具有社交标识的用户](active-directory-b2c-social-migration.md)。
