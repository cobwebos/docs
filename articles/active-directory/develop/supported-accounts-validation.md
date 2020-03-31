---
title: 按受支持的帐户类型进行验证差异 - 微软标识平台 |蔚蓝
description: 了解向 Microsoft 标识平台注册应用时，不同受支持帐户类型的各种属性的验证差异。
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 2a1507b008903085886f9392f3f4e5461997b6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128855"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>按受支持帐户类型列出的验证差异 (signInAudience)

向开发人员的 Microsoft 标识平台注册应用程序时，系统会要求你选择应用程序支持的帐户类型。 在应用程序对象和清单中，此属性是 `signInAudience`。

这些方式包括：

- *AzureADMyOrg*：仅注册应用的组织目录中的帐户（单租户）
- *AzureAD多路组织*：任何组织目录中的帐户（多租户）
- *AzureADand个人 Microsoft 帐户*：任何组织目录（多租户）和个人 Microsoft 帐户（例如，Skype、Xbox 和Outlook.com）中的帐户

对于已注册的应用程序，可以在应用程序的“身份验证”**** 部分找到受支持帐户类型值。 也可以在**清单**中的 `signInAudience` 属性下找到它。

为此属性选择的值已影响其他应用对象属性。 因此，如果更改此属性，则可能需要先更改其他属性。

请参阅下表，了解不同受支持帐户类型的各种属性的验证差异。

| properties | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` 和 `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| 应用程序 ID URI (`identifierURIs`)  | 在租户中必须唯一 <br><br> 支持 urn:// 方案 <br><br> 不支持通配符 <br><br> 支持查询字符串和片段 <br><br> 最大长度为 255 个字符 <br><br> 对 identifierURI 数没有限制*  | 必须全局唯一 <br><br> 支持 urn:// 方案 <br><br> 不支持通配符 <br><br> 支持查询字符串和片段 <br><br> 最大长度为 255 个字符 <br><br> 对 identifierURI 数没有限制* | 必须全局唯一 <br><br> 不支持urn://方案 <br><br> 不支持通配符、片段和查询字符串 <br><br> 最大长度为 120 个字符 <br><br> 最多 50 个标识符 URI |
| 证书 (`keyCredentials`) | 对称签名密钥 | 对称签名密钥 | 加密和非对称签名密钥 | 
| 客户端密码 (`passwordCredentials`) | 无限制* | 无限制* | 如果启用了 liveSDK：最多 2 个客户端机密 | 
| 重定向 URI (`replyURLs`) | 有关详细信息，请参阅[重定向 URI/回复 URL 的局限性和限制](reply-url.md)。 | | | 
| API 权限 (`requiredResourceAccess`) | 无限制* | 无限制* | 每个资源最多允许 30 个权限（例如，微软图形） | 
| 此 API 定义的作用域 (`oauth2Permissions`) | 最大作用域名称长度为 120 个字符 <br><br> 对定义的作用域数没有限制* | 最大作用域名称长度为 120 个字符 <br><br> 对定义的作用域数没有限制* |  最大范围名称长度为 40 个字符 <br><br> 最多定义 100 个作用域 | 
| 授权客户端应用程序 (`preautorizedApplications`) | 无限制* | 无限制* | 总计 500 <br><br> 最多定义 100 个客户端应用 <br><br> 每个客户端最多定义 30 个作用域 | 
| appRoles | 支持 <br> 无限制* | 支持 <br> 无限制* | 不支持 | 
| 注销 URL | 允许 http://localhost <br><br> 最大长度为 255 个字符 | 允许 http://localhost <br><br> 最大长度为 255 个字符 | <br><br> https://localhost允许，MSAhttp://localhost失败 <br><br> 最大长度为 255 个字符 <br><br> 不允许使用 HTTP 方案 <br><br> 不支持通配符 | 

*应用对象的所有集合属性有大约 1000 项的全局限制

## <a name="next-steps"></a>后续步骤

- 了解[应用程序注册](app-objects-and-service-principals.md)
- 了解[应用程序清单](reference-app-manifest.md)
