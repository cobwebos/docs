---
title: Azure Active Directory v2.0 身份验证库 | Microsoft Docs
description: Azure Active Directory v2.0 终结点的兼容客户端库和服务器中间件库，以及相关的库、源代码和示例链接。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 1450cffca7a4cfa57856c75cdcc9e106958ea043
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319116"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0 身份验证库

[Azure Active Directory (Azure AD) v2.0 终结点](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare)支持行业标准 OAuth 2.0 和 OpenID Connect 1.0 协议。 Microsoft 身份验证库 (MSAL) 设计为适用于 Azure AD v2.0 终结点。 还可以使用支持 OAuth 2.0 和 OpenID Connect 1.0 的开放源代码库。

建议使用协议领域的专家根据安全开发生命周期 (SDL) 方法（例如 [Microsoft 遵循的方法][Microsoft-SDL]）编写的库。 如果决定手动编写协议支持，请遵循 Microsoft 的 SDL 等方法并认真对待每个协议的标准规范中的安全注意事项。

> [!NOTE]
> 是否在寻找 Azure AD v1.0 库 (ADAL)？ 请查看 [ADAL 库指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)。
>
>

## <a name="types-of-libraries"></a>库的类型

Azure AD v2.0 终结点适用于两种类型的库：

* **客户端库**。 本机客户端和服务器使用客户端库获取用于调用某个资源（例如 Microsoft Graph）的访问令牌。
* **服务器中间件库**。 Web 应用使用服务器中间件库进行用户登录。 Web API 使用服务器中间件库验证本机客户端或其他服务器发送的令牌。

## <a name="library-support"></a>库支持

可以在使用 v2.0 终结点时选择任何符合标准的库，此时必须知道可从何处寻求支持。 有关库代码中的问题和功能请求，请联系库所有者。 有关服务端协议实现中的问题和功能请求，请联系 Microsoft。 有关要在协议中看到的其他功能，请[提出功能请求](https://feedback.azure.com/forums/169401-azure-active-directory)。 如果发现 Azure AD v2.0 终结点与 OAuth 2.0 或 OpenID Connect 1.0 不兼容的问题，请[创建支持请求](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request)。

库的支持类型有两种：

* **Microsoft 支持**。 Microsoft 为这些库提供修补程序，并对这些库进行 SDL 审慎调查。
* **兼容**。 Microsoft 已在基本方案中测试这些库并确认它们适用于 v2.0 终结点。 Microsoft 不提供这些库的修复程序，且尚未审查这些库。 问题和功能请求应重定向到库的开源项目。

有关适用于 V2.0 终结点的库列表，请参阅本文的后续部分。

## <a name="microsoft-supported-client-libraries"></a>Microsoft 支持的客户端库

> [!IMPORTANT]
> MSAL 预览库适用于生产环境。 Microsoft 为这些库提供与当前生产库 (ADAL) 相同的生产级别支持。 在预览期间，预期会更改这些库的 MSAL API、内部缓存格式和其他机制，恕不另行通知，因此将需要使用 bug 修复或功能改进。 这可能会影响应用程序。 例如，对缓存格式的更改可能会要求用户重新登录。 API 更改可能要求更新代码。 正式发布 (GA) 版本变得可用时，使用预览版的库的所有应用程序都必须在六个月内更新，否则可能会停止工作。

| 平台 | 库 | 下载 | 源代码 | 示例 | 引用
| --- | --- | --- | --- | --- | --- |
| .NET 客户端、Windows 应用商店、UWP、Xamarin iOS 和 Android | MSAL .NET（预览） |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [桌面应用](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js（预览） | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [单页应用](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS、macOS | MSAL（预览） | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS 应用](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL（预览） | [中央存储库](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android 应用](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft 支持的服务器中间件库

| 平台 | 库 | 下载 | 源代码 | 示例 | 引用
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | OWIN OpenID Connect 中间件 |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/) |[MVC 应用](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | 适用于 AzureAD 的 OWIN OAuth Bearer 中间件 |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/) |  | |
| .NET 4.x | 适用于 .NET 4.5 的 JWT 处理程序 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | ASP.NET OpenID Connect 中间件 |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.NET 安全性 (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[MVC 应用](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | ASP.NET OAuth Bearer 中间件 |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.NET 安全性 (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | 适用于 .NET Core 的 JWT 处理程序  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web 应用](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)| |

## <a name="compatible-client-libraries"></a>兼容的客户端库

| 平台 | 库名称 | 测试的版本 | 源代码 | 示例 |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[本机应用示例](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[本机应用示例](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [版本 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| PHP | [The PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [版本 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>相关内容
有关 Azure AD v2.0 终结点的详细信息，请参阅 [Azure AD 应用模型 v2.0 概述][AAD-App-Model-V2-Overview]。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
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

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
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
