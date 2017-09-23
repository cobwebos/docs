---
title: "Azure Active Directory v2.0 终结点的应用类型 |Microsoft Docs"
description: "Azure Active Directory v2.0 终结点支持的应用和方案类型。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 9d59e7f0e8f326c40be86e199d7712f6c565cc13
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---
# <a name="app-types-for-the-azure-active-directory-v20-endpoint"></a>Azure Active Directory v2.0 终结点的应用类型
Azure Active Directory (Azure AD) v2.0 终结点支持各种现代应用体系结构的身份验证，所有这些体系结构都基于行业标准协议 [OAuth 2.0 或 OpenID Connect](active-directory-v2-protocols.md)。 本指南介绍可以通过 Azure AD v2.0 构建的应用的类型，而不考虑首选语言或平台。 本文旨在帮助你在[开始处理代码](active-directory-appmodel-v2-overview.md#getting-started)之前了解高级方案。

> [!NOTE]
> v2.0 终结点并不支持所有 Azure Active Directory 方案和功能。 若要确定是否应使用 v2.0 终结点，请阅读 [v2.0 限制](active-directory-v2-limitations.md)。
> 
> 

## <a name="the-basics"></a>基础知识
必须在 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com)中注册使用 v2.0 终结点的每个应用。 应用注册过程将收集这些值并将其分配给应用：

* 用于唯一标识应用的**应用程序 ID**
* 用于将响应定向回应用的重定向 URI
* 其他一些特定于方案的值

有关详细信息，请了解如何[注册应用](active-directory-v2-app-registration.md)。

注册应用后，应用通过将请求发送到 Azure AD v2.0 终结点与 Azure AD 进行通信。 我们提供开源框架和库，用于处理这些请求的详细信息。 还可以通过创建对这些终结点的请求，选择自行实现身份验证逻辑：

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

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

可以在 [v2.0 令牌参考](active-directory-v2-tokens.md)中了解提供给应用的各种令牌和声明。

在 Web 服务器应用中，登录身份验证流采用以下高级步骤：

![Web 应用身份验证流](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

可以利用从 v2.0 终结点接收到的公用签名密钥通过验证 ID 令牌来确定用户标识。 设置会话 Cookie，在后续页面请求中将其用于识别用户。

若要查看此方案的工作方式，请尝试运行 v2.0 [入门部分](active-directory-appmodel-v2-overview.md#getting-started)中提供的 Web 应用登录代码示例之一。

除了简单登录，Web 服务器应用可能还需要访问其他 Web 服务，例如 REST API。 在这种情况下，Web 服务器应用可以使用 [OAuth 2.0 授权代码流](active-directory-v2-protocols.md)参与合并的 OpenID Connect 和 OAuth 2.0 流。 有关此方案的详细信息，请阅读 [Web 应用和 Web API 入门](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)。

## <a name="web-apis"></a>Web API
可以使用 v2.0 终结点来保护 Web 服务，例如应用的 RESTful Web API。 Web API 使用 OAuth 2.0 访问令牌（而不是 ID 令牌和会话 Cookie）来保护数据并对传入的请求进行身份验证。 Web API 调用方会在 HTTP 请求的授权标头中附加一个访问令牌，如下所示：

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API 使用此访问令牌来验证 API 调用方的标识，并从访问令牌中编码的声明里提取调用方的相关信息。 若要了解提供给应用的各种令牌和声明，请参阅 [v2.0 令牌参考](active-directory-v2-tokens.md)。

Web API 可让用户通过公开权限（也称为[范围](active-directory-v2-scopes.md)）来选择添加/排除特定的功能或数据。 为了使调用应用能够获取某个范围的权限，用户必须在执行流的过程中同意该范围。 v2.0 终结点向用户请求权限，并将这些权限记录在 Web API 收到的所有访问令牌中。 Web API 验证每次调用中接收的访问令牌，并执行授权检查。

Web API 可以从各种应用接收访问令牌，其中包括 Web 服务器应用、桌面和移动应用、单页应用、服务器端守护程序，甚至其他 Web API。 Web API 的高级流如下所示：

![Web API 应用身份验证流](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

若要了解如何使用 OAuth2 访问令牌保护 Web API，请查看[入门](active-directory-appmodel-v2-overview.md#getting-started)部分提供的 Web API 代码示例。

在许多情况下，Web API 还需要对由 Azure Active Directory 保护的其他下游 Web API 发出出站请求。  为执行此操作，Web API 可以利用 Azure AD 的代理流，它允许 Web API 将传入的访问令牌替换为将在出站请求中使用的另一个访问令牌。  [此处](active-directory-v2-protocols-oauth-on-behalf-of.md)详细介绍 v2.0 终结点的代理流。

## <a name="mobile-and-native-apps"></a>移动和本机应用
安装在设备中的应用（如移动和桌面应用）通常需要访问用于存储数据和代表用户执行各种功能的后端服务或 Web API。 这些应用可以使用 [OAuth 2.0 授权代码流](active-directory-v2-protocols-oauth-code.md)将登录凭据和授权添加到后端服务。

在此流中，应用会在用户登录时接收来自 v2.0 终结点的授权代码。 授权代码表示应用有权代表登录用户调用后端服务。 应用可以通过在后台交换授权代码获得 OAuth 2.0 访问令牌和刷新令牌。 应用可以使用访问令牌在 HTTP 请求中向 Web API 进行身份验证，并可以在旧的访问令牌过期时，用刷新令牌获取新的访问令牌。

![本机应用身份验证流](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>单页应用 (JavaScript)
许多新式应用都有一个单页应用前端（主要以 JavaScript 编写）。 通常，这通过 AngularJS、Ember.js 或 Durandal.js 等框架编写。 Azure AD v2.0 终结点通过使用 [OAuth 2.0 隐式流](active-directory-v2-protocols-implicit.md)支持这些应用。

在此流中，应用直接从 v2.0 授权终结点接收令牌，无需任何服务器到服务器的交换。 所有身份验证逻辑和会话处理完全在 JavaScript 客户端中发生，无需进行额外的页面重定向。

![隐式身份验证流](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

若要查看此方案的实际运行情况，请尝试运行[入门](active-directory-appmodel-v2-overview.md#getting-started)部分提供的单页应用代码示例之一。

## <a name="daemons-and-server-side-apps"></a>守护程序和服务器端应用
包含长时运行进程或无需用户交互便可操作的应用还需要通过其他方法访问受保护的资源，例如 Web API。 这些应用可以通过 OAuth 2.0 客户端凭据流使用应用的标识（而不是用户的委派标识）来进行身份验证和获取令牌。

在该流中，应用通过直接与 `/token` 终结点交互来获取令牌：

![守护程序应用身份验证流](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

若要生成守护程序，请参阅[入门](active-directory-appmodel-v2-overview.md#getting-started)部分中的客户端凭据文档，或者尝试 [.NET 示例应用](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)。

