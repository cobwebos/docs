---
title: "请求访问令牌 - Azure AD B2C | Microsoft 文档"
description: "本文介绍如何设置客户端应用程序和获取访问令牌。"
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: parakhj
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 7202be4e0e9b8b28b5ec1443d6d248c1738da6fb
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Azure AD B2C：请求访问令牌

访问令牌（在来自 Azure AD B2C 的响应中表示为 access\_）是某种形式的安全令牌，客户端可以使用它来访问受[授权服务器](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics)保护的资源，如 Web API。 访问令牌表示为 [JWT](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens)，包含有关目标资源服务器以及向服务器授予的权限的信息。 调用资源服务器时，必须在 HTTP 请求中提供访问令牌。

本文讨论怎样配置客户端应用程序和 Web API 才能获取 access\_token。

> [!NOTE]
> **Web API 链（代理）不受 Azure AD B2C 支持。**
>
> 许多体系结构包含需要调用另一个下游 Web API 的 Web API，这两者都受 Azure AD B2C 的保护。 此方案常见于具有 Web API 后端的本机客户端，该后端将调用 Azure AD 图形 API 等 Microsoft Online 服务。
>
> 可以使用 OAuth 2.0 JWT 持有者凭据授权（也称为代理流）来支持这种链接的 Web API 方案。 但是，Azure AD B2C 中目前尚未实现代理流。

## <a name="register-a-web-api-and-publish-permissions"></a>注册 Web API 并发布权限

在请求访问令牌之前，首先需要注册 Web API，并发布可授予客户端应用程序的权限（作用域）。

### <a name="register-a-web-api"></a>注册 Web API

1. 在 Azure 门户的 Azure AD B2C 功能菜单上，单击“应用程序”。
1. 单击菜单顶部的“+添加”。
1. 输入应用程序的“ **名称** ”，用于向使用者描述应用程序。 例如，可以输入“Contoso API”。
1. 将“包括 Web 应用/Web API”开关切换到“是”。
1. 为“回复 URL”输入任意值。 例如，输入 `https://localhost:44316/`。 由于 API 不会直接从 Azure AD B2C 收到令牌，所以该值并不重要。
1. 输入**应用 ID URI**。 这是用于 Web API 的标识符。 例如，在框中输入“notes”。 “应用 ID URI”将为 `https://{tenantName}.onmicrosoft.com/notes`。
1. 单击“ **创建** ”以注册应用程序。
1. 单击刚刚创建的应用程序，并复制稍后会在代码中使用的全局唯一“ **应用程序客户端 ID** ”。

### <a name="publishing-permissions"></a>发布权限

作用域类似于权限，应用调用 API 时必须使用作用域。 作用域的一些示例包括“读取”或“写入”。 假设需要 Web 或本机应用从 API 进行“读取”。 应用将调用 Azure AD B2C 并请求允许访问“读取”作用域的访问令牌。 为了使 Azure AD B2C 发出此类访问令牌，需要授予应用从特定 API 进行“读取”的权限。 若要执行此操作，API 首先需要发布“读取”作用域。

1. 在 Azure AD B2C 的“应用程序”菜单中，打开 Web API 应用程序（“Contoso API”）。
1. 单击“已发布范围”。 这是定义授予其他应用程序的权限（作用域）的位置。
1. 根据需要添加“作用域值”（例如，“读取”）。 默认情况下，将定义“user_impersonation”作用域。 如果愿意，可以忽略此操作。 在“作用域名称”列输入作用域描述。
1. 单击“保存” 。

> [!IMPORTANT]
> “作用域名称”是“作用域值”的说明。 使用作用域时，确保使用“作用域值”。

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>向 Web API 授予本机或 Web 应用权限

一旦配置 API 发布作用域，客户端应用程序将需要通过 Azure 门户授予这些作用域。

1. 在 Azure AD B2C 功能菜单中导航到“应用程序”菜单。
1. 如果没有客户端应用程序，请进行注册（[Web 应用](active-directory-b2c-app-registration.md#register-a-web-app)或[本机客户端](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app)）。 如果按照本指南作为起点，则需要注册客户端应用程序。
1. 单击“API 访问”。
1. 单击“添加”。
1. 选择 Web API 以及授予的（权限）范围。
1. 单击 **“确定”**。

> [!NOTE]
> Azure AD B2C 不要求客户端应用程序用户表示许可。 所有许可由管理员根据上述应用程序之间配置的权限提供。 如果吊销了对某个应用程序的授权，以前能够获取该权限的所有用户不再能够获取。

## <a name="requesting-a-token"></a>请求令牌

在请求访问令牌时，客户端应用程序需要在请求的 scope 参数中指定所需的权限。 例如，若要指定具有 `https://contoso.onmicrosoft.com/notes`“应用 ID URI”的 API“作用域值”读取”，该作用域将是 `https://contoso.onmicrosoft.com/notes/read`。 下面是对 `/authorize` 终结点发出的授权代码请求的示例。

> [!NOTE]
> 目前，不支持将自定义域与访问令牌一起使用。 必须使用请求 URL 中的 tenantName.onmicrosoft.com 域。

```
https://login.microsoftonline.com/<tenantName>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

若要在同一请求中获取多个权限，可在单个 **scope** 参数中添加多个条目并用空格分隔。 例如：

解码的 URL：

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

编码的 URL：

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

为资源请求的范围（权限）数可以超过为客户端应用程序授予的数目。 在这种情况下，如果至少授予了一个权限，则调用会成功。 生成的 **access\_token** 的“scp”声明中只会填充已成功授予的权限。

> [!NOTE] 
> 不支持在同一请求中针对两个不同的 Web 资源请求权限。 此类请求会失败。

### <a name="special-cases"></a>特殊情况

OpenID Connect 标准指定了多个特殊的“scope”值。 以下特殊范围表示“访问用户的配置文件”的权限：

* **openid**请求 ID 令牌
* **offline\_access**：请求刷新令牌（使用[授权代码流](active-directory-b2c-reference-oauth-code.md)）。

如果 `/authorize` 请求中的 `response_type` 参数包含 `token`，那么 `scope` 参数必须包含至少一个将被授予的资源作用域（除 `openid` 和 `offline_access` 以外）。 否则，`/authorize` 请求会终止并失败。

## <a name="the-returned-token"></a>返回的令牌

成功构建的 **access\_token**（通过 `/authorize` 或 `/token` 终结点）包含以下声明：

| Name | 声明 | 说明 |
| --- | --- | --- |
|目标受众 |`aud` |令牌授权访问的单个资源的**应用程序 ID**。 |
|范围 |`scp` |授予资源的权限。 多个授予的权限以空格分隔。 |
|授权方 |`azp` |发起请求的客户端应用程序的**应用程序 ID**。 |

API 收到 **access\_token** 时，必须[验证该令牌](active-directory-b2c-reference-tokens.md)，证明该令牌可信并包含正确的声明。

我们始终乐于接受反馈和建议！ 如果在本主题中遇到任何困难，请使用标记[“azure-ad-b2c”](https://stackoverflow.com/questions/tagged/azure-ad-b2c)将它们发布在 Stack Overflow 上。 对于功能请求，请将其添加到 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)。

## <a name="next-steps"></a>后续步骤

* 使用 [.Net Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi) 生成 Web API
* 使用 [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) 生成 Web API

