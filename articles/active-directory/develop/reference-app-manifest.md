---
title: 了解 Azure Active Directory 应用清单 | Microsoft Docs
description: 详细介绍 Azure Active Directory 应用清单，该清单表示 Azure AD 租户中的应用程序标识配置，并方便实现 OAuth 授权、许可体验和其他功能。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64823dbe2595d7896b9339745f6c8642a32f74e9
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638691"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 应用清单

应用程序清单包含 Microsoft 标识平台中的某个应用程序对象的所有属性的定义。 它还充当用于更新应用程序对象的机制。 有关应用程序实体及其架构的详细信息，请参阅[图形 API 应用程序实体文档](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)。

可以通过 Azure 门户或以编程方式使用[REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)或[PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications)来配置应用的属性。 但是，在某些情况下，需要编辑应用清单来配置应用的属性。 这些方案包括：

* 如果已将应用注册为 Azure AD 多租户和个人 Microsoft 帐户，则不能在 UI 中更改受支持的 Microsoft 帐户。 而是必须使用应用程序清单编辑器来更改支持的帐户类型。
* 如果需要定义你的应用支持的权限和角色，则必须修改应用程序清单。

## <a name="configure-the-app-manifest"></a>配置应用清单

若要配置应用程序清单，请执行以下操作：

