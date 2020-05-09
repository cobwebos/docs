---
title: Microsoft 标识平台身份验证库
description: Microsoft 标识平台终结点的兼容客户端库和服务器中间件库，以及相关的库、源代码和示例链接。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9e1480ad58390ea8c424bc6032940b4c743210e3
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982050"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft 标识平台身份验证库

[Microsoft 标识平台终结点](active-directory-v2-compare.md)支持行业标准协议 OAuth 2.0 和 OpenID Connect 1.0。 Microsoft 身份验证库 (MSAL) 设计为适用于 Microsoft 标识平台终结点。 还可以使用支持 OAuth 2.0 和 OpenID Connect 1.0 的开放源代码库。

建议使用协议领域的专家根据安全开发生命周期 (SDL) 方法编写的库。 此类方法包括 [Microsoft 遵循的方法][Microsoft-SDL]。 如果手动编写协议代码，应遵循 Microsoft SDL 之类的方法。 请认真对待每个协议的标准规范中的安全注意事项。

> [!NOTE]
> 想要使用 Azure Active Directory 身份验证库 (ADAL)？ 请查看 [ADAL 库指南](../azuread-dev/active-directory-authentication-libraries.md)。

## <a name="types-of-libraries"></a>库的类型

Microsoft 标识平台终结点使用两种类型的库：

* **客户端库**：本机客户端和服务器使用客户端库获取用于调用某个资源（例如 Microsoft Graph）的访问令牌。
* **服务器中间件库**：Web 应用使用服务器中间件库进行用户登录。 Web API 使用服务器中间件库验证本机客户端或其他服务器发送的令牌。

## <a name="library-support"></a>库支持

库的支持类型有两种：

* **Microsoft 支持**：Microsoft 为这些库提供修补程序，并对这些库进行 SDL 审慎调查。
* **兼容**：Microsoft 已在基本方案中测试这些库并确认它们适用于 Microsoft 标识平台终结点。 Microsoft 不提供这些库的修复程序，且尚未审查这些库。 问题和功能请求应重定向到库的开源项目。

有关适用于 Microsoft 标识平台终结点的库列表，请参阅以下部分。

## <a name="microsoft-supported-client-libraries"></a>Microsoft 支持的客户端库

使用客户端身份验证库获取令牌以调用受保护的 Web API。

| 平台 | 库 | 下载 | 源代码 | 示例 | 参考 | 概念文档 | 路线图 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![Javascript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [单页应用](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [参考](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [概念文档](msal-overview.md)| [路线图](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angular | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [Angular SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [参考](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [概念文档](msal-overview.md) | [路线图](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [桌面应用](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[概念文档](msal-overview.md) | [路线图](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [路线图](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [参考](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [路线图](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS 和 macOS | MSAL iOS 和 macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS 应用](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)、[macOS 应用](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [参考](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [概念文档](msal-overview.md) | |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [中央存储库](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android 应用](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [概念文档](msal-overview.md) |[路线图](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 支持的服务器中间件库

使用中间件库来保护 Web 应用程序和 Web API。 通过 ASP.NET 或 ASP.NET Core 编写的 Web 应用或 Web API 使用中间件库。

| 平台 | 库 | 下载 | 源代码 | 示例 | 参考
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET 安全性 |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC 应用](quickstart-v2-aspnet-webapp.md) |[ASP.NET API 参考](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| 适用于 .NET 的 IdentityModel 扩展| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC 应用](quickstart-v2-aspnet-webapp.md) |[参考](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web 应用](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>按 OS/语言划分的 Microsoft 支持的库

至于支持的操作系统和语言，映射如下：

|             | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![Javascript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET、ASP.NET Core、MSAL.Net（.NET FW、Core、UWP）| ASP.NET Core、MSAL.Net (.NET Core) | ASP.NET Core、MSAL.Net (macOS)       | MSAL.Net (Xamarin.iOS) | MSAL.Net (Xamarin.Android)|
| Swift <br> Objective-C |            |            | [适用于 iOS 和 macOS 的 MSAL](msal-overview.md) | [适用于 iOS 和 macOS 的 MSAL](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.Js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passport.node | Passport.node | Passport.node |

另请参阅[按支持的平台和语言划分的方案](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>兼容的客户端库

| 平台 | 库名称 | 测试的版本 | 源代码 | 示例 |
|:---:|:---:|:---:|:---:|:---:|
|![Javascript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 版本 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
|![Vue](media/sample-v2-code/logo_vue.png)|[Vue MSAL](https://github.com/mvertopoulos/vue-msal) | 版本3.0。3 |[vue-msal](https://github.com/mvertopoulos/vue-msal) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [版本 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect 库](https://github.com/GluuFederation/oxAuth) | [版本 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect 库](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [版本 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [版本 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [The PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [版本 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth：1.3.1<br />omniauth-oauth2：1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS、macOS 和 Android  | [React Native 应用身份验证](https://github.com/FormidableLabs/react-native-app-auth) | [版本 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [React Native 应用身份验证](https://github.com/FormidableLabs/react-native-app-auth) | |

对于任何符合标准的库，都可以使用 Microsoft 标识平台终结点。 了解去哪里寻求支持非常重要：

* 有关库代码中的问题和新功能请求，请联系库所有者。
* 有关服务端协议实现中的问题和新功能请求，请联系 Microsoft。
* 若要在协议中看到其他功能，请[提出功能请求](https://feedback.azure.com/forums/169401-azure-active-directory)。
* 如果发现 Microsoft 标识平台终结点不符合 OAuth 2.0 或 OpenID Connect 1.0，请[创建支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。

## <a name="related-content"></a>相关内容

有关 Microsoft 标识平台终结点的详细信息，请参阅 [Microsoft 标识平台概述][AAD-App-Model-V2-Overview]。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
