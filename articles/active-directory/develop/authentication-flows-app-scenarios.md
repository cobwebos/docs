---
title: Microsoft 标识平台身份验证流和应用方案 | Azure
description: 了解 Microsoft 标识平台的应用程序方案，包括身份验证标识、获取令牌和调用受保护的 Api。
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
ms.openlocfilehash: 89bafeb077fc83f4f3165d591006831bf8287875
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190477"
---
# <a name="authentication-flows-and-application-scenarios"></a>身份验证流和应用程序方案

Microsoft 标识平台 (v2.0) 终结点支持各种新式应用程序体系结构的身份验证。 所有这些体系结构基于行业标准协议 [OAuth 2.0 和 OpenID Connect](active-directory-v2-protocols.md)。  使用[Microsoft 标识平台身份验证库](reference-v2-libraries.md)，应用程序对标识进行身份验证，并获取令牌来访问受保护的 api。

本文介绍身份验证流和它们所使用的应用程序方案：

- [应用程序方案和支持的身份验证流](#scenarios-and-supported-authentication-flows)
- [应用程序方案和支持的平台与语言](#scenarios-and-supported-platforms-and-languages)

## <a name="application-categories"></a>应用程序类别

可以从多种类型的应用程序获取令牌，其中包括：

- Web 应用
- 移动应用
- 桌面应用
- Web API

还可以从在没有浏览器的设备上运行的应用或在 IoT 上运行的应用获取令牌。

可按以下列表中所述对应用程序进行分类：

- [受保护的资源与客户端应用程序](#protected-resources-vs-client-applications)：在某些情况下，可以保护 web 应用或 web api 等资源。 还有一些方案涉及到获取安全令牌来调用受保护的 Web API。
- [对于用户或没有用户](#with-users-or-without-users)：某些情况涉及到已登录的用户，但其他方案（如 daemon 方案）不涉及用户。
- [单页、公共客户端和机密客户端应用程序](#single-page-public-client-and-confidential-client-applications)：这些类型是应用程序的三个大类别。 每种应用程序配合不同的库和对象使用。
- [登录受众](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types)：可用的身份验证流因登录受众而异。 一些流仅适用于工作或学校帐户。 还有一些流同时适用于工作或学校帐户和 Microsoft 个人帐户。 允许的受众取决于身份验证流。
- [支持的 OAuth 2.0 流](#scenarios-and-supported-authentication-flows)：身份验证流用于实现请求令牌的应用程序方案。 应用程序方案和身份验证流之间不存在一对一的映射。
- [支持的平台](#scenarios-and-supported-platforms-and-languages)：并非所有应用程序方案都适用于每个平台。

### <a name="protected-resources-vs-client-applications"></a>受保护的资源与客户端应用程序

身份验证方案涉及两个活动：

- **获取受保护的 WEB API 的安全令牌**：我们建议你使用[microsoft 支持的客户端库](reference-v2-libraries.md#microsoft-supported-client-libraries)来获取令牌，特别是 microsoft 身份验证库（MSAL）家族。
- **保护 WEB api 或 web 应用**：保护 web api 或 web 应用资源的一个挑战是验证安全令牌。 Microsoft 在某些平台上提供[中间件库](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)。

### <a name="with-users-or-without-users"></a>涉及或不涉及用户

大多数身份验证方案代表已登录的用户获取令牌。

![涉及用户的方案](media/scenarios/scenarios-with-users.svg)

不过，在某些守护程序应用方案中，应用程序代表自身获取令牌，而不涉及到用户。

![涉及守护程序应用的方案](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>单页、公共客户端和机密客户端应用程序

可从多种类型的应用程序获取安全令牌。 这些应用程序往往划分为三种类别：

- **单页应用程序**：也称为 spa，这些应用是 web 应用，其中的令牌是从浏览器中运行的 JavaScript 或 TypeScript 应用获取的。 许多新式应用都有一个单页应用程序前端（主要以 JavaScript 编写）。 该应用程序通常使用 Angular、React 或 Vue 等框架。 MSAL 是唯一支持单页应用程序的 Microsoft 身份验证库。

- **公共客户端应用程序**：这些应用程序始终登录用户：
  - 代表已登录用户调用 Web API 的桌面应用
  - 移动应用
  - 在没有浏览器的设备上运行的应用，例如，在 iOT 上运行的应用

  这些应用由 MSAL [PublicClientApplication ](/dotnet/api/microsoft.identity.client.publicclientapplication) 类表示。 有关详细信息，请参阅[公共客户端和机密客户端应用程序](msal-client-applications.md)。

- **机密客户端应用程序**：
  - 调用 Web API 的 Web 应用
  - 调用 Web API 的 Web API
  - 守护程序应用（即使实施为 Linux 守护程序或 Windows 服务等控制台服务）

  此类应用使用 [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplication) 类。 有关详细信息，请参阅[公共客户端和机密客户端应用程序](msal-client-applications.md)。

## <a name="application-scenarios"></a>应用程序方案

Microsoft 标识平台终结点支持对不同应用程序体系结构进行身份验证：

- 单页应用
- Web 应用
- Web API
- 移动应用
- 本机应用
- 守护程序应用
- 服务器端应用

应用程序使用不同的身份验证流将用户登录和获取令牌，以调用受保护的 API。

### <a name="a-single-page-application"></a>单页应用程序

许多新式 web 应用是作为客户端单页面应用程序构建的。 这些使用 JavaScript 的应用程序或 Vue 的单页面应用程序框架（如）和响应。 这些应用程序在 Web 浏览器中运行。

其身份验证特性不同于传统的服务器端 web 应用。 单页应用程序可以使用 Microsoft 标识平台将用户登录，并获取用于访问后端服务或 Web API 的令牌。

![单页应用程序](media/scenarios/spa-app.svg)

有关详细信息，请参阅[单页应用程序](scenario-spa-overview.md)。

### <a name="a-web-app-that-is-signing-in-a-user"></a>正在将用户登录的 Web 应用

![可将用户登录的 Web 应用](media/scenarios/scenario-webapp-signs-in-users.svg)

若要保护正在将用户登录的 Web 应用：

- 如果在 .NET 中进行开发，请使用包含 ASP.NET Open ID Connect 中间件的 ASP.NET 或 ASP.NET Core。 保护资源涉及到验证安全令牌，为此，可以使用[适用于 .NET 的 IdentityModel 扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)库，但不能使用 MSAL 库。

- 如果在 Node.js 中进行开发，将使用 Passport.js。

有关详细信息，请参阅[可将用户登录的 Web 应用](scenario-web-app-sign-user-overview.md)。

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>可将用户登录并代表用户调用 Web API 的 Web 应用

![调用 Web API 的 Web 应用](media/scenarios/web-app.svg)

若要代表用户从 Web 应用调用 Web API，请使用 MSAL **ConfidentialClientApplication** 类。 使用授权代码流，并在令牌缓存中存储获取的令牌。 必要时，MSAL 可刷新令牌，而控制器可从缓存中以无提示方式获取令牌。

有关详细信息，请参阅[调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md)。

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>代表已登录用户调用 Web API 的桌面应用

要使桌面应用能够调用可将用户登录的 Web API，请使用 MSAL **PublicClientApplication** 类的交互式令牌获取方法。 使用这些交互方法可以控制登录 UI 体验。 MSAL 使用 Web 浏览器进行这种交互。

![调用 Web API 的桌面应用](media/scenarios/desktop-app.svg)

对于已加入 Windows 域或者由 Azure Active Directory (Azure AD) 管理的计算机上的 Windows 托管应用程序，存在另一种可能的情况。 这些应用程序可以使用 [Windows 集成身份验证](https://aka.ms/msal-net-iwa)以无提示方式获取令牌。

在无浏览器的设备上运行的应用程序仍可代表用户调用 API。 若要进行身份验证，用户必须登录到有 Web 浏览器的另一台设备。 此方案要求使用[设备代码流](https://aka.ms/msal-net-device-code-flow)。

![设备代码流](media/scenarios/device-code-flow-app.svg)

可以在公共客户端应用程序中使用[用户名/密码流](https://aka.ms/msal-net-up)，不过我们并不建议使用。 在某些方案（例如 DevOps）中仍需要此流。

但使用此流会限制你的应用程序。 例如，应用程序无法登录需要使用多重身份验证或条件性访问的用户。 应用程序也无法受益于单一登录。

使用用户名/密码流进行身份验证的方式违反新式身份验证的原则，仅仅是出于遗留原因而提供的。

在桌面应用中，如果希望令牌缓存持久保存，请自定义[令牌缓存序列化](https://aka.ms/msal-net-token-cache-serialization)。 通过实现[双重令牌缓存序列化](https://aka.ms/msal-net-dual-cache-serialization)，可以使用向后兼容的和向前兼容的令牌缓存。 这些令牌支持以前代的身份验证库。 具体的库包括适用于 .NET 的 Azure AD 身份验证库 (ADAL.NET) 版本 3 和 4。

有关详细信息，请参阅[调用 Web API 的桌面应用](scenario-desktop-overview.md)。

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>代表交互式用户调用 Web API 的移动应用

类似于桌面应用，移动应用调用 MSAL **PublicClientApplication** 类的交互式令牌获取方法来获取用于调用 Web API 的令牌。

![调用 Web API 的移动应用](media/scenarios/mobile-app.svg)

MSAL iOS 和 MSAL Android 默认使用系统 Web 浏览器。 但是，你可以指示它们改用嵌入式 Web 视图。 Specificities （UWP）、iOS 或 Android 都依赖于移动通用 Windows 平台平台。

某些方案（如涉及到设备 ID 或设备注册相关的条件性访问的方案）要求在设备上安装代理。 中介的示例包括 Android 上的 Microsoft 公司门户，以及 Android 和 iOS 上的 Microsoft Authenticator。 现在，MSAL 可以与代理交互。 有关详细信息，请参阅[在 Android 和 iOS 上利用代理](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)。

有关详细信息，请参阅[调用 Web API 的移动应用](scenario-mobile-overview.md)。

> [!NOTE]
> 对于使用 MSAL.iOS、MSAL.Android 或 Xamarin 上的 MSAL.NET 的移动应用，可以应用应用保护策略。 例如，策略可以阻止用户复制受保护的文本。 移动应用由 Intune 管理，Intune 将其识别为托管应用。 有关详细信息，请参阅[Microsoft Intune APP SDK 概述](https://docs.microsoft.com/intune/app-sdk)。
>
> [Intune 应用 SDK](https://docs.microsoft.com/intune/app-sdk-get-started) 独立于 MSAL 库，可自行与 Azure AD 交互。

### <a name="a-protected-web-api"></a>受保护的 Web API

可以使用 Microsoft 标识平台终结点来保护 web 服务，例如应用的 RESTful web API。 使用访问令牌调用受保护的 web API。 该令牌可以保护 API 的数据并对传入的请求进行身份验证。 Web API 调用方会在 HTTP 请求的授权标头中追加一个访问令牌。

若要保护 ASP.NET 或 ASP.NET Core Web API，需要验证访问令牌。 可以使用 ASP.NET JWT 中间件进行这种验证。 验证是由[适用于.NET 的 IdentityModel 扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)库而不是 MSAL.NET 完成的。

有关详细信息，请参阅[受保护的 Web API](scenario-protected-web-api-overview.md)。

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>代表用户调用另一个 Web API 的 Web API

要使 ASP.NET 或受 ASP.NET Core 保护的 Web API 能够代表用户调用另一个 Web API，应用需要获取下游 Web API 的令牌。 若要获取令牌，你的应用将调用**ConfidentialClientApplication**类的[AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of)方法。 此类调用也称为*服务到服务*调用。 调用其他 Web API 的 Web API 需要提供自定义缓存序列化。

  ![调用另一个 Web API 的 Web API](media/scenarios/web-api.svg)

有关详细信息，请参阅[调用 Web API 的 Web API](scenario-web-api-call-api-overview.md)。

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>在守护程序名称中调用 Web API 的守护程序应用

包含长时运行进程或无需用户交互即可运行的应用还需要通过某种方式访问安全的 Web API。 此类应用可以使用应用的标识进行身份验证和获取令牌。 应用将使用客户端机密或证书来证明其身份。

可以编写使用 MSAL **ConfidentialClientApplication** 类的[客户端凭据](https://aka.ms/msal-net-client-credentials)获取方法获取调用方应用令牌的应用。 这些方法要求调用方应用已在 Azure AD 中注册了一个机密。 然后，该应用将与被调用守护程序共享机密。 此类机密的示例包括应用程序密码、证书断言或客户端断言。

![由其他应用和 API 调用的守护程序应用](media/scenarios/daemon-app.svg)

有关详细信息，请参阅[调用 Web API 的守护程序应用程序](scenario-daemon-overview.md)。

## <a name="scenarios-and-supported-authentication-flows"></a>方案和受支持的身份验证流

涉及获取令牌的方案也会映射到 OAuth 2.0 身份验证流。 有关详细信息，请参阅[Microsoft 标识平台上的 OAuth 2.0 和 OpenID connect 协议](active-directory-v2-protocols.md)。

<table>
 <thead>
  <tr><th>场景</th> <th>详细方案演练</th> <th>OAuth 2.0 流和授权</th> <th>读者</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">单页应用</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">隐式</a></td>
   <td>工作或学校帐户、个人帐户和 Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">可将用户登录的 Web 应用</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">授权代码</a></td>
   <td>工作或学校帐户、个人帐户和 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">调用 Web API 的 Web 应用</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">授权代码</a></td>
   <td>工作或学校帐户、个人帐户和 Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">调用 Web API 的桌面应用</a></td>
   <td>使用<a href="v2-oauth2-auth-code-flow.md">授权代码</a>和 PKCE 的交互式方法</td>
   <td>工作或学校帐户、个人帐户和 Azure AD B2C</td>
 </tr>

  <tr>
   <td>集成的 Windows 身份验证</td>
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
   <td rowspan="2"><a href="scenario-mobile-overview.md">调用 Web API 的移动应用</a></td>
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
   <td><a href="scenario-web-api-call-api-overview.md">调用 Web API 的 Web API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">代表</a></td>
   <td>工作或学校帐户和个人帐户</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>方案和受支持的平台和语言

Microsoft 身份验证库支持多种平台：

- JavaScript
- .NET Framework
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
> 某些应用程序类型在每个平台上不可用。

在下表的“Windows”列中，每当提到 .NET Core，表示 .NET Framework 也可用。 省略后者是为了避免表格内容混杂。

|场景  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [单页应用](scenario-spa-overview.md) <br/>[![单页应用](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [用于让用户登录的 Web 应用](scenario-web-app-sign-user-overview.md) <br/>[![用于让用户登录的 Web 应用](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [用于调用 Web API 的 Web 应用](scenario-web-app-call-api-overview.md) <br/> <br/>[![调用 Web API 的 Web 应用](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [用于调用 Web API 的 桌面应用](scenario-desktop-overview.md) <br/> <br/>[![用于调用 Web API 的桌面应用](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![设备代码流](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS / Objective C 或 swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [用于调用 Web API 的移动应用](scenario-mobile-overview.md) <br/> [![用于调用 Web API 的移动应用](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Objective C 或 swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [守护程序应用](scenario-daemon-overview.md) <br/> [![守护程序应用](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [用于调用 Web API 的 Web API](scenario-web-api-call-api-overview.md) <br/><br/> [![用于调用 Web API 的 Web API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

有关详细信息，请参阅[Microsoft 支持的库（按 OS/语言](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)）。

## <a name="next-steps"></a>后续步骤

详细了解[身份验证基础知识](authentication-scenarios.md)和[Microsoft 标识平台访问令牌](access-tokens.md)。
