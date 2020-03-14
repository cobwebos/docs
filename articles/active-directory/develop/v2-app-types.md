---
title: Microsoft 标识平台的应用程序类型 |Microsoft
description: Microsoft 标识平台（v2.0）终结点支持的应用和方案的类型。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 94cddf097f2a9e51f061909f6bdd3dcd82f18bfe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262524"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Microsoft 标识平台的应用程序类型

Microsoft 标识平台 (v2.0) 终结点支持各种现代应用体系结构的身份验证，所有这些体系结构都基于行业标准协议 [OAuth 2.0 或 OpenID Connect](active-directory-v2-protocols.md)。 本文介绍你可以使用 Microsoft 标识平台构建的应用类型，而不考虑首选语言或平台。 此信息旨在帮助你在[开始处理代码](v2-overview.md#getting-started)之前了解高级方案。

> [!NOTE]
> Microsoft 标识平台终结点并不支持所有 Azure Active Directory （Azure AD）方案和功能。 若要确定是否应使用 Microsoft 标识平台终结点，请阅读[microsoft 标识平台限制](active-directory-v2-limitations.md)。

## <a name="the-basics"></a>基础知识

你必须在新的[应用注册门户](https://go.microsoft.com/fwlink/?linkid=2083908)中注册使用 Microsoft 标识平台终结点的每个应用。 应用注册过程将收集这些值并将其分配给应用：

* 唯一标识应用的**应用程序（客户端） ID**
* 用于将响应定向回应用的重定向 URI
* 其他一些特定于方案的值，如支持的帐户类型

有关详细信息，请了解如何[注册应用](quickstart-register-app.md)。

注册应用后，应用会通过向终结点发送请求来与 Microsoft 标识平台通信。 我们提供开源框架和库，用于处理这些请求的详细信息。 还可以通过创建对这些终结点的请求，选择自行实现身份验证逻辑：

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>单页应用 (JavaScript)

许多新式应用都有一个单页应用前端（主要以 JavaScript 编写）。 通常，使用角度、反应或 Vue 等框架编写。 Microsoft 标识平台终结点通过使用[OAuth 2.0 隐式流](v2-oauth2-implicit-grant-flow.md)支持这些应用。

在此流中，应用直接从 Microsoft 标识平台授权终结点接收令牌，而无需任何服务器到服务器的交换。 所有身份验证逻辑和会话处理完全在 JavaScript 客户端中发生，无需进行额外的页面重定向。

![显示隐式身份验证流](./media/v2-app-types/convergence-scenarios-implicit.svg)

若要查看此方案的实际运行情况，请尝试[Microsoft 标识平台入门](v2-overview.md#getting-started)部分中的单页应用代码示例之一。

## <a name="web-apps"></a>Web 应用

对于通过浏览器访问的 Web 应用（.NET、PHP、Java、Ruby、Python、Node 等），可以使用 [OpenID Connect](active-directory-v2-protocols.md) 来执行用户登录。 在 OpenID Connect 中，Web 应用接收 ID 令牌。 ID 令牌是一个安全令牌，用于验证用户的标识并以声明形式提供有关用户的信息：

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

有关 Microsoft 标识平台终结点中使用的不同类型令牌的更多详细信息，请参阅[访问令牌](access-tokens.md)参考和[id_token 参考](id-tokens.md)

在 Web 服务器应用中，登录身份验证流采用以下高级步骤：

![显示 web 应用身份验证流](./media/v2-app-types/convergence-scenarios-webapp.svg)

可以通过使用从 Microsoft 标识平台终结点接收的公共签名密钥验证 ID 令牌来确保用户的身份。 设置会话 Cookie，在后续页面请求中将其用于识别用户。

若要查看此方案的运行情况，请尝试[Microsoft 标识平台入门](v2-overview.md#getting-started)部分中的 web 应用登录代码示例之一。

除了简单登录，Web 服务器应用可能还需要访问其他 Web 服务，例如 REST API。 在这种情况下，Web 服务器应用可以使用 [OAuth 2.0 授权代码流](active-directory-v2-protocols.md)参与合并的 OpenID Connect 和 OAuth 2.0 流。 有关此方案的详细信息，请阅读 [Web 应用和 Web API 入门](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)。

## <a name="web-apis"></a>Web API

可以使用 Microsoft 标识平台终结点来保护 Web 服务，例如应用的 RESTful Web API。 Web Api 可以在多种平台和语言中实现。 它们还可以在 Azure Functions 中使用 HTTP 触发器实现。 Web API 使用 OAuth 2.0 访问令牌（而不是 ID 令牌和会话 Cookie）来保护数据并对传入的请求进行身份验证。 Web API 调用方会在 HTTP 请求的授权标头中附加一个访问令牌，如下所示：

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API 使用此访问令牌来验证 API 调用方的标识，并从访问令牌中编码的声明里提取调用方的相关信息。 有关 Microsoft 标识平台终结点中使用的不同类型令牌的更多详细信息，请参阅[访问令牌](access-tokens.md)参考和[id_token](id-tokens.md)引用。

Web API 可让用户通过公开权限（也称为[范围](v2-permissions-and-consent.md)）来选择添加/排除特定的功能或数据。 为了使调用应用能够获取某个范围的权限，用户必须在执行流的过程中同意该范围。 Microsoft 标识平台终结点要求用户提供权限，然后记录 Web API 接收的所有访问令牌中的权限。 Web API 验证每次调用中接收的访问令牌，并执行授权检查。

Web API 可以从各种应用接收访问令牌，其中包括 Web 服务器应用、桌面和移动应用、单页应用、服务器端守护程序，甚至其他 Web API。 Web API 的高级流如下所示：

![显示 web API 身份验证流](./media/v2-app-types/convergence-scenarios-webapi.svg)

若要了解如何使用 OAuth2 访问令牌保护 Web API，请查看[Microsoft 标识平台入门](v2-overview.md#getting-started)部分中的 web api 代码示例。

在许多情况下，web Api 还需要对由 Microsoft 标识平台保护的其他下游 web Api 发出出站请求。 为此，web Api 可以利用代理流，这使 web API**可以在出**站请求中使用另一个访问令牌来交换传入的访问令牌。 有关详细信息，请参阅[Microsoft 标识平台和 OAuth 2.0 代理流](v2-oauth2-on-behalf-of-flow.md)。

## <a name="mobile-and-native-apps"></a>移动和本机应用

安装在设备中的应用（如移动和桌面应用）通常需要访问用于存储数据和代表用户执行各种功能的后端服务或 Web API。 这些应用可以使用 [OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)将登录凭据和授权添加到后端服务。

在此流中，当用户登录时，应用程序从 Microsoft 标识平台终结点接收授权代码。 授权代码表示应用有权代表登录用户调用后端服务。 应用可以通过在后台交换授权代码获得 OAuth 2.0 访问令牌和刷新令牌。 应用可以使用访问令牌在 HTTP 请求中向 Web API 进行身份验证，并可以在旧的访问令牌过期时，用刷新令牌获取新的访问令牌。

![显示本机应用身份验证流](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>守护程序和服务器端应用

包含长时运行进程或无需用户交互便可操作的应用还需要通过其他方法访问受保护的资源，例如 Web API。 这些应用可以通过 OAuth 2.0 客户端凭据流使用应用的标识（而不是用户的委派标识）来进行身份验证和获取令牌。 可以使用客户端机密或证书证明应用的身份。 有关详细信息，请参阅[使用 Microsoft 标识平台的 .Net Core 守护程序控制台应用程序](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)。

在此流中，应用直接与 `/token` 终结点交互以获取访问权限：

![显示守护程序应用身份验证流](./media/v2-app-types/convergence-scenarios-daemon.svg)

若要生成守护程序，请参阅[客户端凭据文档](v2-oauth2-client-creds-grant-flow.md)，或者尝试 [.NET 示例应用](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)。
