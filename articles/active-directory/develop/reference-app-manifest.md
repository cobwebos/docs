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
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127014"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 应用清单

与 Azure Active Directory (Azure AD) 集成的应用必须注册到 Azure AD 租户。 可在 [Azure 门户](https://portal.azure.com)中配置此应用，依次选择“Azure Active Directory”、要配置的应用、“清单”即可。

## <a name="manifest-reference"></a>清单参考

> [!NOTE]
> 如果无法查看说明，请将浏览器窗口最大化，或者滚动/轻扫页面来查看说明。

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| 密钥  | 值类型 | 示例值 | Description  |
|---------|---------|---------|---------|
| `appID` | 标识符字符串 | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | 指定由 Azure AD 分配给应用的应用唯一标识符。 |
| `appRoles` | 数组类型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | 指定应用可以声明的角色集合。 可将这些角色分配给用户、组或服务主体。 |
| `availableToOtherTenants`| 布尔值 | `true` | 如果此值设置为 true，则应用可供其他租户使用。 如果设置为 false，则应用只可供它注册到的租户使用。 有关详细信息，请参阅[如何：使用多租户应用模式将任何 Azure AD 用户登录](howto-convert-app-to-be-multi-tenant.md)。 |
| `displayName` | 字符串 | `MyRegisteredApp` | 应用的显示名称。 |
| `errorURL` | 字符串 | `http://MyRegisteredAppError` | 应用中出现的错误的 URL。 |
| `groupMembershipClaims` | 字符串 | `1` | 一个位掩码，用于配置应用所需的用户访问令牌或 OAuth 2.0 访问令牌中颁发的 `groups` 声明。 该位掩码的值为：<br>0：无<br>1：安全组和 Azure AD 角色<br>2：保留值<br>4：保留值<br>将位掩码设置为 7 可获取登录用户所属的所有安全组、通讯组和 Azure AD 目录角色。 |
| `optionalClaims` | 字符串 | `null` | 此特定应用的安全令牌服务在令牌中返回的可选声明。 有关详细信息，请参阅[可选声明](active-directory-optional-claims.md)。 |
| `acceptMappedClaims` | 布尔值 | `true` | 如果此值设置为 `true`，则允许应用使用声明映射，而无需指定自定义的签名密钥。 |
| `homepage` | 字符串 | `http://MyRegisteredApp` | 指定应用主页的 URL。 |
| `informationalUrls` | 字符串 | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | 指定应用服务条款和隐私声明的链接。 服务条款和隐私声明通过用户同意体验展示给用户。 有关详细信息，请参阅[如何：为已注册的 Azure AD 应用添加服务条款和隐私声明](howto-add-terms-of-service-privacy-statement.md)。 |
| `identifierUris` | 字符串数组 | `http://MyRegistererdApp` | 用户定义的 URI，用于在 Web 应用的 Azure AD 租户中唯一标识该应用程序；如果应用是多租户的，则用于在已验证的自定义域中唯一标识该应用。 |
| `keyCredentials` | 数组类型 | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | 包含对应用所分配的凭据、基于字符串的共享机密和 X.509 证书的引用。 在请求访问令牌时，可使用这些凭据（如果应用充当客户端而不是资源）。 |
| `knownClientApplications` | 数组类型 | `[GUID]` | 如果解决方案包含两个部分：客户端应用和自定义 Web API 应用，则该值用于捆绑许可。 如果在此值中输入客户端应用的 appID，则用户只需许可该客户端应用一次。 Azure AD 将知道许可客户端意味着隐式许可 Web API，并自动为客户端和 Web API 同时预配服务主体。 客户端和 Web API 应用都必须在同一个租户中注册。 |
| `logoutUrl` | 字符串 | `http://MyRegisteredAppLogout` | 用于注销应用的 URL。 |
| `oauth2AllowImplicitFlow` | 布尔值 | `false` | 指定此 Web 应用是否可以请求 OAuth2.0 隐式流令牌。 默认值为 false。 此标志用于基于浏览器的应用，例如 Javascript 单页应用。 |
| `oauth2AllowUrlPathMatching` | 布尔值 | `false` | 指定在 OAuth 2.0 令牌请求过程中，Azure AD 是否允许在重定向 URI 与应用的 replyUrls 之间执行路径匹配。 默认值为 false。 |
| `oauth2Permissions` | 数组类型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | 指定 Web API（资源）应用向客户端应用公开的 OAuth 2.0 权限范围集合。 在许可期间，可将这些权限范围授予客户端应用。 |
| `oauth2RequiredPostResponse` | 布尔值 | `false` | 指定在 OAuth 2.0 令牌请求过程中，Azure AD 是否允许与 GET 请求相反的 POST 请求。 默认值为 false，即指定只允许 GET 请求。 |
| `objectId` | 标识符字符串 | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | 应用在目录中的唯一标识符。 此 ID 不是用于在任何协议事务中标识应用的标识符。 用于引用目录查询中的对象。 |
| `parentalControlSettings` | 字符串 | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors` 指定禁止未成年人使用该应用的国家/地区。<br>`legalAgeGroupRule` 指定适用于应用用户的法定年龄组规则。 可设置为 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids` 或 `BlockMinors`。  |
| `passwordCredentials` | 数组类型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | 请参阅 `keyCredentials` 属性的说明。 |
| `publicClient` | 布尔值 | `false` | 指定应用是否是公共客户端（例如在移动设备上运行的已安装应用）。 默认值为 false。 |
| `replyUrls` | 字符串数组 | `"http://localhost"` | 此多值属性保存 Azure AD 在返回令牌时接受用作目标的已注册 redirect_uri 值列表。 |
| `requiredResourceAccess` | 数组类型 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | `requiredResourceAccess` 可以使用动态许可来驱动管理员许可体验，并驱动使用静态许可的用户的用户许可体验。 但是，它无法驱动常规性的用户许可体验。<br>`resourceAppId` 是应用需要访问的资源的唯一标识符。 此值应等于目标资源应用中声明的 appId。<br>`resourceAccess` 是一个数组，列出应用在指定的资源中所需的 OAuth2.0 权限范围和应用角色。 包含指定的资源的 `id` 和 `type` 值。 |
| `samlMetadataUrl` | 字符串 | `http://MyRegisteredAppSAMLMetadata` | 应用的 SAML 元数据 URL。 |

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

