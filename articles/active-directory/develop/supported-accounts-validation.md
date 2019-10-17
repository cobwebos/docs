---
title: 受支持的帐户类型的验证差异（SignInAudience）-Microsoft 标识平台/Azure Active Directory
description: 在向 Microsoft 标识平台注册应用程序时，了解不同支持的帐户类型的各种属性的验证差异。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bea6e3ea93e8b630970a8d86fc246eddaf3f56c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392600"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>按支持的帐户类型的验证差异（signInAudience）

向开发人员注册应用程序时，系统会要求你选择应用程序支持的帐户类型。 在应用程序对象和清单中，此属性 `signInAudience`。

这些方式包括：

- *AzureADMyOrg*：只有注册了应用的组织目录中的帐户（单租户）
- *AzureADMultipleOrgs*：任何组织目录中的帐户（多租户）
- *AzureADandPersonalMicrosoftAccount*：任何组织目录（多租户）和个人 Microsoft 帐户中的帐户（例如 Skype、Xbox 和 Outlook.com）

对于已注册的应用程序，可以在应用程序的 "**身份验证**" 部分找到受支持的帐户类型的值。 你还可以在**清单**中的 `signInAudience` 属性下找到它。

你为此属性选择的值会影响其他应用对象属性。 因此，如果更改此属性，则可能需要先更改其他属性。

请参阅下表，了解不同支持的帐户类型的各种属性的验证差异。

| properties | `AzureADMyOrg` | `AzureADMultipleOrgs`  | `AzureADandPersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| 应用程序 ID URI （`identifierURIs`）  | 在租户中必须是唯一的 <br><br> 支持 urn://方案 <br><br> 不支持通配符 <br><br> 支持查询字符串和片段 <br><br> 最大长度为255个字符 <br><br> IdentifierURIs 数量没有限制 *  | 必须全局唯一 <br><br> 支持 urn://方案 <br><br> 不支持通配符 <br><br> 支持查询字符串和片段 <br><br> 最大长度为255个字符 <br><br> IdentifierURIs 数量没有限制 * | 必须全局唯一 <br><br> 不支持 urn://方案 <br><br> 不支持通配符、段和查询字符串 <br><br> 最大长度为120个字符 <br><br> 最大 50 identifierURIs |
| 证书（`keyCredentials`） | 对称签名密钥 | 对称签名密钥 | 加密和非对称签名密钥 | 
| 客户端密码（`passwordCredentials`） | 无限制 * | 无限制 * | 如果启用 liveSDK：最多2个客户端密码 | 
| 重定向 Uri （`replyURLs`） | 有关详细信息，请参阅[重定向 URI/回复 URL 限制和限制](reply-url.md)。 | | | 
| API 权限（`requiredResourceAccess`） | 无限制 * | 无限制 * | 每个资源最多允许30个权限（例如 Microsoft Graph） | 
| 此 API 定义的作用域（`oauth2Permissions`） | 最大作用域名称长度为120个字符 <br><br> 定义的作用域数量没有限制 * | 最大作用域名称长度为120个字符 <br><br> 定义的作用域数量没有限制 * |  最大作用域名称长度为40个字符 <br><br> 最多定义100个作用域 | 
| 授权客户端应用程序（`preautorizedApplications`） | 无限制 * | 无限制 * | 总最大值为500 <br><br> 最多定义100个客户端应用 <br><br> 每个客户端最多定义30个作用域 | 
| appRoles | 受支持 <br> 无限制 * | 受支持 <br> 无限制 * | 不支持 | 
| 注销 URL | 允许 http://localhost <br><br> 最大长度为255个字符 | 允许 http://localhost <br><br> 最大长度为255个字符 | <br><br> @no__t 为-0，MSA 的 @no__t 失败 <br><br> 最大长度为255个字符 <br><br> 不允许使用 HTTP 方案 <br><br> 不支持通配符 | 

\* 应用对象的所有集合属性的全局限制为大约1000项

## <a name="next-steps"></a>后续步骤

- 了解[应用程序注册](app-objects-and-service-principals.md)
- 了解[应用程序清单](reference-app-manifest.md)
