---
title: Microsoft 标识平台身份验证流和应用方案 | Azure
description: 了解 Microsoft 标识平台的应用方案，包括对标识进行身份验证、获取令牌，以及调用受保护的 API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: ff92f66d4d7b96660fe83b79273f65ca50eb22f9
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89292888"
---
# <a name="authentication-flows-and-application-scenarios"></a>身份验证流和应用程序方案

Microsoft 标识平台 (v2.0) 终结点支持各种新式应用程序体系结构的身份验证。 所有这些体系结构基于行业标准协议 [OAuth 2.0 和 OpenID Connect](active-directory-v2-protocols.md)。 使用 [Microsoft 标识平台的身份验证库](reference-v2-libraries.md)，应用程序可以对标识进行身份验证并获得令牌，以便访问受保护的 API。

本文介绍身份验证流及其应用方案。

## <a name="application-categories"></a>应用程序类别

获取的令牌可以来自多种类型的应用程序，其中包括：

- Web 应用
- 移动应用
- 桌面应用
- Web API

还可以通过设备上运行的应用获取令牌，这些设备没有浏览器或在物联网 (IoT) 上运行。

以下部分介绍应用程序的类别。

### <a name="protected-resources-vs-client-applications"></a>受保护的资源与客户端应用程序

身份验证方案涉及两个活动：

