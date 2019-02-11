---
title: 了解 Azure Active Directory 应用清单 | Microsoft Docs
description: 详细介绍 Azure Active Directory 应用清单，该清单表示 Azure AD 租户中的应用程序标识配置，并方便实现 OAuth 授权、许可体验和其他功能。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: a6a6ae3c89d0a9ee8177b4e943ab53f0c6680c1b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097701"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 应用清单

应用程序清单包含 Microsoft 标识平台中的某个应用程序对象的所有属性的定义。 它还充当用于更新应用程序对象的机制。 有关应用程序实体及其架构的详细信息，请参阅[图形 API 应用程序实体文档](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)。

可以通过 Azure 门户或者使用 Microsoft Graph 以编程方式配置应用的属性。 但是，在某些情况下，需要编辑应用清单来配置应用的属性。 这些方案包括：

* 如果已将应用注册为 Azure AD 多租户和个人 Microsoft 帐户，则不能在 UI 中更改支持的 Microsoft 帐户。 而是必须使用应用程序清单编辑器来更改支持的帐户类型。
* 如果需要定义你的应用支持的权限和角色，则必须修改应用程序清单。

## <a name="configure-the-app-manifest"></a>配置应用清单

若要配置应用程序清单，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择“Azure Active Directory”服务，然后选择“应用注册”或“应用注册(预览版)”。
1. 选择要配置的应用。
1. 在应用的“概览”页中，选择“清单”部分。 此时会打开一个基于 Web 的清单编辑器，可在其中编辑门户中的清单。 （可选）可以选择“下载”以在本地编辑清单，然后使用“上传”将清单重新应用于应用程序。

## <a name="manifest-reference"></a>清单参考

> [!NOTE]
> 如果看不到“说明”后的“示例值”列，请最大化浏览器窗口并滚动/轻扫，直至看到“示例值”列。

