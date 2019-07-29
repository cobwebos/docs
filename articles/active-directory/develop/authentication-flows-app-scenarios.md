---
title: Microsoft 标识平台中的身份验证方案 | Azure
description: 了解 Microsoft 标识平台的身份验证流和应用程序方案。 了解可以验证标识、获得令牌和调用受保护的 API 的不同应用程序类型。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3d9f96f0b61129a0f881c8fe8676bd5df7376ad
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494578"
---
# <a name="authentication-flows-and-application-scenarios"></a>身份验证流和应用程序方案

Microsoft 标识平台 (v2.0) 终结点支持各种现代应用体系结构的身份验证，所有这些体系结构都基于行业标准协议 [OAuth 2.0 或 OpenID Connect](active-directory-v2-protocols.md)。  使用[身份验证库](reference-v2-libraries.md)、应用程序身份验证标识并获得令牌来访问受保护的 API。 本文介绍了不同的身份验证流和它们使用的应用程序方案。  本文还提供了[应用程序方案和受支持的身份验证流](#scenarios-and-supported-authentication-flows)以及[应用程序方案和受支持的平台和语言](#scenarios-and-supported-platforms-and-languages)列表。

## <a name="application-categories"></a>应用程序类别

可以从多个应用程序类型中获取令牌：Web 应用程序、移动和桌面应用程序、Web API 以及在没有浏览器（或 iOT）的设备上运行的应用程序。 可以按以下方式对应用程序进行分类：

- [受保护的资源与客户端应用程序](#protected-resources-vs-client-applications)。 一些方案涉及保护资源（Web 应用或 Web API），另一些涉及获取安全令牌来调用受保护的 Web API。
- [涉及或不涉及用户](#with-users-or-without-users)。 一些方案涉及已登录的用户，而其他方案不涉及用户（守护程序方案）。
- [单页应用程序、公共客户端应用程序和机密客户端应用程序](#single-page-applications-public-client-applications-and-confidential-client-applications)。 应用程序类型有三大类。 用于处理它们的库和对象将有所不同。
- [登录受众](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types)。 某些身份验证流无法用于特定的登录访问群体。 某些流仅适用于工作或学校帐户，某些流可用于工作或学校帐户和个人 Microsoft 帐户。 允许的受众取决于身份验证流。
- [支持的 OAuth 2.0 流](#scenarios-and-supported-authentication-flows)。  身份验证流用于实现请求令牌的应用程序方案。  应用程序方案和身份验证流之间不存在一对一的映射。
- [支持的平台](#scenarios-and-supported-platforms-and-languages)。 并非所有应用程序方案都适用于每个平台。

### <a name="protected-resources-vs-client-applications"></a>受保护的资源与客户端应用程序

身份验证方案涉及两个活动：

- “获取安全令牌”，用于受保护的 Web API  。 Microsoft 建议使用[身份验证库](reference-v2-libraries.md#microsoft-supported-client-libraries)来获取令牌，特别是使用 MicroSoft 身份验证库系列 (MSAL)
- “保护 Web API”（或 Web 应用）  。 保护资源（Web 应用或 Web API）的一大难题是验证安全令牌。 Microsoft 在某些平台上提供了[中间件库](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)。

### <a name="with-users-or-without-users"></a>涉及或不涉及用户

大多数身份验证方案代表（已登录）用户获取令牌  。

![涉及用户的方案](media/scenarios/scenarios-with-users.svg)

不过，也存在应用程序将代表自己获取令牌（无用户）的方案（守护程序应用）。

![守护程序应用](media/scenarios/daemon-app.svg)

### <a name="single-page-applications-public-client-applications-and-confidential-client-applications"></a>单页应用程序、公共客户端应用程序和机密客户端应用程序

可以从多个应用程序类型中获取安全令牌。 应用程序通常分为三类：

- **单页应用程序 (SPA)** ，这是 Web 应用程序的一种形式，其中令牌是从浏览器中运行的应用获取的，该应用是使用 JavaScript 或 Typescript 编写的。 许多新式应用都有一个单页应用前端（主要以 JavaScript 编写）。 通常，该应用可使用 Angular、React 或 Vue 等框架进行编写。 MSAL 是唯一支持单页面应用程序的 Microsoft 身份验证库。

- **公共客户端应用程序**，始终可使用户登录。 这些应用包括：
  - 代表已登录用户调用 Web API 的桌面应用程序。
  - 移动应用程序。
  - 第三种类型的应用程序，在没有浏览器的设备上运行（例如在 iOT 上运行的无浏览器应用）。

  它们由名为 [PublicClientApplication](msal-client-applications.md)的 MSAL 类表示。

- **机密客户端应用程序**
  - 调用 Web API 的 Web 应用程序
  - 调用 Web API 的 Web API
  - 守护程序应用程序（即使作为类似 Linux 上的守护程序或 Windows 服务实现）
 
  这些类型的应用使用 [ConfidentialClientApplication](msal-client-applications.md)

## <a name="application-scenarios"></a>应用程序方案

Microsoft 标识平台终结点支持多种应用体系结构的身份验证：单页应用、Web 应用、Web API、移动和本机应用以及守护程序和服务器端应用。  应用程序使用各种身份验证流来登录用户并获取令牌，以调用受保护的 API。

### <a name="single-page-application"></a>单页应用程序

许多现代 Web 应用程序是作为客户端单页应用程序构建的，此类应用程序使用 JavaScript 或 SPA 框架（例如 Angular、Vue.js 和 React.js）编写。 这些应用程序在 Web 浏览器中运行，其身份验证特征不同于传统的服务器端 Web 应用程序。 Microsoft 标识平台允许单页应用程序登录用户并获取用于访问后端服务或 Web API 的令牌。

![单页应用程序](media/scenarios/spa-app.svg)

有关详细信息，请阅读[单页应用程序](scenario-spa-overview.md)。

### <a name="web-application-signing-in-a-user"></a>使用户登录的 Web 应用程序

![通过 Web 应用让用户登录](media/scenarios/scenario-webapp-signs-in-users.svg)

可使用以下内容保护 Web 应用（使用户登录）： 

- 在 .NET 中，使用 ASP.NET 或带 ASP.NET Open ID Connect 中间件的 ASP.NET Core。 在内部，保护涉及验证安全令牌的资源由[适用于 .NET 的 IdentityModel 扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)库而非 MSAL 库完成

- 如果在 node.js 中进行开发，将使用 Passport.js。

有关详细信息，请阅读[用于登录用户的 Web 应用](scenario-web-app-sign-user-overview.md)。

### <a name="web-application-signing-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>让用户登录并代表用户调用 Web API 的 Web 应用程序

![调用 Web API 的 Web 应用](media/scenarios/web-app.svg)

在 Web 应用中，若要代表用户调用 Web API，请使用 MSAL `ConfidentialClientApplication`  。 可使用授权代码流，在令牌缓存中存储获取的令牌。 然后，控制器在需要时将以无提示方式从缓存中获取令牌。 MSAL 根据需要刷新该令牌。

有关详细信息，请阅读[用于调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)。

### <a name="desktop-application-calling-a-web-api-on-behalf-of-the-signed-in-user"></a>代表已登录用户调用 Web API 的桌面应用程序

若要从登录用户的桌面应用程序调用 Web API，请使用 MSAL 的 PublicClientApplication 的交互式令牌获取方法。 利用这些交互方法可以控制登录 UI 体验。 为了实现这种交互，MSAL 利用了 Web 浏览器。

![桌面型](media/scenarios/desktop-app.svg)

对于在加入 Windows 域或联接 AAD 的计算机上运行的 Windows 托管应用程序，则有另一种可能性。 这些应用程序可以通过使用[集成 Windows 身份验证](https://aka.ms/msal-net-iwa)以无提示方式获取令牌。

在无浏览器的设备上运行的应用程序仍将能代表用户调用 API。 若要进行身份验证，用户必须登录具有 Web 浏览器的另一台设备。 若要启用这种方案，将需要使用[设备代码流](https://aka.ms/msal-net-device-code-flow)

![设备代码流](media/scenarios/device-code-flow-app.svg)

最后，可以在公共客户端应用程序中使用[用户名/密码](https://aka.ms/msal-net-up)，虽然我们并不推荐这种方式。 在某些方案（比如 DevOps）中仍需要此流，但请注意，使用该流将对应用程序施加约束。 例如，使用该流的应用不能让需要运行多重身份验证（条件访问）的用户登录。 它也不会使应用程序受益于单一登录。 使用用户名/密码进行身份验证这种方式违反新式身份验证原则，仅出于遗留原因提供。

在“桌面应用程序”中，如果希望令牌缓存能持久，应[自定义令牌缓存序列化](https://aka.ms/msal-net-token-cache-serialization)。 你甚至可以通过[双重令牌缓存序列化](https://aka.ms/msal-net-dual-cache-serialization)来启用身份验证库（ADAL.NET 3.x 和 4.x）前一代向后和向前兼容的令牌缓存。

有关详细信息，请阅读[用于调用 Web API 的桌面应用](scenario-desktop-overview.md)。

### <a name="mobile-application-calling-a-web-api-on-behalf-of-the-user-whos-signed-in-interactively"></a>代表以交互方式登录的用户调用 Web API 的移动应用程序

类似于桌面应用程序，移动应用程序将使用 MSAL 的 PublicClientApplication 的交互式令牌获取方法来获取调用 Web API 所需的令牌。

![移动型](media/scenarios/mobile-app.svg)

MSAL iOS 和 MSAL Android 默认使用系统 Web 浏览器。 但是，你也可以指示它使用嵌入式 Web 视图。 也有一些特殊情况，具体取决于移动平台：（UWP、iOS、Android）。

某些方案（涉及设备 ID 相关的条件性访问）或注册的设备需要在设备上安装[代理](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)。 代理的示例包括 Microsoft 公司门户（Android 上）、Microsoft Authenticator（Android 和 iOS）。 MSAL 现在能够与代理交互。

> [!NOTE]
> 移动应用（使用 MSAL.iOS、MSAL.Android 或 MSAL.NET/Xamarin）可以应用应用保护策略（例如，阻止用户复制某些受保护的文本）。 这[由 Intune 管理](https://docs.microsoft.com/intune/app-sdk)并被 Intune 识别为托管应用。 [Intune SDK](https://docs.microsoft.com/intune/app-sdk-get-started)独立于 MSAL 库，并单独与 AAD 交流。

有关详细信息，请阅读[用于调用 Web API 的移动应用](scenario-mobile-overview.md)。

### <a name="protected-web-api"></a>受保护的 Web API

可以使用 Microsoft 标识平台终结点来保护 Web 服务，例如应用的 RESTful Web API。 受保护的 Web API 通过访问令牌进行调用，以保护其数据并对传入的请求进行身份验证。 Web API 调用方会在 HTTP 请求的授权标头中附加一个访问令牌。 如果希望保护 ASP.NET 或 ASP.NET Core Web API，则需要验证访问令牌。 为此，将使用 ASP.NET JWT 中间件。 在后台，验证是由[适用于.Net 的 IdentityModel 扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)库（而非 MSAL.NET）完成的

有关详细信息，请阅读[受保护的 Web API](scenario-protected-web-api-overview.md)。

### <a name="web-api-calling-another-downstream-web-api-on-behalf-of-the-user-for-whom-it-was-called"></a>Web API 代表调用它的用户调用其他下游 Web API

此外，如果你希望 ASP.NET 或受 ASP.NET Core 保护的 Web API 代表用户调用另一个 Web API，则应用将需要使用 ConfidentialClientApplication 的方法“[代表用户](https://aka.ms/msal-net-on-behalf-of)获取令牌”来获取下游 Web API 的令牌。 这也称为服务到服务调用。
调用其他 Web API 的 Web API 还需要提供自定义缓存序列化

  ![Web API](media/scenarios/web-api.svg)

有关详细信息，请阅读[用于调用 Web API 的 Web API](scenario-web-api-call-api-overview.md)。

### <a name="desktopservice-or-web-daemon-application-calling-web-api-without-a-user-in-its-own-name"></a>调用 Web API 但其名称中不涉及用户的桌面/服务或 Web 守护程序应用程序

包含长时运行进程或无需用户交互便可操作的应用还需要通过其他方法访问受保护的 Web API。 这些应用可以使用应用的标识（而不是用户的委派标识）来进行身份验证和获取令牌。 可以使用客户端机密或证书证明其身份。
可以使用 MSAL 的 ConfidentialClientApplication 的[客户端凭据](https://aka.ms/msal-net-client-credentials)获取方法，编写此类获取顶层应用令牌的应用（守护程序应用）。 这些操作假设应用以前已使用 Azure AD 注册了机密（应用程序密码、证书或客户端断言），而该机密随后与此调用进行了共享。

![守护程序应用](media/scenarios/daemon-app.svg)

有关详细信息，请阅读[用于调用 Web API 的守护程序应用程序](scenario-daemon-overview.md)。

## <a name="scenarios-and-supported-authentication-flows"></a>方案和受支持的身份验证流

涉及获取令牌的方案还会映射到 [Microsoft 标识平台协议](active-directory-v2-protocols.md)的详细信息中所述的 OAuth 2.0 身份验证流

|场景 | 详细的方案演练 | OAuth 2.0 Flow/Grant | 目标受众 |
|--|--|--|--|
| [![单页应用](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | [单页应用](scenario-spa-overview.md) | [隐式](v2-oauth2-implicit-grant-flow.md) | 工作或学校帐户以及个人帐户、B2C
| [![用于让用户登录的 Web 应用](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | [用于让用户登录的 Web 应用](scenario-web-app-sign-user-overview.md) | [授权代码](v2-oauth2-auth-code-flow.md) | 工作或学校帐户以及个人帐户、B2C |
| [![用于调用 Web API 的 Web 应用](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | [用于调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md) | [授权代码](v2-oauth2-auth-code-flow.md) | 工作或学校帐户以及个人帐户、B2C |
| [![用于调用 Web API 的桌面应用](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) | [用于调用 Web API 的 桌面应用](scenario-desktop-overview.md)| 交互式（[授权代码](v2-oauth2-auth-code-flow.md)与 PKCE） | 工作或学校帐户以及个人帐户、B2C |
| | | 集成的 Windows | 工作或学校帐户 |
| | | [资源所有者密码](v2-oauth-ropc.md)  | 工作或学校帐户、B2C |
| ![设备代码流](media/scenarios/device-code-flow-app.svg)| [用于调用 Web API 的 桌面应用](scenario-desktop-overview.md) | [设备代码](v2-oauth2-device-code.md)  | 工作或学校帐户* |
| [![用于调用 Web API 的移动应用](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | [用于调用 Web API 的移动应用](scenario-mobile-overview.md) | 交互式（[代码](v2-oauth2-auth-code-flow.md)与 PKCE）  |   工作或学校帐户以及个人帐户、B2C
| | | 资源所有者密码  | 工作或学校帐户、B2C |
| [![守护程序应用](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | [守护程序应用](scenario-daemon-overview.md) | [客户端凭据](v2-oauth2-client-creds-grant-flow.md)  |   仅限 AAD 组织上的应用程序权限（无用户）
| [![用于调用 Web API 的 Web API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | [用于调用 Web API 的 Web API](scenario-web-api-call-api-overview.md)| [代表](v2-oauth2-on-behalf-of-flow.md) | 工作或学校帐户以及个人帐户 |

## <a name="scenarios-and-supported-platforms-and-languages"></a>方案和受支持的平台和语言

并非每个应用程序类型在每个平台上都可用。 你还可以使用多种语言来构建你的应用程序。 Microsoft 身份验证库支持多种“平台”（JavaScript、.NET Framework、.net Core、Windows 10/UWP、Xamarin.iOS、Xamarin.Android、本机 iOS、本机 Android、Java、Python） 

|场景  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [单页应用](scenario-spa-overview.md) <br/>[![单页应用](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/logo_js.png) MSAL.js
| [用于让用户登录的 Web 应用](scenario-web-app-sign-user-overview.md) <br/>[![用于让用户登录的 Web 应用](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET](media/sample-v2-code/logo_NET.png)</br> ASP.NET ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core
| [用于调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md) <br/> [![用于调用 Web API 的 Web 应用](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET](media/sample-v2-code/logo_NET.png) </br> ASP.NET + MSAL.NET </br> ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/logo_NETcore.png)ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) Flask + MSAL Python
| [用于调用 Web API 的 桌面应用](scenario-desktop-overview.md) <br/> [![用于调用 Web API 的桌面应用](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md)![设备代码流](media/scenarios/device-code-flow-app.svg) | ![MSAL.NET](media/sample-v2-code/logo_NET.png)  MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python
| [用于调用 Web API 的移动应用](scenario-mobile-overview.md) <br/> [![用于调用 Web API 的移动应用](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/logo_xamarin.png) MSAL.NET | | | ![iOS / Objective C 或 swift](media/sample-v2-code/logo_iOS.png) MSAL.iOS | ![Android](media/sample-v2-code/logo_Android.png) MSAL.Android
| [守护程序应用](scenario-daemon-overview.md) <br/> [![守护程序应用](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET](media/sample-v2-code/logo_NET.png) MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python| ![.NET Core](media/sample-v2-code/logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/logo_java.png) msal4j ![MSAL Python](media/sample-v2-code/logo_python.png) MSAL Python
| [用于调用 Web API 的 Web API](scenario-web-api-call-api-overview.md) <br/> [![用于调用 Web API 的 Web API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![.NET](media/sample-v2-code/logo_NET.png) <br/> ASP.NET + MSAL.NET ![.NET Core](media/sample-v2-code/logo_NETcore.png) <br/> ASP.NET Core + MSAL.NET| ![.NET Core](media/sample-v2-code/logo_NETcore.png) <br/> ASP.NET Core + MSAL.NET| ![.NET Core](media/sample-v2-code/logo_NETcore.png)<br/> ASP.NET Core + MSAL.NET

另请参阅[按 OS/语言划分的 Microsoft 支持的库](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)

## <a name="next-steps"></a>后续步骤
详细了解[身份认证基本信息](authentication-scenarios.md)和[访问令牌](access-tokens.md)。