- **获取受保护 Web API 的安全令牌**：建议使用 [Microsoft 支持的客户端库](reference-v2-libraries.md#microsoft-supported-client-libraries)来获取令牌。 具体而言，建议使用 Microsoft 身份验证库 (MSAL) 系列。
- **保护 Web API 或 Web 应用**：保护这些资源的一大难题是验证安全令牌。 Microsoft 在某些平台上提供[中间件库](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)。

### <a name="with-users-or-without-users"></a>涉及或不涉及用户

大多数身份验证方案代表已登录的用户获取令牌。

![涉及用户的方案](media/scenarios/scenarios-with-users.svg)

但是，也有守护程序应用。 在这些方案中，应用程序以自身身份获取令牌，而不涉及到用户。

![涉及守护程序应用的方案](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>单页、公共客户端和机密客户端应用程序

可通过多种类型的应用程序获取安全令牌。 这些应用程序往往划分为以下三种类别。 每种应用程序配合不同的库和对象使用。

- **单页应用程序**：简称为 SPA。它们是一些 Web 应用，其中的令牌是通过浏览器中运行的 JavaScript 或 TypeScript 应用获取的。 许多新式应用的前端都有一个单页应用程序（主要用 JavaScript 编写）。 该应用程序通常使用 Angular、React 或 Vue 等框架。 MSAL 是唯一支持单页应用程序的 Microsoft 身份验证库。

- **公共客户端应用程序**：此类别中的应用（例如以下类型）始终以用户身份登录：
  - 以登录的用户身份调用 Web API 的桌面应用
  - 移动应用
  - 在没有浏览器的设备上运行的应用，例如，在 IoT 上运行的应用
  
- **机密客户端应用程序**：此类别中的应用包括：
  - 调用 Web API 的 Web 应用
  - 调用 Web API 的 Web API
  - 守护程序应用（即使实施为 Linux 守护程序或 Windows 服务等控制台服务）

### <a name="sign-in-audience"></a>登录受众

可用的身份验证流因登录受众而异。 一些流仅适用于工作或学校帐户。 对于工作或学校帐户以及个人 Microsoft 帐户，还可以使用其他帐户。

有关详细信息，请参阅[支持的帐户类型](v2-supported-account-types.md#account-type-support-in-authentication-flows)。

## <a name="application-scenarios"></a>应用程序方案

Microsoft 标识平台终结点支持对以下应用体系结构进行身份验证：

- 单页应用
- Web 应用
- Web API
- 移动应用
- 本机应用
- 守护程序应用
- 服务器端应用

应用程序使用不同的身份验证流将用户登录和获取令牌，以调用受保护的 API。

### <a name="single-page-application"></a>单页应用程序

许多新式 Web 应用都是作为客户端单页应用程序构建的。 这些应用程序使用 JavaScript 或一个框架，如 "角度"、"Vue" 和 "响应"。 这些应用程序在 Web 浏览器中运行。

单页应用程序在身份验证特征方面与传统的服务器端 Web 应用不同。 单页应用程序可以使用 Microsoft 标识平台将用户登录，并获取用于访问后端服务或 Web API 的令牌。 Microsoft 标识平台为 JavaScript 应用程序提供了两种授予类型： 

| MSAL.js (2.x)  | MSAL.js (1.x)  |
|---|---|
| ![单页应用程序身份验证](media/scenarios/spa-app-auth.svg) | ![隐式单页面应用程序](media/scenarios/spa-app.svg) |

### <a name="web-app-that-signs-in-a-user"></a>以用户身份登录的 Web 应用

![可将用户登录的 Web 应用](media/scenarios/scenario-webapp-signs-in-users.svg)

为了帮助保护以用户身份登录的 Web 应用：

- 如果在 .NET 中进行开发，请使用包含 ASP.NET OpenID Connect 中间件的 ASP.NET 或 ASP.NET Core。 保护资源涉及到验证安全令牌，为此，可以使用[适用于 .NET 的 IdentityModel 扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)，但不能使用 MSAL 库。

- 如果在 Node.js 中进行开发，则使用 [Passport.js](https://github.com/AzureAD/passport-azure-ad)。

有关详细信息，请参阅[可将用户登录的 Web 应用](scenario-web-app-sign-user-overview.md)。

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>以用户身份登录并以用户身份调用 Web API 的 Web 应用

![调用 Web API 的 Web 应用](media/scenarios/web-app.svg)

若要以用户身份通过 Web 应用调用 Web API，请使用授权代码流，并在令牌缓存中存储获取的令牌。 必要时，MSAL 可刷新令牌，而控制器可从缓存中以无提示方式获取令牌。

有关详细信息，请参阅[调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)。

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>以登录用户身份调用 Web API 的桌面应用

要使桌面应用能够以登录用户身份调用 Web API，请使用 MSAL 的交互式令牌获取方法。 使用这些交互方法可以控制登录 UI 体验。 MSAL 使用 Web 浏览器进行这种交互。

![调用 Web API 的桌面应用](media/scenarios/desktop-app.svg)

对于已加入 Windows 域或者由 Azure Active Directory (Azure AD) 管理的计算机上的 Windows 托管应用程序，存在另一种可能的情况。 这些应用程序可以使用 [Windows 集成身份验证](https://aka.ms/msal-net-iwa)以无提示方式获取令牌。

在无浏览器的设备上运行的应用程序仍可代表用户调用 API。 若要进行身份验证，用户必须登录到有 Web 浏览器的另一台设备。 此方案要求使用[设备代码流](https://aka.ms/msal-net-device-code-flow)。

![设备代码流](media/scenarios/device-code-flow-app.svg)

可以在公共客户端应用程序中使用[用户名/密码流](scenario-desktop-acquire-token.md#username-and-password)，不过我们并不建议使用。 在某些方案（例如 DevOps）中仍需要此流。

使用用户名/密码流会使应用程序受限。 例如，应用程序无法以需要使用 Azure AD 中的多重身份验证或条件访问的用户身份登录。 应用程序也无法受益于单一登录。 使用用户名/密码流进行身份验证的方式违反新式身份验证的原则，仅仅是出于遗留原因而提供的。

在桌面应用中，如果希望令牌缓存持久，请自定义[令牌缓存序列化](scenario-desktop-acquire-token.md#file-based-token-cache)。 通过实施[双重令牌缓存序列化](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3)，可以使用后向兼容和前向兼容的令牌缓存。 这些令牌支持以前代系的身份验证库。 具体的库包括适用于 .NET 的 Azure AD 身份验证库 (ADAL.NET) 版本 3 和 4。

有关详细信息，请参阅[调用 Web API 的桌面应用](scenario-desktop-overview.md)。

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>以交互式用户身份调用 Web API 的移动应用

类似于桌面应用，移动应用调用 MSAL 的交互式令牌获取方法来获取用于调用 Web API 的令牌。

![调用 Web API 的移动应用](media/scenarios/mobile-app.svg)

MSAL iOS 和 MSAL Android 默认使用系统 Web 浏览器。 但是，你可以指示它们改用嵌入式 Web 视图。 根据以下移动平台，存在一些特殊情况：通用 Windows 平台 (UWP)、iOS 或 Android。

某些方案（例如，涉及到与设备 ID 或设备注册相关的条件访问的方案）要求在设备上安装一个中介。 中介的示例包括 Android 上的 Microsoft 公司门户，以及 Android 和 iOS 上的 Microsoft Authenticator。 MSAL 现在可与中介交互。 有关中转站的详细信息，请参阅[利用 Android 和 iOS 上的中转站](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)。

有关详细信息，请参阅[调用 Web API 的移动应用](scenario-mobile-overview.md)。

> [!NOTE]
> 对于使用 MSAL.iOS、MSAL.Android 或 Xamarin 上的 MSAL.NET 的移动应用，可以应用应用保护策略。 例如，策略可以阻止用户复制受保护的文本。 移动应用由 Intune 托管，由 Intune 识别为托管应用。 有关详细信息，请参阅 [Microsoft Intune App SDK 概述](/intune/app-sdk)。
>
> [Intune 应用 SDK](/intune/app-sdk-get-started) 独立于 MSAL 库，可自行与 Azure AD 交互。

### <a name="protected-web-api"></a>受保护的 Web API

可以使用 Microsoft 标识平台终结点来保护 Web 服务，例如应用的 RESTful Web API。 通过访问令牌调用受保护的 Web API。 该令牌帮助保护 API 的数据并对传入请求进行身份验证。 Web API 调用方会在 HTTP 请求的授权标头中追加一个访问令牌。

若要保护 ASP.NET 或 ASP.NET Core Web API，需要验证访问令牌。 可以使用 ASP.NET JWT 中间件进行这种验证。 验证是由[适用于.NET 的 IdentityModel 扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)库而不是 MSAL.NET 完成的。

有关详细信息，请参阅[受保护的 Web API](scenario-protected-web-api-overview.md)。

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>以用户身份调用另一个 Web API 的 Web API

要使受保护的 Web API 能以用户身份调用另一个 Web API，应用需要获取下游 Web API 的令牌。 此类调用有时称为服务到服务调用。 调用其他 Web API 的 Web API 需要提供自定义缓存序列化。

![调用另一个 Web API 的 Web API](media/scenarios/web-api.svg)

有关详细信息，请参阅[调用 Web API 的 Web API](scenario-web-api-call-api-overview.md)。

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>在守护程序名称中调用 Web API 的守护程序应用

包含长时运行进程或无需用户交互即可运行的应用还需要通过某种方式访问安全的 Web API。 此类应用可以使用应用的标识进行身份验证和获取令牌。 应用将使用客户端机密或证书来证明其身份。

可以编写此类使用 MSAL 中的[客户端凭据](scenario-daemon-acquire-token.md#acquiretokenforclient-api)获取方法获取调用应用令牌的守护程序应用。 这些方法要求将客户端密码添加到 Azure AD 中的应用注册。 然后，该应用将与被调用守护程序共享机密。 此类机密的示例包括应用程序密码、证书断言和客户端断言。

![由其他应用和 API 调用的守护程序应用](media/scenarios/daemon-app.svg)

有关详细信息，请参阅[调用 Web API 的守护程序应用程序](scenario-daemon-overview.md)。

## <a name="scenarios-and-supported-authentication-flows"></a>方案和受支持的身份验证流

使用身份验证流实现请求令牌的应用程序方案。 应用程序方案和身份验证流之间不存在一对一的映射。

涉及到获取令牌的方案还会映射到 OAuth 2.0 身份验证流。 有关详细信息，请参阅 [Microsoft 标识平台上的 OAuth 2.0 和 OpenID Connect 协议](active-directory-v2-protocols.md)。

<table>
 <thead>
  <tr><th>方案</th> <th>详细方案演练</th> <th>OAuth 2.0 流和授权</th> <th>目标受众</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Auth code" src="media/scenarios/spa-app-auth.svg"></a></td>
   <td><a href="scenario-spa-overview.md">单页应用</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">授权代码</a> 与 PKCE</td>
   <td>工作或学校帐户、个人帐户和 Azure Active Directory B2C (Azure AD B2C) </td>
 </tr>

  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Implicit" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">单页应用</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">隐式</a></td>
   <td>工作或学校帐户、个人帐户和 Azure Active Directory B2C (Azure AD B2C) </td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">用于登录用户的 Web 应用</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">授权代码</a></td>
   <td>工作或学校帐户、个人帐户和 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">用于调用 Web API 的 Web 应用</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">授权代码</a></td>
   <td>工作或学校帐户、个人帐户和 Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">用于调用 Web API 的 桌面应用</a></td>
   <td>使用<a href="v2-oauth2-auth-code-flow.md">授权代码</a>和 PKCE 的交互式方法</td>
   <td>工作或学校帐户、个人帐户和 Azure AD B2C</td>
 </tr>

  <tr>
   <td>Windows 集成身份验证</td>
   <td>工作或学校帐户</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">资源所有者密码</a></td>
   <td>工作或学校帐户和 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">设备代码</a></td>
   <td>工作或学校帐户</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">用于调用 Web API 的移动应用</a></td>
   <td>使用<a href="v2-oauth2-auth-code-flow.md">授权代码</a>和 PKCE 的交互式方法</td>
   <td>工作或学校帐户、个人帐户和 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">资源所有者密码</a></td>
   <td>工作或学校帐户和 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">调用 Web API 的守护程序应用</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">客户端凭据</a></td>
   <td>不涉及用户的仅限应用的权限，仅用于 Azure AD 组织</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">用于调用 Web API 的 Web API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">代理</a></td>
   <td>工作或学校帐户和个人帐户</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>方案和受支持的平台和语言

Microsoft 身份验证库支持多种平台：

- Javascript
- .NET framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- 本机 iOS
- macOS
- 本机 Android
- Java
- Python

你还可以使用多种语言来构建你的应用程序。

> [!NOTE]
> 某些应用程序类型并非在每种平台上都可用。

在下表的“Windows”列中，每当提到 .NET Core，表示 .NET Framework 也可用。 省略后者是为了避免表格内容混杂。

|方案  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [单页应用](scenario-spa-overview.md) <br/>[![单页应用身份验证](media/scenarios/spa-app-auth.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [单页应用](scenario-spa-overview.md) <br/>[![单页面应用隐式](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [用于登录用户的 Web 应用](scenario-web-app-sign-user-overview.md) <br/>[![可将用户登录的 Web 应用](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [用于调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md) <br/> <br/>[![调用 Web API 的 Web 应用](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [用于调用 Web API 的 桌面应用](scenario-desktop-overview.md) <br/> <br/>[![调用 Web API 的桌面应用](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![设备代码流](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS / Objective C 或 swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [用于调用 Web API 的移动应用](scenario-mobile-overview.md) <br/> [![用于调用 Web API 的移动应用](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Objective C 或 swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [守护程序应用](scenario-daemon-overview.md) <br/> [![守护程序应用](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [用于调用 Web API 的 Web API](scenario-web-api-call-api-overview.md) <br/><br/> [![用于调用 Web API 的 Web API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

有关详细信息，请参阅[按 OS/语言列出的 Microsoft 支持的库](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)。

## <a name="next-steps"></a>后续步骤

* 详细了解[身份验证基础知识](./authentication-vs-authorization.md)和 [Microsoft 标识平台中的访问令牌](access-tokens.md)。
* 详细了解 [如何保护对 IoT 应用的访问](/azure/architecture/example-scenario/iot-aad/iot-aad)。