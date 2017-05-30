---
title: Azure AD B2C | Microsoft Docs
description: "可以在 Azure Active Directory B2C 中构建的应用程序类型。"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: bb9d4abe-0db7-4bd9-b0c4-2f43b2c9cf33
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: dastrock
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 762af7e09342f1bb51352e6c3d104bd4d8944e65
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017


---
# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active Directory B2C：应用程序的类型
Azure Active Directory (Azure AD) B2C 支持各种新式应用体系结构的身份验证。 所有这些体系结构都以行业标准协议 [OAuth 2.0](active-directory-b2c-reference-protocols.md) 或 [OpenID Connect](active-directory-b2c-reference-protocols.md) 为基础。 本文档简要介绍可以构建的应用类型（无论使用哪种语言或平台）。 在 [开始构建应用程序](active-directory-b2c-overview.md#get-started)之前，它还可以帮助了解一些高级方案。

## <a name="the-basics"></a>基础知识
使用 Azure AD B2C 的每个应用必须通过 [Azure 门户](https://portal.azure.com/)在 [B2C 目录](active-directory-b2c-get-started.md)中注册。 应用注册过程将收集一些值并将其分配给应用：

* 用于唯一标识应用的 **应用程序 ID** 。
* 用于将响应定向回到应用的 **重定向 URI** 。
* 特定于方案的其他任何值。 请了解如何 [注册应用](active-directory-b2c-app-registration.md)获取详细信息。

应用在注册后，将通过向 Azure AD v2.0 终结点发送请求来与 Azure AD 通信：

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

发送到 Azure AD B2C 的每个请求都指定了一个 **策略**。 策略控制 Azure AD 的行为。 也可以使用这些终结点来创建一系列高度可自定义的用户体验。 常见的策略包括注册、登录和配置文件编辑策略。 如果不熟悉策略，请先了解 Azure AD B2C 的 [可扩展策略框架](active-directory-b2c-reference-policies.md) ，然后再继续下一步。

每个应用与 v2.0 终结点之间的交互遵循类似的高级模式：

1. 应用将用户定向到 v2.0 终结点以执行 [策略](active-directory-b2c-reference-policies.md)。
2. 用户根据策略定义完成策略。
3. 应用从 v2.0 终结点接收某种安全令牌。
4. 应用使用该安全令牌访问受保护的信息或受保护的资源。
5. 资源服务器验证安全令牌，确认是否可以授予访问权限。
6. 应用定期刷新安全令牌。

<!-- TODO: Need a page for libraries to link to -->
根据要构建的应用程序类型，这些步骤可能稍有不同。 开源库可以处理细节。

## <a name="web-apps"></a>Web 应用
对于托管在服务器中通过浏览器访问的 Web 应用（包括 .NET、PHP、Java、Ruby、Python、Node.js），Azure AD B2C 支持使用 [OpenID Connect](active-directory-b2c-reference-protocols.md) 实现所有用户体验。 这包括登录、注册和配置文件管理。 在 Azure AD B2C 的 OpenID Connect 实现中，Web 应用向 Azure AD 发出身份验证请求，发起这些用户体验。 请求的结果是 `id_token`。 此安全令牌代表用户的标识。 它还以声明形式提供用户的相关信息：

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

请参阅 [B2C token reference](active-directory-b2c-reference-tokens.md)（B2C 令牌参考），详细了解应用可用的令牌和声明类型。

在 Web 应用中，每次执行 [策略](active-directory-b2c-reference-policies.md) 都要执行以下高级步骤：

![Web 应用泳道图像](./media/active-directory-b2c-apps/webapp.png)

使用从 Azure AD 收到的公共签名密钥来验证 `id_token` ，就足以验证用户的标识。 这也会设置可在后续页面请求中用于识别用户的会话 Cookie。

若要查看此方案的工作方式，请尝试运行 [入门部分](active-directory-b2c-overview.md#get-started)中提供的 Web 应用登录代码示例之一。

除了简化登录，Web 服务器应用可能还需要访问后端 Web 服务。 在此情况下，Web 应用可以执行稍有不同的 [OpenID Connect 流](active-directory-b2c-reference-oidc.md) ，使用授权代码和刷新令牌来获取令牌。 以下 [Web API 部分](#web-apis)描述了此方案。

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>Web API
可以使用 Azure AD B2C 保护 Web 服务，例如应用的 RESTful Web API。 Web API 可以使用 OAuth 2.0 保护其数据，使用令牌对传入的 HTTP 请求进行身份验证。 Web API 的调用方在 HTTP 请求的授权标头中附加一个令牌：

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

然后，Web API 使用该令牌验证 API 调用方的标识，从令牌中编码的声明提取有关调用方的信息。 请参阅 [Azure AD B2C token reference](active-directory-b2c-reference-tokens.md)（Azure AD B2C 令牌参考），详细了解应用可用的令牌和声明类型。

> [!NOTE]
> Azure AD B2C 目前仅支持由已知客户端访问的 Web API。 例如，完整的应用可能包括 iOS 应用、Android 应用和后端 Web API。 这种体系结构完全受支持。 目前不支持第三方客户端（例如另一个 iOS 应用）访问同一个 Web API。 完整应用的所有组件必须共享一个应用程序 ID。
>
>

Web API 可以从许多类型的客户端（包括 Web 应用、桌面和移动应用、单页应用、服务器端守护程序甚至其他 Web API）接收令牌。 下面是 Web 应用调用 Web API 的完整流程示例：

![Web 应用 Web API 泳道图像](./media/active-directory-b2c-apps/webapi.png)

有关授权代码、刷新令牌的详细信息和获取令牌的步骤，请参阅 [OAuth 2.0 protocol](active-directory-b2c-reference-oauth-code.md)（OAuth 2.0 协议）。

若要了解如何使用 Azure AD B2C 保护 Web API，请查看 [入门部分](active-directory-b2c-overview.md#get-started)中的 Web API 教程。

## <a name="mobile-and-native-apps"></a>移动和本机应用
安装在设备中的应用（例如移动和桌面应用）通常需要代表用户访问后端服务或 Web API。 可以将自定义的标识管理体验添加到本机应用，使用 Azure AD B2C 和 [OAuth 2.0 授权代码流](active-directory-b2c-reference-oauth-code.md)安全调用后端服务。  

在此流程中，应用执行[策略](active-directory-b2c-reference-policies.md)，在用户完成策略之后，从 Azure AD 接收 `authorization_code`。 `authorization_code` 表示应用有权代表当前登录用户调用后端服务。 然后，应用可以在后台使用 `authorization_code` 来兑换 `id_token` 和 `refresh_token`。  应用可以在 HTTP 请求中使用 `id_token` 向后端 Web API 进行身份验证。 它还可以使用 `refresh_token` 获取新的 `id_token`（如果旧令牌已过期）。

> [!NOTE]
> Azure AD B2C 目前仅支持用于访问应用自身后端 Web 服务的令牌。 例如，完整的应用可能包括 iOS 应用、Android 应用和后端 Web API。 这种体系结构完全受支持。 目前不支持 iOS 应用使用 OAuth 2.0 访问令牌来访问合作伙伴 Web API。 完整应用的所有组件必须共享一个应用程序 ID。
>
>

![本机应用泳道图像](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>当前限制
Azure AD B2C 目前不支持以下类型的应用，但此项支持正在开发中。 

### <a name="daemonsserver-side-apps"></a>守护程序/服务器端应用
包含长时运行进程或不需要用户操作的应用还需要通过其他方法访问受保护的资源，例如 Web API。 这些应用可以通过 OAuth 2.0 客户端凭据流，使用应用的标识（而不是用户的委派标识）来进行身份验证和获取令牌。

Azure AD B2C 目前不支持此流。 只有在发生交互式用户流之后，这些应用才获取令牌。

### <a name="web-api-chains-on-behalf-of-flow"></a>Web API 链（代理流）
许多体系结构包含需要调用另一个下游 Web API 的 Web API，这两者都受 Azure AD B2C 的保护。 此方案常见于包含 Web API 后端的本机客户端。 然后，此 Web API 将调用 Azure AD 图形 API 等 Microsoft 联机服务。

可以使用 OAuth 2.0 JWT 持有者凭据授权（也称为“代理流”）来支持这种链接的 Web API 方案。  但是，Azure AD B2C 中目前尚未实现代理流。

