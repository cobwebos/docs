---
title: Azure Active Directory 中的权限 | Microsoft docs
description: 了解 Azure Active Directory 中的权限及其用法。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 6c0dc122-2cd8-4d70-be5a-3943459d308e
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: celested
ms.reviewer: jesakowi, justhu
ms.custom: aaddev
ms.openlocfilehash: db50a43e23d982722a4f3a7b663086863d915dd2
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579986"
---
# <a name="permissions-in-azure-active-directory"></a>Azure Active Directory 中的权限

Azure Active Directory (Azure AD) 对 OAuth 和 OpenID Connect (OIDC) 流广泛使用权限。 当应用从 Azure AD 接收访问令牌时，访问令牌将包含声明，这些声明描述了应用对特定资源的权限。 利用权限（也称为作用域），资源可以轻松进行授权，因为资源只需要检查令牌是否包含对应用要调用的 API 的合适权限。 

## <a name="types-of-permissions"></a>权限的类型

Azure AD 定义两种权限： 
* **委托的权限** - 由包含登录用户的应用使用。 对于这些应用，用户或管理员需许可应用请求的权限，并向应用授予委托的权限，以便在对 API 发出调用时，该应用可充当登录的用户。 根据具体的 API，用户可能无法直接许可 API，而是改为[要求管理员提供“管理员许可”。](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)
* **应用程序权限** - 由无需存在登录用户即可运行的应用使用；例如，以后台服务或守护程序形式运行的应用。 应用程序权限只能[由管理员许可](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant)，因为它们通常非常强大，允许访问跨用户边界的数据，或者访问仅限管理员访问的数据。 

有效权限是应用在对 API 发出请求时拥有的权限。 

* 对于委托的权限，应用的有效权限是（通过许可）授予应用的委托权限与当前登录用户的特权的最低特权交集。 应用的特权永远不会超过登录用户的特权。 在组织内部，可以通过策略或者一个或多个管理员角色的成员身份来确定登录用户的特权。 有关管理员角色的详细信息，请参阅[在 Azure AD 中分配管理员角色](../users-groups-roles/directory-assign-admin-roles.md)。
    例如，假设为应用授予了 Microsoft Graph 中的 `User.ReadWrite.All` 委托权限。 此权限在名义上会授予应用读取和更新组织中每个用户的个人资料的权限。 如果登录用户是全局管理员，则应用可以更新组织中每个用户的个人资料。 但是，如果登录用户不是充当管理员角色，则应用只能更新登录用户的个人资料。 它无法更新组织中其他用户的个人资料，因为该应用有权代表的用户没有这些特权。
* 对于应用程序权限，应用的有效权限是权限默示的完整特权级别。 例如，拥有 `User.ReadWrite.All` 应用程序权限的应用可以更新组织中每个用户的个人资料。 

## <a name="permission-attributes"></a>权限特性
Azure AD 中的权限提供多个属性用于帮助用户、管理员或应用开发人员在权限授予哪些对象的访问权限方面做出明智的决策。 

> [!NOTE]
> 可以使用 Azure 门户或 PowerShell 查看 Azure AD 应用程序或服务主体公开的权限。 请尝试运行以下脚本来查看 Microsoft Graph 公开的权限。
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| 属性名称 | Description | 示例 | 
| --- | --- | --- |
| `ID` | 这是唯一标识此权限的 GUID 值。 | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca | 
| `IsEnabled` | 指示此权限是否可供使用。 | 是 | 
| `Type` | 指示此权限是否需要用户许可或管理员许可。 | 用户 | 
| `AdminConsentDescription` | 这是在管理员许可体验期间向管理员显示的说明 | 允许应用读取用户邮箱中的电子邮件。 | 
| `AdminConsentDisplayName` | 这是在管理员许可体验期间向管理员显示的友好名称。 | 读取用户邮件 | 
| `UserConsentDescription` | 这是在用户许可体验期间向用户显示的说明。 |  允许应用读取你邮箱中的电子邮件。 | 
| `UserConsentDisplayName` | 这是在用户许可体验期间向用户显示的友好名称。 | 读取你的邮件 | 
| `Value` | 这是在 OAuth 2.0 授权流期间用于标识权限的字符串。 还可以将 `Value` 与应用 ID URI 字符串进行组合，以构成完全限定的权限名称。 | `Mail.Read` | 