1. 转到 [Azure 门户](https://portal.azure.com)。 搜索并选择 " **Azure Active Directory**服务"。
1. 选择“应用注册”。
1. 选择要配置的应用。
1. 在应用的“概览”页中，选择“清单”部分。 此时会打开一个基于 Web 的清单编辑器，可在其中编辑门户中的清单。 （可选）可以选择“下载”以在本地编辑清单，然后使用“上传”将清单重新应用于应用程序。

## <a name="manifest-reference"></a>清单参考

> [!NOTE]
> 如果看不到“说明”后的“示例值”列，请最大化浏览器窗口并滚动/轻扫，直至看到“示例值”列。

| 密钥  | 值类型 | Description  | 示例值 |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | 可为 Null 的 Int32 | 指定资源所需的访问令牌版本。 此参数可更改独立于终结点或用于请求访问令牌的客户端生成的 JWT 的版本和格式。<br/><br/>使用的端点 v1.0 或 v2.0 由客户端选择，仅影响 id_tokens 的版本。 资源需要显式配置 `accesstokenAcceptedVersion` 以指示受支持的访问令牌格式。<br/><br/>`accesstokenAcceptedVersion` 的可能值为 1、2 或为 null。 如果该值为 null，则此参数默认为1，这对应于 v2.0 终结点。 <br/><br/>如果 `AzureADandPersonalMicrosoftAccount``signInAudience`，则值必须 `2`  | `2` |
| `addIns` | 集合 | 定义自定义行为，使用服务可使用该行为在特定上下文中调用应用。 例如，可以呈现文件流的应用程序可以为其 "FileHandler" 功能设置 addIns 属性。 此参数将允许 Office 365 等服务在用户正在处理的文档的上下文中调用该应用程序。 | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Boolean | 指定回退应用程序类型。 默认情况下，Azure AD 基于 replyUrlsWithType 推断应用程序类型。 在某些情况下，Azure AD 无法确定客户端应用程序类型。 例如，一个此类方案是在没有 URL 重定向的情况下出现 HTTP 请求的[ROPC](https://tools.ietf.org/html/rfc6749#section-4.3)流。 在这些情况下，Azure AD 会根据此属性的值解释应用程序类型。 如果此值设置为 true，则回退应用程序类型设置为公共客户端，例如在移动设备上运行的已安装应用。 默认值为 false，这意味着，回退应用程序类型为机密，例如 Web 应用。 | `false` |
| `availableToOtherTenants` | Boolean | 如果与其他租户共享应用程序，则为 true;否则为 false。 <br><br> _注意：这仅适用于应用注册（旧）体验。替换为[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验中的 `signInAudience`。_ | |
| `appId` | String | 指定由 Azure AD 分配给应用的应用唯一标识符。 | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | 集合 | 指定应用可以声明的角色集合。 可将这些角色分配给用户、组或服务主体。 有关更多示例和信息，请参阅[在应用程序中添加应用角色并在令牌中接收它们](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | String | 应用的显示名称。 <br><br> _注意：这仅适用于应用注册（旧）体验。替换为[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验中的 `name`。_ | `"MyRegisteredApp"` |
| `errorUrl` | String | 不支持。 | |
| `groupMembershipClaims` | String | 配置应用所需的用户访问令牌或 OAuth 2.0 访问令牌中颁发的 `groups` 声明。 要设置此属性，请使用以下有效字符串值之一：<br/><br/>- `"None"`<br/>- `"SecurityGroup"`（适用于安全组和 Azure AD 角色）<br/>- `"All"`（该操作可获取登录用户所属的所有安全组、通讯组和 Azure AD 目录角色。 | `"SecurityGroup"` |
| `homepage` | String | 应用程序主页的 URL。 <br><br> _注意：这仅适用于应用注册（旧）体验。替换为[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验中的 `signInUrl`。_ | `"https://MyRegisteredApp"` |
| `objectId` | String | 应用在目录中的唯一标识符。 <br><br> _注意：这仅适用于应用注册（旧）体验。替换为[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验中的 `id`。_ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | String | 此特定应用的安全令牌服务在令牌中返回的可选声明。<br>目前，同时支持个人帐户和 Azure AD（已通过应用注册门户注册）的应用无法使用可选声明。 但是，使用 v2.0 终结点仅为 Azure AD 注册的应用可以获取它们在清单中请求的可选声明。 有关详细信息，请参阅[可选声明](active-directory-optional-claims.md)。 | `null` |
| `id` | String | 应用在目录中的唯一标识符。 此 ID 不是用于在任何协议事务中标识应用的标识符。 用于引用目录查询中的对象。 | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | 用户定义的 URI，用于在 Web 应用的 Azure AD 租户中唯一标识该应用程序；如果应用是多租户的，则用于在已验证的自定义域中唯一标识该应用。 | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | String | 指定应用服务条款和隐私声明的链接。 服务条款和隐私声明通过用户同意体验展示给用户。 有关详细信息，请参阅[如何：为已注册的 Azure AD 应用添加服务条款和隐私声明](howto-add-terms-of-service-privacy-statement.md)。 | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | 集合 | 包含对应用所分配的凭据、基于字符串的共享机密和 X.509 证书的引用。 ）。 | <code>[<br>&nbsp;{<br>&nbsp;&These credentials are used when requesting access tokens (when the app is acting as a client rather that as resourcenbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | 如果解决方案包含两个部分：客户端应用和自定义 Web API 应用，则该值用于捆绑许可。 如果在此值中输入客户端应用的 appID，则用户只需许可该客户端应用一次。 Azure AD 将知道，同意客户端意味着隐式同意 web API。 它会同时为客户端和 web API 自动预配服务主体。 客户端和 Web API 应用都必须在同一个租户中注册。 | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | String | 只读值，指向已在门户中上传的徽标的 CDN URL。 | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | String | 用于注销应用的 URL。 | `"https://MyRegisteredAppLogout"` |
| `name` | String | 应用的显示名称。 | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Boolean | 指定此 Web 应用是否可以请求 OAuth2.0 隐式流访问令牌。 默认值为 false。 此标志用于基于浏览器的应用，例如 Javascript 单页应用。 若要了解详细信息，请在目录中输入 `OAuth 2.0 implicit grant flow`，并查看有关隐式流的主题。 | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Boolean | 指定此 Web 应用是否可以请求 OAuth2.0 隐式流 ID 令牌。 默认值为 false。 此标志用于基于浏览器的应用，例如 Javascript 单页应用。 | `false` |
| `oauth2Permissions` | 集合 | 指定 Web API（资源）应用向客户端应用公开的 OAuth 2.0 权限范围集合。 在许可期间，可将这些权限范围授予客户端应用。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Boolean | 指定在 OAuth 2.0 令牌请求过程中，Azure AD 是否允许与 GET 请求相反的 POST 请求。 默认值为 false，即指定只允许 GET 请求。 | `false` |
| `parentalControlSettings` | String | `countriesBlockedForMinors` 指定禁止未成年人使用该应用的国家/地区。<br>`legalAgeGroupRule` 指定适用于应用用户的法定年龄组规则。 可设置为 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids` 或 `BlockMinors`。  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | 集合 | 请参阅 `keyCredentials` 属性的说明。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | 集合 | 列出了应用程序和针对隐式同意的所请求权限。 要求管理员已针对应用程序表示同意。 preAuthorizedApplications 不要求用户同意所请求的权限。 preAuthorizedApplications 中列出的权限不要求用户同意。 但是，preAuthorizedApplications 中未列出的任何其他所请求权限都要求用户同意。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Boolean | 指定此应用程序是否为公共客户端（例如，在移动设备上运行的已安装应用程序）。 <br><br> _注意：此属性仅在应用注册（旧）体验中可用。替换为[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验中的 `allowPublicClient`。_ | |
| `publisherDomain` | String | 已验证的应用程序的发布者域。 只读。 | https://www.contoso.com |
| `replyUrls` | 字符串数组 | 此多值属性保存 Azure AD 在返回令牌时接受用作目标的已注册 redirect_uri 值列表。 <br><br> _注意：此属性仅在应用注册（旧）体验中可用。替换为[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验中的 `replyUrlsWithType`。_ | |
| `replyUrlsWithType` | 集合 | 此多值属性保存 Azure AD 在返回令牌时接受用作目标的已注册 redirect_uri 值列表。 每个 URI 值都应包含关联的应用类型值。 支持的类型值为： <ul><li>`Web`</li><li>`InstalledClient`</li></ul><br> 了解有关[replyUrl 限制和限制](https://docs.microsoft.com/azure/active-directory/develop/reply-url)的详细信息。 | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | 集合 | `requiredResourceAccess` 可以使用动态许可来驱动管理员许可体验，并驱动使用静态许可的用户的用户许可体验。 但是，此参数不会驱动一般情况下的用户许可体验。<br>`resourceAppId` 是应用需要访问的资源的唯一标识符。 此值应等于目标资源应用中声明的 appId。<br>`resourceAccess` 是一个数组，列出应用在指定的资源中所需的 OAuth2.0 权限范围和应用角色。 包含指定的资源的 `id` 和 `type` 值。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | String | 应用的 SAML 元数据 URL。 | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | String | 指定应用主页的 URL。 | `https://MyRegisteredApp` |
| `signInAudience` | String | 指定当前应用程序支持哪些 Microsoft 帐户。 支持的值为：<ul><li>**AzureADMyOrg** -在我的组织的 Azure AD 租户中使用 Microsoft 工作或学校帐户的用户（例如，单租户）</li><li>**AzureADMultipleOrgs** -在任何组织的 Azure AD 租户中使用 Microsoft 工作或学校帐户的用户（例如，多租户）</li> <li>**AzureADandPersonalMicrosoftAccount** - 在任何组织的 Azure AD 租户中具有个人 Microsoft 帐户、工作或学校帐户的用户</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | 可用来对应用程序进行分类和标识的自定义字符串。 | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>常见问题

### <a name="manifest-limits"></a>清单限制

应用程序清单具有多个称为集合的属性;例如，approles、keycredentials、knownClientApplications、identifierUris、rediretUris、requiredResourceAccess 和 oauth2Permissions。 在任何应用程序的完整应用程序清单中，所有集合中的条目总数已限制在1200。 如果你之前在应用程序清单中指定了100重定向 Uri，则仅保留1100个剩余项，以在构成清单的所有其他集合中使用。

> [!NOTE]
> 如果尝试在应用程序清单中添加超过1200个条目，可能会看到错误 **"更新应用程序 Xxxxxx 失败。错误详细信息：清单的大小已超出其限制。请减少值的数目，然后重试请求。 "**

### <a name="unsupported-attributes"></a>不支持的属性

应用程序清单表示 Azure AD 中基础应用程序模型的架构。 随着基础架构的发展，将更新清单编辑器，以反映新架构的时间。 因此，你可能会注意到应用程序清单中显示了新的属性。 在极少数情况下，你可能会注意到现有属性中的语法或语义发生了更改，或者可能会发现以前存在的属性不再受支持。 例如，您将在[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)中看到新属性，这些属性在应用注册（旧）体验中具有不同的名称。


| 应用注册（旧版）| 应用注册           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

有关这些属性的说明，请参阅[清单参考](#manifest-reference)部分。

尝试上传先前下载的清单时，可能会出现以下错误之一。 此错误很可能是因为清单编辑器现在支持较新版本的架构，该架构与你尝试上传的架构不匹配。

- "**无法更新 xxxxxx 应用程序。错误详细信息：对象标识符 "undefined" 无效。[].** "
- "**无法更新 xxxxxx 应用程序。错误详细信息：指定的一个或多个属性值无效。[].** "
- "**无法更新 xxxxxx 应用程序。错误详细信息：不允许在此 api 版本中设置 availableToOtherTenants 进行更新。[].** "
- "**无法更新 xxxxxx 应用程序。错误详细信息：不允许对此应用程序进行 "replyUrls" 属性更新。请改用 "replyUrlsWithType" 属性。[].** "
- "**无法更新 xxxxxx 应用程序。错误详细信息：找不到类型名称的值，并且没有预期类型可用。指定模型时，负载中的每个值都必须具有可在有效负载中指定的类型，该类型可由调用方显式指定或从父值隐式推断。[]** "

看到这些错误之一时，建议执行以下操作：

1. 在清单编辑器中单独编辑属性，而不是上传以前下载的清单。 使用[清单引用](#manifest-reference)表来了解旧特性和新属性的语法和语义，以便您可以成功编辑您感兴趣的属性。 
1. 如果你的工作流要求你将清单保存在源存储库中供以后使用，则建议变基存储库中保存的清单与**应用注册**体验中看到的一样。

## <a name="next-steps"></a>后续步骤

* 有关应用程序的应用程序对象与服务主体对象之间的关系的详细信息，请参阅[Azure AD 中的应用程序和服务主体对象](app-objects-and-service-principals.md)。
* 请参阅[microsoft 标识平台开发人员术语表](developer-glossary.md)，了解一些核心 Microsoft 标识平台开发人员概念的定义。

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
