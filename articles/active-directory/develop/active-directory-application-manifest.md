---
title: 了解 Azure Active Directory 应用程序清单 | Microsoft 文档
description: 详细介绍 Azure Active Directory 应用程序清单，该清单表示 Azure AD 租户中的应用程序标识配置，并方便实现 OAuth 授权、许可体验和其他功能。
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
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, sureshja
ms.openlocfilehash: 7448a6c37df2c0bbffbebf23d211e3ace8d12edc
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317389"
---
# <a name="azure-active-directory-application-manifest"></a>Azure Active Directory 应用程序清单
与 Azure AD 集成的应用必须注册到 Azure AD 租户。 可以在 [Azure 门户](https://portal.azure.com)中使用应用清单配置此应用（在“Azure AD”边栏选项卡下面）。

## <a name="manifest-reference"></a>清单参考

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|密钥  |值类型 |示例值  |说明  |
|---------|---------|---------|---------|
|appID     |  标识符字符串       |""|  由 Azure AD 分配给应用的应用程序唯一标识符。|
|appRoles     |    数组类型     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|应用程序可以声明的角色集合。 可将这些角色分配给用户、组或服务主体。|
|availableToOtherTenants|布尔值|`true`|如果此值设置为 true，则应用程序可供其他租户使用。 如果设置为 false，则应用只可供它注册到的租户使用。 有关详细信息，请参阅：[如何使用多租户应用程序模式将任何 Azure Active Directory (AD) 用户登录](active-directory-devhowto-multi-tenant-overview.md)。 |
|displayName     |字符串         |`MyRegisteredApp`         |应用程序的显示名称。 |
|errorURL     |字符串         |`http://MyRegisteredAppError`         |应用程序中出现的错误的 URL。 |
|groupMembershipClaims     |    字符串     |    `1`     |   一个位掩码，用于配置应用程序所需的用户或 OAuth 2.0 访问令牌中颁发的“groups”声明。 位掩码值包括：0：无；1：安全组和 Azure AD 角色；2：保留；4：保留。 将位掩码设置为 7 可获取登录用户所属的所有安全组、通讯组和 Azure AD 目录角色。 |
|optionalClaims     |  字符串       |     `null`    |    此特定应用的安全令牌服务在令牌中返回的[可选声明](active-directory-optional-claims.md)。 |
|acceptMappedClaims    |      布尔值   | `true`        |    如果此值设置为 true，则允许应用程序使用声明映射，而无需指定自定义的签名密钥。|
|homepage     |  字符串       |`http://MyRegistererdApp`         |    应用程序主页的 URL。 |
|identifierUris     |  字符串数组       | `http://MyRegistererdApp`        |   用户定义的 URI，用于在 Web 应用程序的 Azure AD 租户中唯一标识该应用程序；如果应用程序是多租户的，则用于在已验证的自定义域中唯一标识该应用程序。 |
|keyCredentials     |   数组类型      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   此属性包含对应用程序分配的凭据、基于字符串的共享机密和 X.509 证书的引用。 在请求访问令牌时，可使用这些凭据（如果应用充当客户端而不是资源）。 |
|knownClientApplications     |     数组类型    |    [guid]     |     如果解决方案包含两个部分：客户端应用程序和自定义 Web API 应用程序，则该值用于捆绑许可。 如果在此值中输入客户端应用程序的 appID，则用户只需许可该客户端应用程序一次。 Azure AD 将知道许可客户端意味着隐式许可 Web API，并自动为客户端和 Web API 同时预配服务主体。 客户端和 Web API 应用程序都必须在同一个租户中注册。|
|logoutUrl     |   字符串      |     `http://MyRegisteredAppLogout`    |   应用程序的注销 URL。 |
|oauth2AllowImplicitFlow     |   布尔值      |  `false`       |       指定此 Web 应用程序是否可以请求 OAuth2.0 隐式流令牌。 默认值为 false。 此标志用于基于浏览器的应用，例如 Javascript 单页应用。 |
|oauth2AllowUrlPathMatching     |   布尔值      |  `false`       |   指定在 OAuth 2.0 令牌请求过程中，Azure AD 是否允许在重定向 URI 与应用程序的 replyUrls 之间执行路径匹配。 默认值为 false。 |
|oauth2Permissions     | 数组类型         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Web API（资源）应用程序向客户端应用程序公开的 OAuth 2.0 权限范围集合。 在许可期间，可将这些权限范围授予客户端应用程序。 |
|oauth2RequiredPostResponse     | 布尔值        |    `false`     |      指定在 OAuth 2.0 令牌请求过程中，Azure AD 是否允许与 GET 请求相反的 POST 请求。 默认值为 false，即指定只允许 GET 请求。 
|objectId     | 标识符字符串        |     ""    |    应用程序在目录中的唯一标识符。 此 ID 不是用于在任何协议事务中标识应用的标识符。 它是引用目录查询中对象的用户。|
|passwordCredentials     | 数组类型        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    请参阅 keyCredentials 属性的说明。 |
|publicClient     |  布尔值       |      `false`   | 指定应用程序是否是公共客户端（例如在移动设备上运行的已安装应用程序）。 默认值为 false。 |
|supportsConvergence     |  布尔值       |   `false`      | 不应编辑此属性。 请接受默认值。 |
|replyUrls     |  字符串数组       |   `http://localhost`     |  此多值属性保存 Azure AD 在返回令牌时接受用作目标的已注册 redirect_uri 值列表。 |
|requiredResourceAccess     |     数组类型    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}] </code>    |   指定此应用程序需要访问的资源，以及其中每个资源所需的 OAuth 权限范围和应用程序角色集。 这项预先配置的所需资源访问权限有利于提升许可体验。|
|resourceAppId     |    标识符字符串     |  ""      |   应用程序需要访问的资源的唯一标识符。 此值应等于目标资源应用程序中声明的 appId。 |
|resourceAccess     |  数组类型       | 请参阅 requiredResourceAccess 属性的示例值。 |   应用程序需要从指定资源获取的 OAuth2.0 权限范围和应用角色列表（包含指定资源的 ID 和类型值）        |
|samlMetadataUrl    |字符串| `http://MyRegisteredAppSAMLMetadata` |应用程序的 SAML 元数据 URL。| 

## <a name="next-steps"></a>后续步骤
* 有关应用程序的应用程序对象与服务主体对象之间关系的详细信息，请参阅 [Azure AD 中的应用程序对象和服务主体对象][AAD-APP-OBJECTS]。
* 请参阅 [Azure AD developer glossary][AAD-DEVELOPER-GLOSSARY]（Azure AD 开发人员术语表），了解某些核心的 Azure Active Directory (AD) 开发人员概念的定义。

请使用以下评论部分提供反馈，帮助我们改进和组织内容。

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

