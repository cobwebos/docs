---
title: Azure Active Directory 身份验证库 | Microsoft 文档
description: 通过 Azure AD 身份验证库 (ADAL)，客户端应用程序开发人员能够轻松利用云或本地 Active Directory (AD) 对用户进行身份验证，并获取访问令牌，以进行安全的 API 调用。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 33a9be83f7f79989ac09c3e7593a86ecb6c87507
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579184"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory 身份验证库

通过 Azure Active Directory 身份验证库 (ADAL) v1.0，应用程序开发人员可以利用云或本地 Active Directory (AD) 对用户进行身份验证，并获取令牌来保护 API 调用。 ADAL 通过以下功能使开发者更轻松地进行身份验证：

- 存储访问令牌和刷新令牌的可配置令牌缓存
- 当访问令牌过期且刷新令牌可用时，自动刷新令牌
- 支持异步方法调用

> [!NOTE]
> 在找 Azure AD v2.0 库 (MSAL) 吗？ 请参阅 [MSAL 库指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)。
>
>

## <a name="microsoft-supported-client-libraries"></a>Microsoft 支持的客户端库

| 平台 | 库 | 下载 | 源代码 | 示例 | 引用
| --- | --- | --- | --- | --- | --- |
| .NET 客户端、Windows 应用商店、UWP、Xamarin iOS 和 Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [桌面应用](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[参考](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| .NET 客户端、Windows 应用商店、Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [桌面应用](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[单页应用](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS、macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS 应用](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [参考](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[中央存储库](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android 应用](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.js Web 应用](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[参考](https://docs.microsoft.com/en-us/javascript/api/adal-node/?view=azure-node-latest) |
| Java |ADAL4J |[Maven](http://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java Web 应用](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[参考](http://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python Web 应用](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[参考](http://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Microsoft 支持的服务器库

| 平台 | 库 | 下载 | 源代码 | 示例 | 引用
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN for AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC 应用](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN for OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Web 应用](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |适用于 WS 联合身份验证的 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC Web 应用](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |适用于 .NET 4.5 的标识协议扩展 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |适用于 .NET 4.5 的 JWT 处理程序 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>方案

以下是在访问远程资源的客户端中使用 ADAL 的三种常见方案：

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>对设备上运行的本机客户端应用程序的用户进行身份验证

在此方案中，开发人员有一个移动客户端或桌面应用程序需要访问远程资源（如 Web API）。 该 Web API 不允许匿名调用，并且必须在经过身份验证的用户的上下文中调用。 该 Web API 已预先配置为信任由特定 Azure AD 租户颁发的访问令牌。 Azure AD 已预先配置为颁发该资源的访问令牌。 若要从客户端调用 Web API，开发人员可使用 ADAL 来简化 Azure AD 的身份验证。 使用 ADAL 最安全的方法是使其呈现用于收集用户凭据的用户界面（呈现为浏览器窗口）。

使用 ADAL 可以轻松地对用户进行身份验证，从 Azure AD 获取访问令牌和刷新令牌，然后使用访问令牌调用 Web API。

有关使用 Azure AD 身份验证演示此方案的代码示例，请参阅[本机客户端 WPF 应用程序到 Web API](https://github.com/azureadsamples/nativeclient-dotnet)。

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>对 Web 服务器上运行的机密客户端应用程序进行身份验证

在此方案中，开发人员在服务器上有一个正在运行的应用程序需要访问远程资源（如 Web API）。 该 Web API 不允许匿名调用，因此必须从授权服务中调用它。 该 Web API 已预先配置为信任由特定 Azure AD 租户颁发的访问令牌。 Azure AD 已预先配置为向具有客户端凭据（客户端 ID 和密码）的服务颁发该资源的访问令牌。 ADAL 可简化 Azure AD 对服务的身份验证，并返回可用于调用该 Web API 的访问令牌。 ADAL 还通过缓存访问令牌并在必要时续订它，来处理对访问令牌生存期的管理。 有关演示此方案的代码示例，请参阅[守护程序控制台应用程序到 Web API](https://github.com/AzureADSamples/Daemon-DotNet)。

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>代表用户对服务器上运行的机密客户端应用程序进行身份验证

在此方案中，开发人员在服务器上有一个正在运行的 Web 应用程序需要访问远程资源（如 Web API）。 该 Web API 不允许匿名调用，因此必须以经过身份验证的用户身份从授权服务中调用它。 该 Web API 已预先配置为信任由特定 Azure AD 租户颁发的访问令牌，而 Azure AD 已预先配置为向具有客户端凭据的服务颁发该资源的访问令牌。 用户在 Web 应用程序中进行身份验证后，应用程序可以从 Azure AD 获取该用户的授权代码。 然后，Web 应用程序可以使用该授权代码以及与应用程序关联的客户端凭据，代表用户通过 ADAL 从 Azure AD 中获取访问令牌和刷新令牌。 Web 应用程序拥有访问令牌后，就可以调用 Web API，直到该令牌过期。 令牌过期后，Web 应用程序可以使用前面收到的刷新令牌，通过 ADAL 获取新的访问令牌。 有关演示此方案的代码示例，请参阅[本机客户端到 Web API 到 Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)。

## <a name="see-also"></a>另请参阅

- [Azure Active Directory 开发人员指南](azure-ad-developers-guide.md)
- [Azure Active directory 的身份验证方案](authentication-scenarios.md)
- [Azure Active Directory 代码示例](sample-v1-code.md)
