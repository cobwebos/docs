---
title: Microsoft 标识平台的应用程序类型 |Azure
description: 类型的应用和 Microsoft 标识平台 (v2.0) 终结点所支持方案。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2019
ms.author: celested
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b45f30bf057feba38a716e040ab2efc15da90
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785943"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Microsoft 标识平台的应用程序类型

Microsoft 标识平台 (v2.0) 终结点支持各种现代应用程序体系结构中，所有这些基于行业标准协议的身份验证[OAuth 2.0 或 OpenID Connect](active-directory-v2-protocols.md)。 本文介绍可以使用 Microsoft 标识平台，而不考虑你的首选的语言或平台构建的应用程序的类型。 信息旨在帮助您了解在之前的高级方案[开始处理代码](v2-overview.md#getting-started)。

> [!NOTE]
> Microsoft 标识平台终结点不支持所有 Azure Active Directory (Azure AD) 方案和功能。 若要确定是否应使用 Microsoft 标识平台终结点，请阅读[Microsoft 标识平台限制](active-directory-v2-limitations.md)。

## <a name="the-basics"></a>基础知识

必须注册每个应用使用 Microsoft 标识平台终结点中的新[应用程序注册门户](https://go.microsoft.com/fwlink/?linkid=2083908)。 应用注册过程将收集这些值并将其分配给应用：

* **应用程序 （客户端） ID**用于唯一标识您的应用程序
* 用于将响应定向回应用的重定向 URI
* 几个其他特定于方案的值，如支持帐户类型

有关详细信息，请了解如何[注册应用](quickstart-register-app.md)。

注册应用后，应用将通过将请求发送到终结点与 Microsoft 标识平台进行通信。 我们提供开源框架和库，用于处理这些请求的详细信息。 还可以通过创建对这些终结点的请求，选择自行实现身份验证逻辑：

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>单页应用 (JavaScript)

许多新式应用都有一个单页应用前端（主要以 JavaScript 编写）。 通常，这通过 AngularJS、Ember.js 或 Durandal.js 等框架编写。 Microsoft 标识平台终结点支持通过使用这些应用程序[OAuth 2.0 隐式流](v2-oauth2-implicit-grant-flow.md)。

在此流中，应用程序收到令牌直接从 Microsoft 标识平台的授权终结点，而无需任何服务器到服务器的交换。 所有身份验证逻辑和会话处理完全在 JavaScript 客户端中发生，无需进行额外的页面重定向。

![隐式身份验证流](./media/v2-app-types/convergence-scenarios-implicit.svg)

若要查看此方案的操作，请尝试中的单页面应用代码示例[Microsoft 标识平台入门](v2-overview.md#getting-started)部分。

## <a name="web-apps"></a>Web 应用

对于通过浏览器访问的 Web 应用（.NET、PHP、Java、Ruby、Python、Node 等），可以使用 [OpenID Connect](active-directory-v2-protocols.md) 来执行用户登录。 在 OpenID Connect 中，Web 应用接收 ID 令牌。 ID 令牌是一个安全令牌，用于验证用户的标识并以声明形式提供有关用户的信息：

```
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

Microsoft 标识平台终结点中使用的令牌的不同类型的更多详细信息位于[访问令牌](access-tokens.md)引用和[id_token 引用](id-tokens.md)

在 Web 服务器应用中，登录身份验证流采用以下高级步骤：

![Web 应用身份验证流](./media/v2-app-types/convergence-scenarios-webapp.svg)

可以通过使用来自 Microsoft 标识平台终结点的公共签名密钥验证 ID 令牌来确保用户的标识。 设置会话 Cookie，在后续页面请求中将其用于识别用户。

若要查看此方案的操作，请尝试在中的 web 应用登录代码示例[Microsoft 标识平台入门](v2-overview.md#getting-started)部分。

除了简单登录，Web 服务器应用可能还需要访问其他 Web 服务，例如 REST API。 在这种情况下，Web 服务器应用可以使用 [OAuth 2.0 授权代码流](active-directory-v2-protocols.md)参与合并的 OpenID Connect 和 OAuth 2.0 流。 有关此方案的详细信息，请阅读 [Web 应用和 Web API 入门](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)。

## <a name="web-apis"></a>Web API

Microsoft 标识平台终结点可用于保护 web 服务，例如应用的 RESTful Web API。 Web API 使用 OAuth 2.0 访问令牌（而不是 ID 令牌和会话 Cookie）来保护数据并对传入的请求进行身份验证。 Web API 调用方会在 HTTP 请求的授权标头中附加一个访问令牌，如下所示：

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API 使用此访问令牌来验证 API 调用方的标识，并从访问令牌中编码的声明里提取调用方的相关信息。 Microsoft 标识平台终结点中使用的令牌的不同类型的更多详细信息位于[访问令牌](access-tokens.md)引用和[id_token 引用](id-tokens.md)

Web API 可让用户通过公开权限（也称为[范围](v2-permissions-and-consent.md)）来选择添加/排除特定的功能或数据。 为了使调用应用能够获取某个范围的权限，用户必须在执行流的过程中同意该范围。 Microsoft 标识平台终结点要求用户提供权限，，然后在 Web API 收到的所有访问令牌中记录的权限。 Web API 验证每次调用中接收的访问令牌，并执行授权检查。

Web API 可以从各种应用接收访问令牌，其中包括 Web 服务器应用、桌面和移动应用、单页应用、服务器端守护程序，甚至其他 Web API。 Web API 的高级流如下所示：

![Web API 应用身份验证流](./media/v2-app-types/convergence-scenarios-webapi.svg)

若要了解如何使用 OAuth2 访问令牌保护 Web API，请参阅中的 Web API 代码示例[Microsoft 标识平台入门](v2-overview.md#getting-started)部分。

在许多情况下，web Api 还需要对其他下游 web Api 受保护的 Microsoft 标识平台进行出站请求。 若要执行此操作，web Api 可以利用**上代表的**流，它允许 web API 来交换另一个访问令牌，以在出站请求中使用的传入访问令牌。 有关详细信息，请参阅[Microsoft 标识平台和 OAuth 2.0 代理流](v2-oauth2-on-behalf-of-flow.md)。

## <a name="mobile-and-native-apps"></a>移动和本机应用

安装在设备中的应用（如移动和桌面应用）通常需要访问用于存储数据和代表用户执行各种功能的后端服务或 Web API。 这些应用可以使用 [OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)将登录凭据和授权添加到后端服务。

在此流中，应用接收授权代码从 Microsoft 标识平台终结点时用户登录。 授权代码表示应用有权代表登录用户调用后端服务。 应用可以通过在后台交换授权代码获得 OAuth 2.0 访问令牌和刷新令牌。 应用可以使用访问令牌在 HTTP 请求中向 Web API 进行身份验证，并可以在旧的访问令牌过期时，用刷新令牌获取新的访问令牌。

![本机应用身份验证流](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>守护程序和服务器端应用

包含长时运行进程或无需用户交互便可操作的应用还需要通过其他方法访问受保护的资源，例如 Web API。 这些应用可以通过 OAuth 2.0 客户端凭据流使用应用的标识（而不是用户的委派标识）来进行身份验证和获取令牌。 可以使用客户端机密或证书证明应用的身份。 有关详细信息，请参阅[对 Microsoft 标识平台在守护程序应用中使用证书进行身份验证](https://azure.microsoft.com/resources/samples/active-directory-dotnet-daemon-certificate-credential/)。

在此流中，应用程序直接交互`/token`终结点以获取访问权限：

![守护程序应用身份验证流](./media/v2-app-types/convergence-scenarios-daemon.svg)

若要生成守护程序，请参阅[客户端凭据文档](v2-oauth2-client-creds-grant-flow.md)，或者尝试 [.NET 示例应用](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)。
