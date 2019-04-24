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
ms.date: 04/13/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b7a383eb8df8fcfd358bce226168e5fbeb42f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298285"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory 应用清单

应用程序清单包含 Microsoft 标识平台中的某个应用程序对象的所有属性的定义。 它还充当用于更新应用程序对象的机制。 应用程序实体和其架构的详细信息，请参阅[图形 API 应用程序实体文档](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)。

你可以配置通过 Azure 门户或以编程方式使用应用程序的属性[REST API](https://docs.microsoft.com/en-us/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)或[PowerShell](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0#applications)。 但是，在某些情况下，需要编辑应用清单来配置应用的属性。 这些方案包括：

* 如果为 Azure AD 多租户和个人 Microsoft 帐户注册应用程序，则无法更改用户界面中受支持的 Microsoft 帐户。 而是必须使用应用程序清单编辑器来更改支持的帐户类型。
* 如果需要定义你的应用支持的权限和角色，则必须修改应用程序清单。

## <a name="configure-the-app-manifest"></a>配置应用清单

若要配置应用程序清单，请执行以下操作：

1. 登录[Azure 门户](https://portal.azure.com)。
1. 选择“Azure Active Directory”服务，然后选择“应用注册”或“应用注册(预览版)”。
1. 选择要配置的应用。
1. 在应用的“概览”页中，选择“清单”部分。 此时会打开一个基于 Web 的清单编辑器，可在其中编辑门户中的清单。 （可选）可以选择“下载”以在本地编辑清单，然后使用“上传”将清单重新应用于应用程序。

## <a name="manifest-reference"></a>清单参考

> [!NOTE]
> 如果看不到“说明”后的“示例值”列，请最大化浏览器窗口并滚动/轻扫，直至看到“示例值”列。

| 密钥  | 值类型 | 描述  | 示例值 |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | 可为 Null 的 Int32 | 指定资源所需的访问令牌版本。 这会更改独立于用于请求访问令牌的终结点或客户端生成的 JWT 的版本和格式。<br/><br/>使用的端点 v1.0 或 v2.0 由客户端选择，仅影响 id_tokens 的版本。 资源需要显式配置 `accesstokenAcceptedVersion` 以指示受支持的访问令牌格式。<br/><br/>`accesstokenAcceptedVersion` 的可能值为 1、2 或为 null。 如果值为 null，则默认为 1，这对应于 v1.0 终结点。 | `2` |
| `addIns` | 集合 | 定义使用服务可用于在特定上下文中调用的应用的自定义行为。 例如，可以呈现文件流的应用程序可能会设置其"FileHandler"功能的外接程序属性。 这将让 Office 365 等服务在用户正在处理的文档的上下文中调用应用程序。 | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Boolean | 指定回退应用程序类型。 默认情况下，Azure AD 基于 replyUrlsWithType 推断应用程序类型。 某些情况下，Azure AD 无法确定客户端应用类型（例如，其中发生了没有 URL 重定向的 HTTP 请求的 [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) 流）。 在这种情况下，Azure AD 将基于此属性的值解释应用程序类型。 如果此值设置为 true，则回退应用程序类型设置为公共客户端，例如在移动设备上运行的已安装应用。 默认值为 false，这意味着，回退应用程序类型为机密，例如 Web 应用。 | `false` |
| `availableToOtherTenants` | Boolean | 如果应用程序共享与其他租户; 则为 true否则为 false。 <br><br> _注意：这将替换为`signInAudience`中[应用注册 （预览版）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)体验。_ | |
| `appId` | String | 指定由 Azure AD 分配给应用的应用唯一标识符。 | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | 集合 | 指定应用可以声明的角色集合。 可将这些角色分配给用户、组或服务主体。 有关更多示例和信息，请参阅[在应用程序中添加应用角色并在令牌中接收它们](howto-add-app-roles-in-azure-ad-apps.md) | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | String | 应用的显示名称。 <br><br> _注意：这将替换为`name`中[应用注册 （预览版）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)体验。_ | `"MyRegisteredApp"` |
| `errorUrl` | String | 不受支持。 | |
| `groupMembershipClaims` | String | 配置应用所需的用户访问令牌或 OAuth 2.0 访问令牌中颁发的 `groups` 声明。 要设置此属性，请使用以下有效字符串值之一：<br/><br/>- `"None"`<br/>- `"SecurityGroup"`（适用于安全组和 Azure AD 角色）<br/>- `"All"`（该操作可获取登录用户所属的所有安全组、通讯组和 Azure AD 目录角色。 | `"SecurityGroup"` |
| `homepage` | String | 应用程序主页的 URL。 <br><br> _注意：这将替换为`signInUrl`中[应用注册 （预览版）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)体验。_ | `"https://MyRegisteredApp"` |
| `objectId` | String | 应用在目录中的唯一标识符。 <br><br> _注意：这将替换为`id`中[应用注册 （预览版）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)体验。_ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | String | 此特定应用的安全令牌服务在令牌中返回的可选声明。<br>目前，同时支持个人帐户和 Azure AD（已通过应用注册门户注册）的应用无法使用可选声明。 但是，使用 v2.0 终结点仅为 Azure AD 注册的应用可以获取它们在清单中请求的可选声明。 有关详细信息，请参阅[可选声明](active-directory-optional-claims.md)。 | `null` |
| `id` | String | 应用在目录中的唯一标识符。 此 ID 不是用于在任何协议事务中标识应用的标识符。 用于引用目录查询中的对象。 | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | 用户定义的 URI，用于在 Web 应用的 Azure AD 租户中唯一标识该应用程序；如果应用是多租户的，则用于在已验证的自定义域中唯一标识该应用。 | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | String | 指定应用服务条款和隐私声明的链接。 服务条款和隐私声明通过用户同意体验展示给用户。 有关详细信息，请参阅[如何：为已注册的 Azure AD 应用添加服务条款和隐私声明](howto-add-terms-of-service-privacy-statement.md)。 | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | 集合 | 包含对应用所分配的凭据、基于字符串的共享机密和 X.509 证书的引用。 在请求访问令牌时，可使用这些凭据（如果应用充当客户端而不是资源）。 | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | 如果解决方案包含两个部分：客户端应用和自定义 Web API 应用，则该值用于捆绑许可。 如果在此值中输入客户端应用的 appID，则用户只需许可该客户端应用一次。 Azure AD 将知道许可客户端意味着隐式许可 Web API，并自动为客户端和 Web API 同时预配服务主体。 客户端和 Web API 应用都必须在同一个租户中注册。 | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
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
| `publicClient` | Boolean | 指定此应用程序是否为公共客户端 （例如移动设备上运行的已安装应用程序）。 <br><br> _注意：这将替换为`allowPublicClient`中[应用注册 （预览版）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)体验。_ | |
| `publisherDomain` | String | 应用程序验证的发行者域。 只读的。 | https://www.contoso.com |
| `replyUrls` | 字符串数组 | 此多值属性保存 Azure AD 在返回令牌时接受用作目标的已注册 redirect_uri 值列表。 <br><br> _注意：这将替换为`replyUrlsWithType`中[应用注册 （预览版）](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/)体验。_ | |
| `replyUrlsWithType` | 集合 | 此多值属性保存 Azure AD 在返回令牌时接受用作目标的已注册 redirect_uri 值列表。 每个 URI 值都应当包含一个关联的应用类型值。 支持的类型值为：`Web`、`InstalledClient`。 | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | 集合 | `requiredResourceAccess` 可以使用动态许可来驱动管理员许可体验，并驱动使用静态许可的用户的用户许可体验。 但是，它无法驱动常规性的用户许可体验。<br>`resourceAppId` 是应用需要访问的资源的唯一标识符。 此值应等于目标资源应用中声明的 appId。<br>`resourceAccess` 是一个数组，列出应用在指定的资源中所需的 OAuth2.0 权限范围和应用角色。 包含指定的资源的 `id` 和 `type` 值。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | String | 应用的 SAML 元数据 URL。 | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | String | 指定应用主页的 URL。 | `https://MyRegisteredApp` |
| `signInAudience` | String | 指定当前应用程序支持哪些 Microsoft 帐户。 支持的值是：<ul><li>**AzureADMyOrg** - 在我的组织的 Azure AD 租户（即，单租户）中具有 Microsoft 工作或学校帐户的用户</li><li>**AzureADMultipleOrgs** - 在任何组织的 Azure AD 租户（即，多租户）中具有 Microsoft 工作或学校帐户的用户</li> <li>**AzureADandPersonalMicrosoftAccount** - 在任何组织的 Azure AD 租户中具有个人 Microsoft 帐户、工作或学校帐户的用户</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | 可用来对应用程序进行分类和标识的自定义字符串。 | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>常见问题

### <a name="manifest-limits"></a>清单限制

应用程序清单具有多个称为集合; 的属性例如，approles、 keycredentials、 knownClientApplications、 identifierUris、 rediretUris、 requiredResourceAccess 和 oauth2Permissions。 在任一应用程序的完整应用程序清单中，所有合并集合中的条目总数不能超过 1200 个。 如果已有 100 重定向 Uri 的应用程序清单中指定，则表明剩下唯一与 1100年剩余中结合使用的所有其他集合中使用的条目组成清单。

> [!NOTE]
> 如果尝试添加应用程序清单中的 1200 多个项，可能会看到错误 **"未能更新应用程序 xxxxxx。错误详细信息：清单大小已超过其限制。请减少的值数量，然后重试请求。"**

### <a name="unsupported-attributes"></a>不支持的特性

应用程序清单在 Azure AD 中表示的基础的应用程序模型的架构。 随着基础架构的发展，将更新清单编辑器以不时反映新的架构。 因此，可能会注意到应用程序清单中显示的新属性。 在极少数情况下，可能会注意到现有属性的语法或语义修改或您可能会发现不再支持以前存在的属性。 例如，你将看到中的新特性[应用注册 （预览版）](https://developer.microsoft.com/graph/blogs/new-app-registration/)的已知包含在应用注册 (GA) 体验中不同的名称。


| 应用注册 (GA）)    | 应用注册 （预览版） |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

有关这些属性的说明，请参阅[清单参考](#manifest-reference)部分。

当您尝试上传以前下载的清单时，可能会看到以下错误之一。 这可能是因为在清单编辑器现在支持架构，这与你正在尝试上传的那个不匹配的较新版本。

- "**更新 xxxxxx 应用程序失败。错误详细信息：无效的对象标识符 undefined。[].**"
- "**更新 xxxxxx 应用程序失败。错误详细信息：指定的一个或多个属性值均无效。[].**"
- "**更新 xxxxxx 应用程序失败。错误详细信息：不允许更新此 api 版本中设置 availableToOtherTenants。[].**"
- "**更新 xxxxxx 应用程序失败。错误详细信息：不允许此应用程序的 replyUrls 属性的更新。请改用 replyUrlsWithType 属性。[].**"
- "**更新 xxxxxx 应用程序失败。错误详细信息：找到没有类型名称的值并且没有预期的类型时可用。当指定模型时，有效负载中的每个值必须具有可在负载中，显式由调用方指定或隐式从父值推断的类型。[]**"

当您看到以下错误之一时，我们的建议如下：

1. 编辑清单编辑器中而不是上传以前下载的清单中单独的属性。 使用[清单参考](#manifest-reference)表来了解语法和语义的旧和新属性，以便可以成功编辑您感兴趣的属性。 
1. 如果您的工作流需要将清单保存在源存储库中供以后使用，我们建议将变基中看到与在存储库中的已保存的清单**应用注册 （预览版）** 体验。

## <a name="next-steps"></a>后续步骤

* 有关应用的应用程序和服务主体对象之间的关系的详细信息，请参阅[应用程序和 Azure AD 中的服务主体对象](app-objects-and-service-principals.md)。
* 请参阅[Microsoft 标识平台开发人员词汇表](developer-glossary.md)了解某些核心 Microsoft 标识平台开发人员概念的定义。

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