| 密钥  | 值类型 | 说明  | 示例值 |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | 可为 Null 的 Int32 | 指定资源所需的访问令牌版本。 这会更改独立于用于请求访问令牌的终结点或客户端生成的 JWT 的版本和格式。<br/><br/>使用的端点 v1.0 或 v2.0 由客户端选择，仅影响 id_tokens 的版本。 资源需要显式配置 `accesstokenAcceptedVersion` 以指示受支持的访问令牌格式。<br/><br/>`accesstokenAcceptedVersion` 的可能值为 1、2 或为 null。 如果值为 null，则默认为 1，这对应于 v1.0 终结点。 | `2` |
| `allowPublicClient` | 布尔值 | 指定回退应用程序类型。 默认情况下，Azure AD 基于 replyUrlsWithType 推断应用程序类型。 某些情况下，Azure AD 无法确定客户端应用类型（例如，其中发生了没有 URL 重定向的 HTTP 请求的 [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) 流）。 在这种情况下，Azure AD 将基于此属性的值解释应用程序类型。 如果此值设置为 true，则回退应用程序类型设置为公共客户端，例如在移动设备上运行的已安装应用。 默认值为 false，这意味着，回退应用程序类型为机密，例如 Web 应用。 | `false` |
| `appId` | 标识符字符串 | 指定由 Azure AD 分配给应用的应用唯一标识符。 | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | 数组类型 | 指定应用可以声明的角色集合。 可将这些角色分配给用户、组或服务主体。 有关更多示例和信息，请参阅[在应用程序中添加应用角色并在令牌中接收它们](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `groupMembershipClaims` | 字符串 | 配置应用所需的用户访问令牌或 OAuth 2.0 访问令牌中颁发的 `groups` 声明。 要设置此属性，请使用以下有效字符串值之一：<br/><br/>- `"None"`<br/>- `"SecurityGroup"`（适用于安全组和 Azure AD 角色）<br/>- `"All"`（该操作可获取登录用户所属的所有安全组、通讯组和 Azure AD 目录角色。 | `"SecurityGroup"` |
| `optionalClaims` | 字符串 | 此特定应用的安全令牌服务在令牌中返回的可选声明。<br>目前，同时支持个人帐户和 Azure AD（已通过应用注册门户注册）的应用无法使用可选声明。 但是，使用 v2.0 终结点仅为 Azure AD 注册的应用可以获取它们在清单中请求的可选声明。 有关详细信息，请参阅[可选声明](active-directory-optional-claims.md)。 | `null` |
| `id` | 标识符字符串 | 应用在目录中的唯一标识符。 此 ID 不是用于在任何协议事务中标识应用的标识符。 用于引用目录查询中的对象。 | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | 字符串数组 | 用户定义的 URI，用于在 Web 应用的 Azure AD 租户中唯一标识该应用程序；如果应用是多租户的，则用于在已验证的自定义域中唯一标识该应用。 | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | 字符串 | 指定应用服务条款和隐私声明的链接。 服务条款和隐私声明通过用户同意体验展示给用户。 有关详细信息，请参阅[如何：为已注册的 Azure AD 应用添加服务条款和隐私声明](howto-add-terms-of-service-privacy-statement.md)。 | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | 数组类型 | 包含对应用所分配的凭据、基于字符串的共享机密和 X.509 证书的引用。 在请求访问令牌时，可使用这些凭据（如果应用充当客户端而不是资源）。 | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | 数组类型 | 如果解决方案包含两个部分：客户端应用和自定义 Web API 应用，则该值用于捆绑许可。 如果在此值中输入客户端应用的 appID，则用户只需许可该客户端应用一次。 Azure AD 将知道许可客户端意味着隐式许可 Web API，并自动为客户端和 Web API 同时预配服务主体。 客户端和 Web API 应用都必须在同一个租户中注册。 | `[GUID]` |
| `logoUrl` | 字符串 | 只读值，指向已在门户中上传的徽标的 CDN URL。 | `https://MyRegisteredAppLogo` |
| `logoutUrl` | 字符串 | 用于注销应用的 URL。 | `https://MyRegisteredAppLogout` |
| `name` | 字符串 | 应用的显示名称。 | `MyRegisteredApp` |
| `oauth2AllowImplicitFlow` | 布尔值 | 指定此 Web 应用是否可以请求 OAuth2.0 隐式流访问令牌。 默认值为 false。 此标志用于基于浏览器的应用，例如 Javascript 单页应用。 若要了解详细信息，请在目录中输入 `OAuth 2.0 implicit grant flow`，并查看有关隐式流的主题。 | `false` |
| `oauth2AllowIdTokenImplicitFlow` | 布尔值 | 指定此 Web 应用是否可以请求 OAuth2.0 隐式流 ID 令牌。 默认值为 false。 此标志用于基于浏览器的应用，例如 Javascript 单页应用。 | `false` |
| `oauth2Permissions` | 数组类型 | 指定 Web API（资源）应用向客户端应用公开的 OAuth 2.0 权限范围集合。 在许可期间，可将这些权限范围授予客户端应用。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | 布尔值 | 指定在 OAuth 2.0 令牌请求过程中，Azure AD 是否允许与 GET 请求相反的 POST 请求。 默认值为 false，即指定只允许 GET 请求。 | `false` |
| `parentalControlSettings` | 字符串 | `countriesBlockedForMinors` 指定禁止未成年人使用该应用的国家/地区。<br>`legalAgeGroupRule` 指定适用于应用用户的法定年龄组规则。 可设置为 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids` 或 `BlockMinors`。  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | 数组类型 | 请参阅 `keyCredentials` 属性的说明。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | 数组类型 | 列出了应用程序和针对隐式同意的所请求权限。 要求管理员已针对应用程序表示同意。 preAuthorizedApplications 不要求用户同意所请求的权限。 preAuthorizedApplications 中列出的权限不要求用户同意。 但是，preAuthorizedApplications 中未列出的任何其他所请求权限都要求用户同意。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `replyUrlsWithType` | 字符串数组 | 此多值属性保存 Azure AD 在返回令牌时接受用作目标的已注册 redirect_uri 值列表。 每个 URI 值都应当包含一个关联的应用类型值。 支持的类型值为：`Web`、`InstalledClient`。 | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | 数组类型 | `requiredResourceAccess` 可以使用动态许可来驱动管理员许可体验，并驱动使用静态许可的用户的用户许可体验。 但是，它无法驱动常规性的用户许可体验。<br>`resourceAppId` 是应用需要访问的资源的唯一标识符。 此值应等于目标资源应用中声明的 appId。<br>`resourceAccess` 是一个数组，列出应用在指定的资源中所需的 OAuth2.0 权限范围和应用角色。 包含指定的资源的 `id` 和 `type` 值。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | 字符串 | 应用的 SAML 元数据 URL。 | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | 字符串 | 指定应用主页的 URL。 | `https://MyRegisteredApp` |
| `signInAudience` | 字符串 | 指定当前应用程序支持哪些 Microsoft 帐户。 支持的值是：<ul><li>**AzureADMyOrg** - 在我的组织的 Azure AD 租户（即，单租户）中具有 Microsoft 工作或学校帐户的用户</li><li>**AzureADMultipleOrgs** - 在任何组织的 Azure AD 租户（即，多租户）中具有 Microsoft 工作或学校帐户的用户</li> <li>**AzureADandPersonalMicrosoftAccount** - 在任何组织的 Azure AD 租户中具有个人 Microsoft 帐户、工作或学校帐户的用户</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | 字符串数组 | 可用来对应用程序进行分类和标识的自定义字符串。 | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="next-steps"></a>后续步骤

* 有关应用的应用程序对象与服务主体对象之间关系的详细信息，请参阅 [Azure AD 中的应用程序对象和服务主体对象](app-objects-and-service-principals.md)。
* 请参阅 [Azure AD developer glossary](developer-glossary.md)（Azure AD 开发人员术语表），了解某些核心的 Azure Active Directory (AD) 开发人员概念的定义。

请使用以下评论部分提供反馈，帮助我们改进和组织内容。

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
