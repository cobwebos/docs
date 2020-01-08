---
title: Microsoft 标识平台身份验证库 |Microsoft Docs
description: 兼容的客户端库和服务器中间件库，以及相关的库、源代码和示例链接，适用于 Microsoft 标识平台终结点。
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d2b2ea9d878db8284aa44cdbdacb92c8cd4fabe
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424009"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft 标识平台身份验证库

[Microsoft 标识平台终结点](active-directory-v2-compare.md)支持行业标准 OAuth 2.0 和 OpenID connect 1.0 协议。 Microsoft 身份验证库（MSAL）旨在与 Microsoft 标识平台终结点一起使用。 你还可以使用支持 OAuth 2.0 和 OpenID Connect 1.0 的开源库。

建议使用遵循安全开发生命周期（SDL）方法的协议域专家编写的库。 此类方法包括[Microsoft 遵循][Microsoft-SDL]的方法。 如果为协议编写代码，则应遵循 Microsoft SDL 等方法。 请密切注意每个协议的标准规范中的安全注意事项。

> [!NOTE]
> 是否正在查找 Azure Active Directory 身份验证库（ADAL）？ 请查看[ADAL 库指南](active-directory-authentication-libraries.md)。

## <a name="types-of-libraries"></a>库的类型

Microsoft 标识平台终结点适用于两种类型的库：

* **客户端库**：本机客户端和服务器使用客户端库获取用于调用资源（如 Microsoft Graph）的访问令牌。
* **服务器中间件库**：Web 应用使用服务器中间件库进行用户登录。 Web API 使用服务器中间件库验证本机客户端或其他服务器发送的令牌。

## <a name="library-support"></a>库支持

库的支持类型有两种：

* **Microsoft 支持**：Microsoft 为这些库提供修补程序，并对这些库进行 SDL 审慎调查。
* **兼容**： Microsoft 已在基本方案中测试这些库，并已确认它们适用于 Microsoft 标识平台终结点。 Microsoft 不会为这些库提供修补程序，也不会对这些库进行评论。 问题和功能请求应重定向到库的开源项目。

有关使用 Microsoft 标识平台终结点的库列表，请参阅以下各节。

## <a name="microsoft-supported-client-libraries"></a>Microsoft 支持的客户端库

使用客户端身份验证库获取用于调用受保护的 web API 的令牌。

| 平台 | 库 | 下载 | 源代码 | 示例 | 参考 | 概念文档 | 路线图 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [单页应用](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [参考](https://azuread.github.io/microsoft-authentication-library-for-js/docs/msal/) | [概念文档](msal-overview.md)| [路线图](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL 角（预览） | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [桌面应用](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[概念文档](msal-overview.md) | [路线图](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [示例](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [路线图](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [示例](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [参考](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [路线图](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS & macOS | MSAL iOS 和 macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS 应用](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)、 [macOS 应用](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [参考](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [概念文档](msal-overview.md) | |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [中央存储库](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android 应用](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [概念文档](msal-overview.md) |[路线图](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 支持的服务器中间件库

使用中间件库来帮助保护 web 应用程序和 web Api。 使用 ASP.NET 或 ASP.NET Core 编写的 web 应用或 web Api 使用中间件库。

| 平台 | 库 | 下载 | 源代码 | 示例 | 参考
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET 安全性 |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC 应用](quickstart-v2-aspnet-webapp.md) |[ASP.NET API 参考](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| 适用于 .NET 的 IdentityModel 扩展| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC 应用](quickstart-v2-aspnet-webapp.md) |[参考](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web 应用](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Microsoft 支持的库（按 OS/语言）

在支持的操作系统与语言的术语中，映射如下所示：

|             | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET、ASP.NET Core、MSAL.Net （.NET FW、Core、UWP）| ASP.NET Core，MSAL.Net （.NET Core） | ASP.NET Core，MSAL.Net （MacOS）       | MSAL.Net （Xamarin） | MSAL.Net （Xamarin）|
| Swift <br> Objective-C |            |            | [适用于 iOS 和 macOS 的 MSAL](msal-overview.md) | [适用于 iOS 和 macOS 的 MSAL](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j | msal4j | msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passport. 节点 | Passport. 节点 | Passport. 节点 |

另请参阅[按支持的平台和语言的方案](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>兼容的客户端库

| 平台 | 库名称 | 测试的版本 | 源代码 | 示例 |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 版本1.13。5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [版本 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect 库](https://github.com/GluuFederation/oxAuth) | [版本3.0。2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect 库](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [版本 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [版本2.4。5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [The PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [版本 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth：1.3。1<br />omniauth-oauth2：1.4。0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS、macOS、& Android  | [响应本机应用身份验证](https://github.com/FormidableLabs/react-native-app-auth) | [版本4.2。0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [响应本机应用身份验证](https://github.com/FormidableLabs/react-native-app-auth) | |

对于任何符合标准的库，可以使用 Microsoft 标识平台终结点。 请务必了解支持在何处：

* 有关库代码中的问题和新功能请求，请联系库所有者。
* 有关服务端协议实现中的问题和新功能请求，请联系 Microsoft。
* 为需要在协议中查看的其他功能提供[文件请求](https://feedback.azure.com/forums/169401-azure-active-directory)。
* 如果发现 Microsoft 标识平台终结点不符合 OAuth 2.0 或 OpenID Connect 1.0，请[创建支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。

## <a name="related-content"></a>相关内容

有关 Microsoft 标识平台终结点的详细信息，请参阅[microsoft 标识平台概述][AAD-App-Model-V2-Overview]。

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