## <a name="types-of-consent"></a>许可的类型
Azure AD 中的应用程序必须获得许可才能访问所需的资源或 API。 应用可能需要了解多种类型的许可才能成功访问。 在定义权限时，还需要了解用户如何获取应用或 API 的访问权限。

* **静态用户许可** - 在执行 [OAuth 2.0 授权流](v1-protocols-oauth-code.md#request-an-authorization-code)期间指定应用想要交互的资源时自动发生。 在静态用户许可方案中，应用必须已指定它在 Azure 门户的应用配置中所需的所有权限。 如果用户（或管理员）未授予此应用的许可，则 Azure AD 现在会提示用户提供许可。 

    详细了解如何注册请求访问一组静态 API 的 Azure AD 应用。
* **动态用户许可** - 是 v2 Azure AD 应用模型的一项功能。 在此方案中，你的应用请求它在 [v2 应用的 OAuth 2.0 授权流](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent)中所需的一组权限。 如果用户尚未许可，系统现在会提示他们许可。 [详细了解动态许可](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent)。

    > [!NOTE]
    > 动态许可有时十分方便，但对于管理员许可的权限而言，可能会带来很大的挑战，因为管理员许可体验在许可时不知道这些权限。 如果你需要管理员特权，则应用必须在 Azure 门户中注册这些权限。
  
* **管理员同意** - 当应用需要访问特定的高特权权限时必须执行。 管理员同意可以确保管理员在授权应用或用户访问组织中的高特权数据之前拥有某些额外的控制权。 [详细了解如何授予管理员许可](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)。

## <a name="best-practices"></a>最佳实践

### <a name="client-best-practices"></a>客户端最佳实践

- 仅请求你的应用需要的权限。 拥有过多权限的应用一旦泄密，则存在透露用户数据的风险。
- 根据你的应用支持的方案在委派的权限与应用程序权限之间进行选择。 
    - 如果调用是以用户身份执行的，请始终使用委派的权限。
    - 如果应用是非交互的并且不以任何特定用户的身份执行调用，请仅使用应用程序权限。 应用程序权限有很高的特权，只有绝对必要时才应使用。
- 使用基于 v2.0 终结点的应用时，请始终将静态权限（在应用程序注册中指定的那些权限）设置为你在运行时请求的动态权限（在代码中指定的并且在授权请求中作为查询参数发送的那些权限）的超集，以便管理员同意等方案可以正常工作。

### <a name="resourceapi-best-practices"></a>资源 /API 最佳做法

- 公开 API 的资源应当定义与它们所要保护的数据或操作相关的权限。 遵循此最佳做法有助于确保客户端最终不会有权访问它们不需要的数据，并合理告知用户他们需要许可的数据。
- 资源应该单独显式定义 `Read` 和 `ReadWrite` 权限。
- 资源应将允许访问跨用户边界的数据的所有权限标记为 `Admin` 权限。
- 资源应当遵循命名模式 `Subject.Permission[.Modifier]`，其中：
    - `Subject` 对应于可用的数据类型，
    - `Permission` 对应于用户可以对该数据执行的操作，并且 
    - 还可以使用 `Modifier` 来描述另一权限的专用化。 
    
    例如： 
    * Mail.Read - 允许用户读取邮件。
    * Mail.ReadWrite - 允许用户读取或写入邮件。
    * Mail.ReadWrite.All - 允许管理员或用户访问组织中的所有邮件。





